# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The solution build produced no errors across all five projects:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

This indicates the transformation to cross-platform .NET was successful. The following steps outline how to validate, test, and deploy the solution.

---

## 1. Restore and Build the Solution

Run the following commands from the root of the solution to confirm a clean restore and build:

```bash
dotnet restore
dotnet build --configuration Release
```

Ensure there are no warnings that could indicate compatibility issues, deprecated APIs, or missing references.

---

## 2. Run the Unit Tests

Execute the test project to verify that existing domain logic behaves as expected after the transformation:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for:
- Any failed or skipped tests
- Exceptions that may point to runtime incompatibilities
- Any tests that were passing before the transformation but are now failing

---

## 3. Verify Data Layer Functionality

Since `Bookstore.Data` handles data access, confirm the following:

- **Database provider compatibility**: Ensure the database provider (e.g., Entity Framework Core, Dapper) targets a version compatible with the current .NET SDK.
- **Migrations**: If using Entity Framework Core, verify that existing migrations are intact and apply cleanly:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj
```

- **Connection strings**: Confirm that connection strings in configuration files (`appsettings.json`, environment variables) are correct for the target environment.

---

## 4. Run and Validate the Web Application

Start the web application locally to confirm it runs correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:
- All pages load without errors
- Data is read from and written to the database correctly
- Any authentication or authorization flows work as expected
- Static assets (CSS, JavaScript, images) are served correctly

Check the application logs for any runtime exceptions or warnings.

---

## 5. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Review it to confirm:

- All AWS CDK or infrastructure library references are compatible with the current .NET version
- Environment-specific configuration (regions, resource names, IAM roles) is accurate for the target deployment environment
- Run a synthesis step if using AWS CDK to validate the infrastructure definition:

```bash
dotnet run --project app/Bookstore.Cdk/Bookstore.Cdk.csproj
```

Or, if using the CDK CLI:

```bash
cdk synth
```

Review the synthesized output for correctness before deploying any infrastructure changes.

---

## 6. Cross-Platform Validation

Since the goal is cross-platform compatibility, if the solution was previously developed on Windows, validate it on Linux or macOS:

- Confirm file path separators are handled using `Path.Combine` rather than hardcoded backslashes
- Verify that any file I/O operations work correctly on the target OS
- Ensure no Windows-specific APIs or libraries (e.g., `Microsoft.Win32`, COM interop) remain in use

---

## 7. Check Target Framework and Dependency Versions

Open each `.csproj` file and confirm:

- The `<TargetFramework>` element targets the intended .NET version (e.g., `net8.0`)
- All NuGet package versions are current and do not reference packages that are end-of-life or incompatible with the target framework
- No packages reference `netstandard2.0` or `net4x` frameworks exclusively, which could indicate incomplete migration

---

## 8. Publish the Application

Once validation is complete, publish the web application:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Review the output directory to confirm all required files are present, including:
- The application binary
- `appsettings.json` and any environment-specific configuration files
- Static web assets

Deploy the contents of the `./publish` directory to your target hosting environment.