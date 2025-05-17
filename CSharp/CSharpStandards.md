# Coding Standards for C#

This document outlines the coding standards for all my C# projects. Adhering to these standards ensures consistent, readable, and maintainable code. All contributors must follow these guidelines when submitting pull requests.

Older projects may not follow all these standards. If making a change to an existing project, you can (at your discretion) fix the classes you changed to follow these standards. However, it is not required.

Do not change a class if the only change to it is only to bring it up the the current standards.

## 1. General Principles

- **Clarity and Readability**: Write code that is easy to understand and maintain. Favor explicitness over cleverness.
- **Consistency**: Follow the conventions in this document and match the style of existing code in the project.
- **Simplicity**: Avoid unnecessary complexity. Use the simplest solution that meets requirements.
- **Performance**: Optimize only when necessary, with profiling to justify changes.

## 2. Naming Conventions

- **General**:
  - Use PascalCase for public types, methods, properties, and events (e.g., `PublicClass`, `CalculateTotal`).
  - Use camelCase for local variables and parameters (e.g., `inputValue`).
  - Avoid abbreviations unless widely understood (e.g., `Http` is acceptable, but `CalcVal` is not).
- **Class-Level Variables**:
  - **Static Variables**: Prefix static fields and properties with `s_` to indicate potential locking needs for thread safety (e.g., `private static int s_counter`, `public static string s_sharedConfig`). Document locking requirements in XML comments.
  - **Non-Static Private Variables**: Prefix non-static private fields with `_` (e.g., `private int _counter`, `private string _userName`).
- **Specific Rules**:
  - Classes and interfaces: Use nouns or noun phrases (e.g., `CustomerService`, `IRepository`).
  - Methods: Use verbs or verb phrases (e.g., `GetCustomer`, `SaveChanges`).
  - Properties: Use descriptive names (e.g., `IsActive`, `CustomerName`).
  - Constants: Use all upper-case with underscores for descriptive names (e.g., `MAXIMUM_USER_COUNT`, `DEFAULT_TIMEOUT_SECONDS`).
  - Avoid underscores except for `s_`, `_`, and constant names; do not use Hungarian notation (e.g., avoid `strName`).

## 3. Code Organization

- **File Structure**:
  - One public type per file, with the file name matching the type (e.g., `CustomerService.cs` for `CustomerService` class).
    - Small helper classes that are only used internally to another class can be contained within the file for the class that uses it. For example, if you want a function to return multiple values and do not want to use a tuple.
  - Use partial classes only for generated code (e.g., designer files).
- **Namespaces**:
  - Use a consistent namespace hierarchy, e.g., `[YourCompany].[Project].[Module]` (e.g., `MyOrg.Billing.Services`).
  - Avoid overly deep namespaces (aim for 3-4 levels).
- **Class Structure**:
  - Order members: constants, static fields, instance fields, constructors, properties, methods, events.
  - Group related members together logically.
- **Regions**:
  - Avoid `#region` directives, as they can obscure code and are unnecessary in compact classes.
      - **NOTE:** Many existing projects contain regions. Only remove them if you are making other changes to the file.
  - Use regions only for:
    - Generated code (e.g., designer files).
    - Large classes (which you should generally try to avoid) where grouping improves clarity, e.g., separating interface implementations.
  - If used, name regions descriptively (e.g., `// Interface Implementations`).

## 4. Coding Practices

- **Null Safety**:
  - Enable nullable reference types (`<Nullable>enable</Nullable>` in `.csproj`) and use `?` for nullable types.
  - Validate parameters for null, throwing `ArgumentNullException` (e.g., `if (param == null) throw new ArgumentNullException(nameof(param));`).
- **Thread Safety**:
  - For static variables prefixed with `s_`, document and implement locking if accessed across threads (e.g., `lock (s_lockObject) { s_counter++; }`).
  - Prefer thread-safe alternatives like `ConcurrentDictionary` when applicable.
