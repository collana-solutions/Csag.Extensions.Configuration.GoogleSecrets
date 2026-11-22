# Csag.Extensions.Configuration.GoogleSecrets

A configuration provider that loads secrets from [Google Cloud Secret Manager](https://cloud.google.com/secret-manager) into `Microsoft.Extensions.Configuration`.

## Installation

```bash
dotnet add package Csag.Extensions.Configuration.GoogleSecrets
```

## Usage

```csharp
using Csag.Extensions.Configuration.GoogleSecrets;

Host.CreateDefaultBuilder(args)
    .ConfigureAppConfiguration((_, configuration) =>
    {
        configuration.AddGoogleSecrets(options =>
        {
            options.ProjectName = "your-project-id";
        });
    });
```

Without `ProjectName`, the provider reads the project from the environment variable `GOOGLE_SECRETS_PROJECT`. By default every secret becomes a configuration key, with `__` in the secret ID mapped to `:`. A configuration value such as `{GoogleSecret:MySecret}` or `{GoogleSecret:MySecret:2}` is replaced by that secret.

For ASP.NET Core, `Csag.Extensions.Configuration.GoogleSecrets.AspNetCore` adds `AddGoogleSecrets` to the `WebApplicationBuilder`.

The [repository README](https://github.com/collana-solutions/Csag.Extensions.Configuration.GoogleSecrets#readme) describes all options and the authentication.
