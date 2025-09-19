# 🧱 Nx Modular Monolith Setup Guide

A comprehensive setup guide for building a modular monolithic architecture using [Nx](https://nx.dev) with support for:

- ✅ .NET Web API (ASP.NET Core)
- ✅ Angular frontend (client)
- ✅ Optional: React, Node.js backends
- ✅ DDD-style modular libraries
- ✅ Incremental builds and dependency graph

---

## 📦 Step 1 — Install Nx CLI

```bash
npm install -g nx
# or
npm add --global nx
```

---

## 🏗️ Step 2 — Create an Nx Workspace

```bash
npx create-nx-workspace@latest
# or with an empty preset:
npx --ignore-existing create-nx-workspace my-workspace --preset=empty

# Alternatively (if you already have a workspace):
npx nx@latest init --ignore-scripts
```
Say your workspace name is fullstackapp.

---

## 🌐 Step 3 — Angular Frontend App Setup

```bash
# Accessing workspace folder
cd fullstackname

# Install Angular plugin
npm install -D @nx/angular

# Generate Angular app in apps/frontend/client
npx nx g @nx/angular:app apps/frontend/client

# (Optional) Generate Angular libraries
npx nx g @nx/angular:lib libs/shared/ui
npx nx g @nx/angular:lib libs/features/auth

# Serve the Angular app
nx serve client
```

---

## ⚛️ Optional: React App Setup

```bash
npm install -D @nx/react
npx nx g @nx/react:app admin
```

---

## 🌐 Optional: Node.js Backend App Setup

```bash
npm install -D @nx/node
npx nx g @nx/node:app backend
```

---

## 🧩 Step 4 — .NET Backend App Setup

```bash
# Install Nx .NET plugin
npm add -D @nx-dotnet/core
# or
yarn add -D @nx-dotnet/core

# Initialize .NET support
nx g @nx-dotnet/core:init

# Generate a .NET Web API app
nx g @nx-dotnet/core:app apps/backend/api --framework webapi
```

---

## 📚 Step 5 — Create Domain/Feature/Shared Libraries

DDD-style layered structure:

```bash
nx g @nx-dotnet/core:lib users --directory domain
nx g @nx-dotnet/core:lib users --directory app
nx g @nx-dotnet/core:lib efcore --directory infrastructure
nx g @nx-dotnet/core:lib identity --directory shared
```

Angular feature/shared libraries:

```bash
nx g @nx/angular:lib auth --directory features
nx g @nx/angular:lib ui --directory shared
```

---

## 🔗 Step 6 — Reference .NET Projects Correctly

### CLI:

```bash
cd libs/app/users
dotnet add reference ../../../libs/domain/users/domain.users.csproj
```

### Or manually in `.csproj`:

```xml
<ItemGroup>
  <ProjectReference Include="..\..\..\domain\users\domain.users.csproj" />
</ItemGroup>
```

---

## 🛠️ Step 7 — Build / Serve / Test with Nx

```bash
nx build api
nx serve api
nx test app-users

nx serve client
nx test shared-ui
```

Targets are defined in each project's `project.json`.

---

## 🧩 Step 8 — Add Solution File (Optional for Visual Studio)

```bash
dotnet new sln -n my-monolith
dotnet sln add apps/api/api.csproj
dotnet sln add libs/**/**/*.csproj
```

---

## 🚧 Step 9 — Enforce Module Boundaries (Recommended)

### In `project.json`, add tags:

```json
{
  "tags": ["scope:domain", "type:lib"]
}
```

### In `nx.json`, define constraints:

```json
"projects": {},
"targetDefaults": {},
"pluginsConfig": {},
"workspaceLayout": {
  "appsDir": "apps",
  "libsDir": "libs"
},
"enforceModuleBoundaries": true,
"depConstraints": [
  {
    "sourceTag": "scope:app",
    "onlyDependOnLibsWithTags": ["scope:shared", "scope:app"]
  },
  {
    "sourceTag": "scope:features",
    "onlyDependOnLibsWithTags": ["scope:shared", "scope:domain"]
  }
]
```

---

## 📈 Step 10 — Visualize Project Dependency Graph

```bash
nx graph
```

Shows how all apps and libs relate to each other.

---

## 🗂️ Recommended Folder Structure

```
apps/
  api/                --> ASP.NET Core Web API
  client/             --> Angular frontend
  admin/              --> (Optional) React dashboard
  backend/            --> (Optional) Node service

libs/
  domain/users/       --> Domain layer
  app/users/          --> Application logic
  infrastructure/     --> EF Core, service impls
  shared/identity/    --> Shared types / value objects
  shared/ui/          --> Angular shared UI
  features/auth/      --> Angular feature modules
```

---

## ✅ Summary

You now have an Nx-powered modular monolithic architecture with:

- Scalable domain separation (DDD-style)
- Angular frontend + .NET backend
- Optional: React, Node.js support
- Nx features: caching, graph, generators, test runners

Happy coding! 🚀