- **Using Directives**:
  - Place `using` statements outside the namespace declaration.
  - Remove unused imports.
  - Prefer explicit type names over aliases (e.g., `List<T>` over `var` for clarity in complex cases).
- **Namespaces**
  - Use file-scoped namespaces in .NET 6.0 and later
- **Modifiers**:
  - Explicitly declare access modifiers (e.g., `private`, `public`) even when default applies.
  - Use `sealed` for classes not designed for inheritance.
  - Mark methods as `virtual` or `abstract` only when necessary.
- **Using code blocks**:
  - When using a "using" block around code that instantiates and uses an IDisposable object, (like a SqlConnection object) prefer to *not* use curly-braces around the following code block, unless the function is large and the braces improve clarity.
- **Dependencies**:
  - Use only Microsoft-provided NuGet packages (e.g., `Microsoft.Extensions.*`, `System.Text.Json`).
  - Avoid third-party libraries unless approved by maintainers.

## 5. Error Handling

- **Exceptions**:
  - Throw specific exceptions (e.g., `InvalidOperationException`, `ArgumentException`) rather than generic `Exception`.
  - Include meaningful messages (e.g., `throw new ArgumentException("Value must be positive", nameof(value));`).
  - Avoid catching exceptions unless you can handle them meaningfully.
- **Validation**:
  - Validate inputs early and fail fast.
  - Use guard clauses/early exits to reduce nesting (e.g., `if (!condition) throw ...;` at method start).
- **Logging**:
  - Log errors with context but avoid sensitive data.

## 6. Testing

- **Unit Tests**:
  - Write unit tests for new or modified code using MSTest or xUnit (match the project’s framework).
  - Cover happy paths, edge cases, and error conditions.
  - Name tests descriptively, e.g., `CalculateTotal_WithNegativeInput_ThrowsArgumentException`.
- **Test Coverage**:
  - Aim for 80%+ code coverage, measured via tools like Coverlet.
  - If the project lacks tests, discuss with maintainers in the issue.
- **Test Organization**:
  - Place tests in a separate project (e.g., `Tests.[ProjectNameBeingTested]`).
  - Mirror the source project’s namespace structure.

## 7. Documentation

- **XML Comments**:
  - Provide XML documentation (`///`) for all public and protected members.
  - Include `<summary>`, `<param>`, `<returns>`, and `<exception>` tags where applicable.
  - Example:
    ```csharp
    /// <summary>
    /// Calculates the total price for an order.
    /// </summary>
    /// <param name="orderId">The unique identifier of the order.</param>
    /// <returns>The total price as a decimal.</returns>
    /// <exception cref="ArgumentException">Thrown when orderId is invalid.</exception>
    public decimal CalculateTotal(int orderId) { ... }
    ```
- **Code Comments**:
  - Use inline comments (`//`) sparingly, only for complex logic.
  - Avoid redundant comments (e.g., `// Increment counter` for `counter++`).
- **README and Docs**:
  - Update README or documentation for new features or changes.
  - Ensure examples are accurate and tested.

## 8. Performance and Optimization

- **String Handling**:
  - Use `StringBuilder` for heavy concatenation.
  - Prefer `ReadOnlySpan<char>` or `string.Concat` for performance-critical operations.
- **Collections**:
  - Choose appropriate collection types (e.g., `List<T>` vs. `Dictionary<TKey, TValue>`).
  - Pre-size collections when possible.
- **LINQ**:
  - Prefer chaining LINQ functions, versus the "reverse SQL" style.
  - Avoid chaining in performance-critical paths unless profiled.
  - Consider `AsSpan` for string operations in LINQ.
- **Profiling**:
  - Profile before optimizing using Visual Studio’s Performance Profiler.
  - Document performance improvements in PRs.

## 9. Tooling

- **IDE**: Use Visual Studio 2022 or later for formatting and nullable support.
- **Analyzers**:
  - Enable default Roslyn analyzers and consider Roslynator.
  - Configure `.editorconfig` to enforce style rules.
  - Example `.editorconfig`:
    ```ini
    [*.cs]
    indent_style = space
    indent_size = 4
