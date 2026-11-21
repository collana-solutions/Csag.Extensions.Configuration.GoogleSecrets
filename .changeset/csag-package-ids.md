---
"@collana-solutions/csag-extensions-configuration-googlesecrets": major
"@collana-solutions/csag-extensions-configuration-googlesecrets-aspnetcore": major
---

The packages move to new IDs and namespaces. The API and the behavior are the same as in 1.6.0.

**New package IDs:**

| Up to 1.6.0 | From 2.0.0 |
| --- | --- |
| `Neolution.Extensions.Configuration.GoogleSecrets` | `Csag.Extensions.Configuration.GoogleSecrets` |
| `Neolution.Extensions.Configuration.GoogleSecrets.AspNetCore` | `Csag.Extensions.Configuration.GoogleSecrets.AspNetCore` |

**To migrate:**

1. Replace each `Neolution.Extensions.Configuration.GoogleSecrets*` package reference with the `Csag.Extensions.Configuration.GoogleSecrets*` package of the same name, at 2.0.0 or later.
2. Change the namespaces: `using Neolution.Extensions.Configuration.GoogleSecrets;` becomes `using Csag.Extensions.Configuration.GoogleSecrets;`, and `using Neolution.Extensions.Configuration.GoogleSecrets.AspNetCore;` becomes `using Csag.Extensions.Configuration.GoogleSecrets.AspNetCore;`. The assemblies are renamed the same way.

The configuration does not change: the environment variable `GOOGLE_SECRETS_PROJECT`, the `{GoogleSecret:SecretName:Version}` placeholders and the default mapping of `__` in secret IDs to `:` work as before. The console log messages of the provider now carry the category `Csag.Extensions.Configuration.GoogleSecrets.GoogleSecretsProvider`, so a log query or alert on the old category needs the new name.

The packages now declare the MIT license and come with a README, the collana solutions icon, Source Link and a symbol package.

The `Neolution.Extensions.Configuration.GoogleSecrets*` IDs get deprecated on nuget.org, with the `Csag.Extensions.Configuration.GoogleSecrets*` packages as the alternates. Their existing versions stay available, but they get no new releases.
