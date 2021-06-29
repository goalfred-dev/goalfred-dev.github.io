---
layout: post
title: "GitHub Actions"
author: alfred
categories: [github, info sheet]
featured: false
hidden: false
---

GitHub Actions help you automate tasks within your software development life cycle. GitHub Actions are event-driven, meaning that you can run a series of commands after a specified event has occurred. For example, every time someone creates a pull request for a repository, you can automatically run a command that executes a software testing script.

### General Info

[GitHub Actions Features](https://github.com/features/actions)

[GitHub Actions Documentation](https://docs.github.com/en/actions) - [Introduction to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)

[How GitHub accelerates cloud adoption](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/github-velocity/)

[DevSecOps in GitHub](https://docs.microsoft.com/en-us/azure/architecture/solution-ideas/articles/devsecops-in-github)

### Concepts

#### Automate Workflows

Easily automate your code-to-cloud workflows on any GitHub event, such as code commits, creation of pull requests, or new GitHub releases.

Deploy apps to Azure across popular languages and frameworks—including .NET, Node.js, Java, PHP, Ruby, Python—in containers, or running on any operating system.

#### Shift Left

Use GitHub Actions for Azure to simplify “shifting-left,” meaning the automation of governance, security, and compliance into the early stages of the software development life cycle.

Write infrastructure configurations, release pipelines, compliance, and security policies “as code” to enable continuous improvement and greater transparency.

#### Accelerate developer velocity

Deploy to Azure using popular developer tools integrated with GitHub Actions. Significantly reduce ramp-up time, avoid frequent context switching, and help teams be more productive with built-in extensions into Visual Studio Code, Azure CLI, and Azure Portal.

### Applicability

[GitHub and Azure](https://azure.microsoft.com/en-us/products/github/)

[GitHub Actions for Azure](https://azure.github.io/actions/#automate) - [Azure Actions Repo](https://github.com/Azure/actions)

[Building and testing .NET](https://docs.github.com/en/actions/guides/building-and-testing-net)

[Deploy to App Service using GitHub Actions](https://docs.microsoft.com/en-us/azure/app-service/deploy-github-actions?tabs=applevel) - [Deploy Function Apps using GitHub Actions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-github-actions?tabs=dotnet)

[Packaging with GitHub Actions](https://docs.github.com/en/actions/guides/about-packaging-with-github-actions) - [Storing workflow data as artifacts](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)

[Deploy ARM templates by using GitHub Actions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions) - [Learn module: Automate the deployment of ARM templates by using GitHub Actions](https://docs.microsoft.com/en-us/learn/modules/deploy-templates-command-line-github-actions/)

[Environments](https://docs.github.com/en/actions/reference/environments) - [Reviewing / Approve deployments](https://docs.github.com/en/actions/managing-workflow-runs/reviewing-deployments) - [Using the visualization graph](https://docs.github.com/en/actions/managing-workflow-runs/using-the-visualization-graph)

[Example of complete workflow build, deploy to staging, deploy to production](https://timheuer.com/blog/add-approval-workflow-to-github-actions/)

[Managing complex workflows](https://docs.github.com/en/actions/learn-github-actions/managing-complex-workflows) - [Sharing workflows with your organization](https://docs.github.com/en/actions/learn-github-actions/sharing-workflows-with-your-organization)

### Advantages

* 'Natural' CI/CD workflows, triggered by many available fine-grained events
* Very good documentation
* CI/CD code stored in repo where code resides
* Big community
* Many workflow templates available (standard and community)
* Good integration with and support for Azure / Visual Studio
* Supports complete CI/CD workflow for building and deploying on Azure
* Built-in secrets
* Supports environments (secrets / environment)
* Allows creating complete workflows build, test, deploy to integration, deploy to test, deploy to staging, deploy to production with necessary approvals in between the phases
