# Csag.Extensions.Configuration.GoogleSecrets.AspNetCore

Adds the [Csag.Extensions.Configuration.GoogleSecrets](https://www.nuget.org/packages/Csag.Extensions.Configuration.GoogleSecrets) configuration provider to an ASP.NET Core `WebApplicationBuilder`, so secrets from Google Cloud Secret Manager become part of the application's configuration.

## Installation

```bash
dotnet add package Csag.Extensions.Configuration.GoogleSecrets.AspNetCore
```

## Usage

```csharp
using Csag.Extensions.Configuration.GoogleSecrets.AspNetCore;

var builder = WebApplication.CreateBuilder(args);

// Reads the project from the environment variable GOOGLE_SECRETS_PROJECT.
builder.AddGoogleSecrets();

// Or with options:
builder.AddGoogleSecrets(options =>
{
    options.ProjectName = "your-project-id";
});
```

The [repository README](https://github.com/collana-solutions/Csag.Extensions.Configuration.GoogleSecrets#readme) describes all options and the authentication.
