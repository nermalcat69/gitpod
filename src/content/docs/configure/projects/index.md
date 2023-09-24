---
section: projects
title: Projects
description: Learn how to manage projects in your organization. Also learn about project roles and permissions. It allows configuring settings like environment variables and prebuilds for repositories in your organization.
---

# Projects

A project is an **optional** way to collaboratively add configuration and refine the experience of working with a repository in Gitpod. You **do not need to create projects to work with Gitpod**. Any user can open any repository from the git providers that the user has authorized in their settings.

## Why are Projects useful?

1. **Improve workspace start performance** - Enabling Prebuilds will help to speed up the time to open a workspace in Gitpod by running defined installation tasks in `.gitpod.yml` asynchronously, similarly to how Continuous Integration (CI) systems work.
2. **Create "golden paths" for your repositories** - When you create projects, you are "signposting" to other users of your Organization that the repository is now fully configured and is ready-to-code.
3. **Add dynamic configuration** - With Projects you can attach environment variables to be used in your `.gitpod.yml` configuration. It can be useful to centrally manage environment variables, rather than manage distributing those environment variables to all the users of an organisation.

## How Projects relate to a repository `.gitpod.yml`

The `.gitpod.yml` stored in your repository is a declarative instruction set of how a repository should be started. Gitpod runs these instructions automatically when workspaces start.

The project entity is _complimentary_ to a repositories `.gitpod.yml` definition, as you can access the environment variables defined in the project in your scripts. You can also configure the `init` and `before` tasks in your `.gitpod.yml` to be executed as a Prebuild (if prebuilds are enabled on the Project).

## Characteristics of a Project

1. **Projects are associated within an organisation** - A project is created within a Gitpod Organisation so that when Prebuilds are enabled on a project, cost is attributed to the organisation.
2. **Projects are connected to a single git repository** - However, using a multi-repo configuration, repositories can be connected together.
3. **Anyone can create and edit project** - All members of an organisation can see all projects and all members can update any setting in a project. All proejcts are viewable regardless of whether the user has access to the respective SCM (e.g. GitHub), those an error will be shown when starting workspaces where the user is not permitted to read the repository.
4. **Some project settings require SCM permissions** - Some project settings, such as prebuilds will require the user to have certain SCM permissions, such as the ability to create a webhook.
5. **A repository can only have one project** - You can only have one project per repository in Gitpod.

## Managing Projects

### Viewing a Project

-   To view all Organization projects, visit [/projects](https://gitpod.io/projects).

### Adding a Project

-   To add a new project, visit [/projects/new](https://gitpod.io/projects/new).
<!-- * A user can only add repositories that they have access to.  -->

<!-- * You may also be prompted to configure our [GitHub app](/docs/configure/authentication/github#authorizing-github-webhooks), the first time a project is created for a GitHub account. The new project will be associated with the current organization selected in the dashboard. -->

### Configuring a Project

Organization members can configure Prebuilds, and set the workspace class used for project workspaces, in project settings.

### Remove a project

You can remove a project using the "Remove project" action from a project card.

## FAQs

### Am I billed for Prebuilds on a Project?

<!-- TODO: -->

### [New Project page is stuck at fetching repositories](https://discord.com/channels/816244985187008514/1056255866791272488)

<!-- DISCORD_BOT_FAQ - DO NOT REMOVE -->

To identify the problem: check browser console logs.

Possible solution(s):

-   Try to disconnect Git(Hub/Lab) or Bitbucket from https://gitpod.io/integrations, then reconnect.
-   Make sure correct scopes are set for those Git integration providers as well.
