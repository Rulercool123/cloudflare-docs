---
pcx_content_type: how-to
title: Blazor
---

# Blazor

[Blazor](https://blazor.net) is an SPA framework that can use C# code, rather than JavaScript in the browser. In this guide, you will build a site using Blazor, and deploy it using Cloudflare Pages.

## Install .NET

Blazor uses C#. You will need the latest version of the [.NET SDK](https://dotnet.microsoft.com/download) to continue creating a Blazor project. If you don't have the SDK installed on your system please download and run the installer.

## Creating a new Blazor WASM project

There are two types of Blazor hosting models: [Blazor Server](https://learn.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-8.0#blazor-server) which requires a server to serve the Blazor application to the end user, and [Blazor WebAssembly](https://learn.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-8.0#blazor-webassembly) which runs in the browser. Blazor Server is incompatible with the Cloudflare edge network model, thus this guide only use Blazor WebAssembly.

Create a new Blazor WebAssembly (WASM) application by running the following command:

```sh
$ dotnet new blazorwasm -o my-blazor-project
```

## Create the build script

To deploy, Cloudflare Pages will need a way to build the Blazor project. In the project's directory root, create a `build.sh` file. Populate the file with this (updating the `.dotnet-install.sh` line appropriately if you're not using the latest .NET SDK):

```
---
filename: build.sh
---
#!/bin/sh
curl -sSL https://dot.net/v1/dotnet-install.sh > dotnet-install.sh
chmod +x dotnet-install.sh
./dotnet-install.sh -c 8.0 -InstallDir ./dotnet
./dotnet/dotnet --version
./dotnet/dotnet publish -c Release -o output
```

Your `build.sh` file needs to be executable for the build command to work. You can make it so by running `chmod +x build.sh`.

{{<render file="_tutorials-before-you-start.md">}}

## Create a `.gitignore` file

Creating a `.gitignore` file ensures that only what is needed gets pushed onto your GitHub repository. Create a `.gitignore` file by running the following command:

```sh
$ dotnet new gitignore
```

{{<render file="/_framework-guides/_create-github-repository.md">}}

## Deploy with Cloudflare Pages

To deploy your site to Pages:

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/) and select your account.
2. In Account Home, select **Workers & Pages**.
3. Select **Create application** > **Pages** > **Connect to Git**.

Select the new GitHub repository that you created and, in the **Set up builds and deployments** section, provide the following information:

<div>

| Configuration option | Value            |
| -------------------- | ---------------- |
| Production branch    | `main`           |
| Build command        | `./build.sh`     |
| Build directory      | `output/wwwroot` |

</div>

After configuring your site, you can begin your first deploy. You should see Cloudflare Pages installing `dotnet`, your project dependencies, and building your site, before deploying it.

{{<Aside type="note">}}

For the complete guide to deploying your first site to Cloudflare Pages, refer to the [Get started guide](/pages/get-started/).

{{</Aside>}}

After deploying your site, you will receive a unique subdomain for your project on `*.pages.dev`.
Every time you commit new code to your Blazor site, Cloudflare Pages will automatically rebuild your project and deploy it. You will also get access to [preview deployments](/pages/configuration/preview-deployments/) on new pull requests, so you can preview how changes look to your site before deploying them to production.

## Troubleshooting

### A file is over the 25 MiB limit

If you receive the error message `Error: Asset "/opt/buildhome/repo/output/wwwroot/_framework/dotnet.wasm" is over the 25MiB limit`, resolve this by doing one of the following actions:

1.  Reduce the size of your assets with the following [guide](https://docs.microsoft.com/en-us/aspnet/core/blazor/performance?view=aspnetcore-6.0#minimize-app-download-size).

Or

2.  Remove the `*.wasm` files from the output (`rm output/wwwroot/_framework/*.wasm`) and modify your Blazor application to [load the Brotli compressed files](https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/webassembly?view=aspnetcore-6.0#compression) instead.

{{<render file="/_framework-guides/_learn-more.md" withParameters="Blazor">}}