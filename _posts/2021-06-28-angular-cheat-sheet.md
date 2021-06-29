---
layout: post
title: "Angular Cheat Sheet"
author: alfred
categories: [angular, yarn, ng, nx, cheat sheet]
featured: false
hidden: false
toc: true
---

Cheat sheet for CLIs and tools related to Angular development such as Yarn, Ng and Nx.

<!--more-->

### References

[Yarn](https://yarnpkg.com/getting-started)

[Ng](https://angular.io/cli)

[Nx](https://nx.dev/latest/angular/getting-started/nx-cli)

### npm

```sh
# Update npm to latest version
npm install -g npm
```

### Ng

```sh
# Update Angular CLI globally
npm uninstall -g @angular/cli
npm install -g @angular/cli

ng --version

# Perform basic update of existing project to the current stable release of the core framework and CLI
ng update @angular/cli @angular/core

# Switch to yarn; don't forget to delete package-lock.json
yarn

# Start API and app
ng serve api
ng serve

# Add Angular Material
ng add @angular/material
```

### Nx

```sh
# Install Nx CLI globally
npm install -g @nrwl/cli
yarn global add @nrwl/cli

# Create empty workspace
yarn create nx-workspace <ws_name>

# Switch to yarn; don't forget to delete package-lock.json
yarn

# Add Angular capability to workspace (change folder permissions first if in container)
chmod +rwx /workspaces/<ws_name/dev-container
ng add @nrwl/angular

# Add Angular Material
ng add @angular/material

# Generate application
ng g @nrwl/angular:application <app_nam>

# Updating Nx
#
# Migrate to latest Nx version (updates package.json)
nx migrate @nrwl/workspace
# Install package updates
yarn install
# Run migrations (if any)
nx migrate --run-migrations=migrations.json
```

### PrimeNG

```sh
# Add PrimeNG and icons
yarn add primeng
yarn add primeicons

# Add Angular animations and import BrowserAnimationsModule module in application module
yarn add @angular/animations
```
