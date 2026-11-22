# @collana-solutions/csag-extensions-configuration-googlesecrets

## 2.0.0

### Major Changes

- [#29](https://github.com/collana-solutions/Csag.Extensions.Configuration.GoogleSecrets/pull/29) [`d37fba5`](https://github.com/collana-solutions/Csag.Extensions.Configuration.GoogleSecrets/commit/d37fba51f9bde357f91c0df6c38c79a8b03a8fae) Thanks [@neotrow](https://github.com/neotrow)! - The packages move to new IDs and namespaces. The API and the behavior are the same as in 1.6.0.
  
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

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
and adheres to a project-specific [Versioning](/README.md).

## [1.6.0] - 2025-04-24

### Added

- Added a log message to indicate which project is being used for Google Secrets. This is useful for debugging and understanding which project is being used when multiple projects are available.

## [1.5.0] - 2025-04-15

### Added

- Added new overload for `AddGoogleSecrets` to allow passing `GoogleSecretsOptions`.

## [1.4.0] - 2025-03-18

### Added

- Convention to load Google Secrets project name from environment variable `GOOGLE_SECRETS_PROJECT` if not specified in the options.
- New package `Neolution.Extensions.Configuration.GoogleSecrets.AspNetCore` to reference in ASP.NET Core projects.

### Changed

- Update GitHub Actions to use the latest versions of the actions

## [1.3.1] - 2024-03-12

### Fixed

- Fixed an issue where the `GoogleSecret:SecretName:SecretVersion` syntax was not working as expected. Instead of using a starts with comparison, the code now uses a regular expression to match the secret name and version.
- Fixed an issue where the `GoogleSecret:SecretName:SecretVersion` syntax was not working as expected. The version was parsed including the `}` character. The code now removes the `}` character from the version before calling the google api.

## [1.3.0] - 2024-03-12

### Added

Added support for the `{GoogleSecret:SecretName:SecretVersion}` syntax to override the default values in the `appsettings.json` or `appsettings.{Environment}.json` files.

## [1.2.0] - 2023-06-29

### Changed

- Updated the Google.Cloud.SecretManager.V1 to the latest version to support workload identity federation

## [1.1.9] - 2023-06-29

### Added

- Changelog

[1.6.0]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.5.0...1.6.0
[1.5.0]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.4.0...1.5.0
[1.4.0]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.3.1...1.4.0
[1.3.1]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.3.0...1.3.1
[1.3.0]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.2.0...1.3.0
[1.2.0]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.1.9...1.2.0
[1.1.9]: https://github.com/neolution-ch/Neolution.Extensions.Configuration.GoogleSecrets/compare/1.1.7...1.1.9
