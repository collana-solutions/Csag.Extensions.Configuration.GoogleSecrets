# Csag.Extensions.Configuration.GoogleSecrets

This package is a configuration provider that injects google secrets into the `Microsoft.Extensions.Configuration`.

| Package | Description |
| ------- | ----------- |
| `Csag.Extensions.Configuration.GoogleSecrets` | The configuration provider and the `AddGoogleSecrets` extension on `IConfigurationBuilder`. Targets .NET Standard 2.1. |
| `Csag.Extensions.Configuration.GoogleSecrets.AspNetCore` | The `AddGoogleSecrets` extension on the ASP.NET Core `WebApplicationBuilder`. Targets .NET 8. |

Up to version 1.6.0 the packages were published as `Neolution.Extensions.Configuration.GoogleSecrets` and `Neolution.Extensions.Configuration.GoogleSecrets.AspNetCore`, with the `Neolution.Extensions.Configuration.GoogleSecrets` namespace. See the [changelog](Csag.Extensions.Configuration.GoogleSecrets/CHANGELOG.md) for the migration.

It is not recommended to store secrets like connection strings in the source code directly or in environment variables. The better approach is to use a secret provider like the [secret manager](https://cloud.google.com/secret-manager) for the google cloud eco system.

## Usage

Install the package using your favourite method, for example:
`dotnet add package Csag.Extensions.Configuration.GoogleSecrets`

Add the custom configuration provider to your `IHostBuilder `:

```c#
public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        ...
        .ConfigureAppConfiguration((_, configuration) =>
        {
            configuration.AddGoogleSecrets(options =>
            {
                options.ProjectName = "your-project-id";
            });
        });
}
```

If `ProjectName` is not set, the provider reads the project from the environment variable `GOOGLE_SECRETS_PROJECT`. Without a project it logs a warning and loads no secrets.

### ASP.NET Core

For an ASP.NET Core application, install the ASP.NET Core package:
`dotnet add package Csag.Extensions.Configuration.GoogleSecrets.AspNetCore`

Then add the provider to the `WebApplicationBuilder`:

```c#
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

## Options

| Name              | Required           | Description                                                                                                                                                                                                                                      | Default                                         |
| ----------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------- |
| ProjectName       | :heavy_check_mark: | Specifies which project the secrets are read from.                                                                                                                                                                                               | `null`                                          |
| Filter            | ❌                 | A server side filter forwarded to the google api according to https://cloud.google.com/secret-manager/docs/filtering                                                                                                                             | `null`                                          |
| FilterFn          | ❌                 | A filter function that will be run after the secrets are read from google (client side filtering). Supplies the `Google.Cloud.SecretManager.V1.Secret` as parameter. If returned true the secret will be processed otherwise it will be ignored. | `secret => true`                                |
| MapFn             | ❌                 | A map function that will be run after the secrets have been filtered                                                                                                                                                                             | `secret.SecretName.SecretId.Replace("__", ":")` |
| VersionDictionary | ❌                 | By default the latest version of each secret is taken. But you can specify a specific version in the form of a dictionary. Key is the secret id and value is the version to take. For example: `{"MySecretId": "2"}`                             | `null`                                          |

## Overriding default appsettings.json values

To override the default `appsettings.json` values you have to options which you can use together or separately:

### Use the `MapFn` to map the secret to the correct path

You can override existing `appsettings.json` values by making sure the `MapFn` maps it to the correct path.
For example if we have the following `appsettings.json`:

```json
{
  "Secrets": {
    "MyGoogleSecret": "santa isn't real"
  }
}
```

We can override it by making sure that the `MapFn` maps it to `Secrets:MyGoogleSecret`. With the default `MapFn` the google secret would have to be called `Secrets__MyGoogleSecret`.

### Use the {GoogleSecret:SecretName:SecretVersion} syntax

In the `appsettings.json` you can use the `{GoogleSecret:SecretName:SecretVersion}` syntax to override the default values. For example:

```json
{
  "Secrets": {
    "MyGoogleSecret": "{GoogleSecret:MyGoogleSecret:latest}"
  }
}
```

will override the `MyGoogleSecret` with the latest version of the secret `MyGoogleSecret`.

**Note: The Version is optional and can be omitted. If omitted the latest version will be taken.**

## Authentication

### Inside GCP

Authorization is done automatically when run inside google cloud.

### Outside GCP

To test it outside of google cloud you need to either set an environment variable called `GOOGLE_APPLICATION_CREDENTIALS` or use the `gcloud auth application-default login` command from the google cloud sdk.

#### Environment variable

Set the environment variable `GOOGLE_APPLICATION_CREDENTIALS` to the json file with your credentials. More details can be found here: https://cloud.google.com/docs/authentication/getting-started#setting_the_environment_variable. You can also set the environment variable via code, for example:

```c#
Environment.SetEnvironmentVariable("GOOGLE_APPLICATION_CREDENTIALS", "C:\\temp\\your-file.json");
```

#### gcloud auth application-default login

Simply run the command `gcloud auth application-default login` (after you have run `gcloud auth login`). This will set up the application default credentials using your personal account.

## Known issues:

### Running in Alpine Linux docker container fails with "Grpc.Core.Internal.UnmanagedLibrary Attempting to load native library "/app/libgrpc_csharp_ext.x64.so"

If you run this nuget inside an alpine docker container you might get an error message similar to this:

```
System.IO.IOException: Error loading native library "/app/runtimes/linux-x64/native/libgrpc_csharp_ext.x64.so". Error loading shared library ld-linux-x86-64.so.2: No such file or directory (needed by /app/runtimes/linux-x64/native/libgrpc_csharp_ext.x64.so)
         at Grpc.Core.Internal.UnmanagedLibrary..ctor(String[] libraryPathAlternatives)
         at Grpc.Core.Internal.NativeExtension.LoadNativeMethodsUsingExplicitLoad()
         at Grpc.Core.Internal.NativeExtension.LoadNativeMethods()
         at Grpc.Core.Internal.NativeExtension..ctor()
         at Grpc.Core.Internal.NativeExtension.Get()
         at Grpc.Core.Internal.NativeMethods.Get()
         at Grpc.Core.GrpcEnvironment.GrpcNativeInit()
         at Grpc.Core.GrpcEnvironment..ctor()
         at Grpc.Core.GrpcEnvironment.AddRef()
         at Grpc.Core.Channel..ctor(String target, ChannelCredentials credentials, IEnumerable`1 options)
         at Google.Api.Gax.Grpc.GrpcCore.GrpcCoreAdapter.CreateChannelImpl(String endpoint, ChannelCredentials credentials, GrpcChannelOptions options)
         at Google.Api.Gax.Grpc.GrpcAdapter.CreateChannel(String endpoint, ChannelCredentials credentials, GrpcChannelOptions options)
         at Google.Api.Gax.Grpc.ChannelPool.GetChannel(GrpcAdapter grpcAdapter, String endpoint, GrpcChannelOptions channelOptions, ChannelCredentials credentials)
         at Google.Api.Gax.Grpc.ChannelPool.GetChannel(GrpcAdapter grpcAdapter, String endpoint, GrpcChannelOptions channelOptions)
         at Google.Api.Gax.Grpc.ClientBuilderBase`1.CreateCallInvoker()
         at Google.Cloud.SecretManager.V1.SecretManagerServiceClientBuilder.BuildImpl()
         at Csag.Extensions.Configuration.GoogleSecrets.GoogleSecretsProvider.Load()
```

To fix it you need to add the following to your `Dockerfile` to manaully build the missing library:

```
# we need temporarly this library untill the issue is solved
# https://github.com/grpc/grpc/issues/21446#issuecomment-807990690
# can be removed once Google.Cloud.SecretManager.V1 does not relay on GRCP, there is a fix at the end as well
FROM alpine:3.13 AS grpc-build
WORKDIR /opt
RUN apk add --update alpine-sdk autoconf libtool linux-headers cmake git && \
    \
    git clone -b v1.36.4 https://github.com/grpc/grpc --depth 1 --shallow-submodules && \
    cd grpc && git submodule update --init --depth 1 && \
    \
    mkdir -p cmake/build && cd cmake/build && \
    \
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo \
      -DgRPC_BACKWARDS_COMPATIBILITY_MODE=ON \
      -DgRPC_BUILD_TESTS=OFF \
      ../.. && \
    \
    make grpc_csharp_ext -j4 && \
    \
    mkdir -p /out && cp libgrpc_csharp_ext.* /out
```

And than at the end of your `Dockerfile` copy the outputs from the `grpc-build` stage:

```
# copy the grpc library required
COPY --from=grpc-build /out/libgrpc_csharp_ext.so /app/libgrpc_csharp_ext.x64.so
```

To speed things up you can push the `grpc-build` stage to a docker registry and pull it from there:

```
COPY --from=docker.io/your-grpc-build-image /out/libgrpc_csharp_ext.so /app/libgrpc_csharp_ext.x64.so
```

ref: https://github.com/grpc/grpc/issues/21446

## Build

Prerequisites:

- The .NET SDK pinned in [global.json](global.json) (10.0.4xx, a newer 10.0 SDK rolls forward).
- Node.js 24 with Corepack, for the changesets tooling: run `corepack enable` and then `pnpm install` once in the repository root.

```bash
dotnet restore --locked-mode
dotnet build --configuration Release --no-restore
dotnet pack --configuration Release --no-build -o ./nupkgs
```

Every project has a `packages.lock.json`, and CI restores in locked mode. After changing a package reference, run `dotnet restore` without `--locked-mode` and commit the updated lock files.

## Preview packages

Each pull request to `main` or to a `release/**` branch publishes both packages to a public preview feed on feedz.io. A draft PR, a PR from a fork and a PR from Dependabot publish no preview. The version is one patch above the current release, for example `2.0.1-pr.42.183` (PR 42, workflow run 183). The **"chore: version packages"** PR publishes a release candidate of the upcoming version instead, for example `2.1.0-pr.73.190`, so the changes of the next release can be tested before it ships. In prerelease mode the version in `package.json` is a prerelease such as `3.0.0-rc.0`, and the preview extends it, for example `3.0.0-rc.0.pr.42.183`. That version sorts after the prerelease and before the next one. A comment on the PR gives the version of its latest push. The feed removes previews after 30 days. Releases go to nuget.org only.

To use a preview in an application, add the feed to the `nuget.config` of the application. The feed needs no credentials:

```xml
<packageSources>
  <clear />
  <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
  <add key="csag-extensions-configuration-googlesecrets-previews" value="https://f.feedz.io/collana-solutions/csag-extensions-configuration-googlesecrets/nuget/index.json" />
</packageSources>
<packageSourceMapping>
  <packageSource key="nuget.org">
    <package pattern="*" />
    <package pattern="Csag.Extensions.Configuration.GoogleSecrets*" />
  </packageSource>
  <packageSource key="csag-extensions-configuration-googlesecrets-previews">
    <package pattern="Csag.Extensions.Configuration.GoogleSecrets*" />
  </packageSource>
</packageSourceMapping>
```

The `Csag.Extensions.Configuration.GoogleSecrets*` pattern is also necessary under nuget.org. NuGet looks for a package only in the sources of its most specific pattern, so without it the released versions do not restore.

To test a preview:

1. Set every `Csag.Extensions.Configuration.GoogleSecrets*` package to the preview version.
2. If the application restores in locked mode, update its `packages.lock.json` files.
3. Add the feed only on the branch that tests the preview. The main branch of the application then does not need feedz.io to restore.

## Release process

This repository follows the [collana-solutions release playbook](https://github.com/collana-solutions/release-playbook) (Changesets, NuGet variant):

1. Every PR that changes a package needs a changeset. Run `pnpm changeset`, pick the packages the change affects, choose the bump type and describe the change for consumers of the packages. The two packages are a fixed group, so they always share one version. A change that ships nothing, such as CI or repository documentation, needs an empty changeset: `pnpm changeset --empty`. CI's **Changeset Check** fails a PR that changes files in one of the two project folders without a changeset. It does not see changes outside them, so a change to `Directory.Build.props` or another shared file that affects the packages needs its changeset added by hand.
2. On merge to `main`, the Release workflow keeps a **"chore: version packages"** PR up to date. It bumps the versions in the `package.json` files, writes them into the `.csproj` files with `scripts/sync-versions.mjs` and updates the `CHANGELOG.md` files.
3. Merging that PR creates the git tags and the GitHub Releases. The release of `Csag.Extensions.Configuration.GoogleSecrets` starts the **NuGet Publish** workflow, which packs both packages and pushes them to nuget.org through trusted publishing. To publish an existing release again, for example after a failed push, run **NuGet Publish** from the Actions tab with that release tag.

Dependabot PRs get their changesets automatically. Do not edit the `CHANGELOG.md` files or the `<Version>` in the `.csproj` files by hand.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
