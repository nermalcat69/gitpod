---
section: projects
title: Projects
description: Learn how to manage projects in your organization. Also learn about project roles and permissions. It allows configuring settings like environment variables and prebuilds for repositories in your organization.
---

# Projects

A project is an **optional** way to collaboratively add configuration and refine the experience of working with a repository in Gitpod. You **do not need to create a project to work in Gitpod**.

All Gitpod users can open a workspace based on any repository regardless of whether the Organization has a corresponding project provided that the user has the necessary SCM authorization (e.g. public repo, or private repos authorized in Gitpod settings).

## Why are Projects useful?

1. **Improve workspace start performance** - Enabling Prebuilds will help to speed up the time to open a workspace in Gitpod by running defined installation tasks in `.gitpod.yml` asynchronously ahead of time. Prebuilds work similarly to Continuous Integration (CI) systems by responding to an SCM trigger (webhooks) and remotely executing instructions.
2. **Create "golden paths" for your repositories** - When you create projects in Gitpod, you are "signposting" to users of your Organization that that repository is configured and "ready-to-code", a project allows you to collaborate together to improve your developer experience.
3. **Add dynamic configuration** - With Projects you can attach environment variables to be used in your `.gitpod.yml` configuration. It can be useful to centrally manage your environment variables, as opposed to manually handling the distribution of environment variables to all the users of a Gitpod Organization.

## How Projects relate to a repository `.gitpod.yml`

The `.gitpod.yml` stored in your repository is a declarative instruction set of how a repository should be started. Gitpod runs these instructions automatically when workspaces start, regardless of whether Prebuilds are enabled for the repository or not.

The project entity is complimentary to a repository `.gitpod.yml` definition. You can access the environment variables defined in the Project from within the scripts in your `.gitpod.yml`.By defining the `init` and `before` tasks in your `.gitpod.yml` you are telling Gitpod which parts of your setup script can be handled in a Prebuild, when enabled.

## How Projects behave in Gitpod

The following are a list of important notes about how Projects work in Gitpod.

1. **Projects are associated within an organisation** - A project is created within a single Gitpod Organisation so that when Prebuilds are then enabled on a project, the costs can be attributed.
2. **A repository can only have one project** - You can only connect one project to a single repository. In other words, creating two projects for a single repository is not possible with Gitpod.
3. **Anyone can create and edit all project** - All members of an Organization can see all Projects, and all members can update any setting in any Project.
4. **Projects are shown regardless of SCM Authorization** - All Projects are viewable regardless of whether the user has access to the respective SCM (e.g. GitHub). Though, an error will be shown when starting workspaces where the user if not permitted to access the repository.
5. **Adding some project settings require SCM permissions** - Some project settings, such as enabling Prebuilds will require the user to have certain SCM permissions, such as the ability to create a webhook.

## Managing Projects

### Viewing a Project

1. Before viewing your proejcts, ensure that you are looking at the correct Organization using the Organization switcher in the top left of the dashboard.
2. To view all your Organization projects visit [/projects](https://gitpod.io/projects).

### Adding a Project

1. Projects are created in the current Organization. Ensure you are looking at the correct Organization using the Organization switcher in the top left of the dashboard.
2. Add a new Project to the Organization by visiting [/projects/new](https://gitpod.io/projects/new). You can also create Projects directly from the [/projects](https://gitpod.io/projects) page.

<!-- * A user can only add repositories that they have access to.  -->

<!-- * You may also be prompted to configure our [GitHub app](/docs/configure/authentication/github#authorizing-github-webhooks), the first time a project is created for a GitHub account. The new project will be associated with the current organization selected in the dashboard. -->

### Configuring a Project

Organization members do the following via Project settings:

1. Rename a Project
    - Choose any name up to 32 characters (default: repository name)
2. Configure [Prebuilds](/docs/configure/projects/prebuilds) for improved workspace start performance.
    - Enable/disable Prebuilds (default: disabled)
    - Select Prebuild filters (e.g. branch pattern)
    - Associated workspace class for the Prebuild
3. Add [Environment Variables](/docs/configure/projects/environment-variables) to the Prebuild.
    - Optionally you can remove variables once the Prebuild is complete

### Remove a project

1. Go to the [/projects](https://gitpod.io/projects) list page.
2. Select the Project you wish to delete.
3. Select "Remove Project"

## FAQs

### Does billing apply for Projects?

Creating a Project to configure environment variables is not included in your [Billing](/docs/configure/billing).

However, any configured Prebuild usage is billed.

### The "New Project" page is not finding the repositories I expect?

1. Reconnect your git provider in [/integrations](https://gitpod.io/integrations).
2. Ensure at least read scopes/permissions are set for the git integration in [user settings](https://gitpod.io/settings).
