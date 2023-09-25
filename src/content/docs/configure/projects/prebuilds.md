---
section: projects
title: Gitpod Prebuilds
description: Learn how to configure prebuilds for your Gitpod projects. Prebuilds reduce wait time,by installing dependencies or running builds before you start a new workspace.
---

# Prebuilds

Prebuilds help you to optimise the time to open a workspace in Gitpod by executing any defined installation tasks from the `.gitpod.yml` in associated repository asynchronously ahead of time.

Rather than running installation tasks every time you open a workspace, with Prebuilds, Gitpod will run any installation and setup tasks for you behind-the-scenes, cache them, and then re-use that cache when starting your workspace. Prebuilds work similarly to Continuous Integration (CI) systems by responding to an SCM trigger (webhooks) and remotely executing instructions.

To understand Prebuilds in Gitpod, it helps to already be familiar with:

1. [Projects](/docs/configure/projects)
2. [Tasks](/docs/configure/workspaces/tasks)
3. [.gitpod.yml](/docs/references/gitpod-yml)

`youtube: DwkoOz1GSVg`

## How Prebuilds relate to Projects and `.gitpod.yml`

A Prebuild cannot be created without a Project, as Projects control Prebuild settings. A Prebuild also requires that you have a `.gitpod.yml` stored in your repository with either the `init` or `before` tasks added as these tasks tell Gitpod what to execute in the Prebuild.

The `.gitpod.yml` stored in your repository is a declarative instruction set of how the repository should be bootstrapped. Gitpod will run these instructions automatically when workspaces start, regardless of whether Prebuilds are enabled for the repository or not.

Creating a Project and enabling a Prebuild is complimentary to the repositories `.gitpod.yml` definition. In fact, by defining the `init` and `before` tasks in your `.gitpod.yml` you are explicitly telling Gitpod which parts of your setup scripts can be handled asynchronously in a Prebuild.

## Prebuild triggers

Prebuilds are executed based on triggers, such as:

1. **Webhook** - A URL is registered with the git provider, which is then invoked when changes are observed to the repository. Some Git providers allow webhooks to be filtered, otherwise those filters must be applied via the Gitpod Project settings.
2. **Manually** - A user can trigger a Prebuild manually through the Gitpod UI, which can be useful for testing Prebuilds.

#### Webhook Prebuild triggers

Prebuilds are trigger based on webhooks. The user who creates the project must have permissions to setup a webhook on the repository. Each user can modify their settings in the [user settings](/user/integrations).

By default, Gitpod Prebuilds workspaces for all changes on the default branch and for pull/merge requests from the same repository.

For more details on how to setup webhooks for each provider, see:

-   [BitBucket](/docs/integrations/bitbucket)
-   [GitHub](/docs/integrations/github)
-   [GitLab](/docs/integrations/gitlab)

#### Manual Prebuild triggers

Incremental Prebuilds use an earlier, successful Prebuild as the base. This can reduce the duration of your Prebuilds significantly, especially if they normally take more than ten minutes to complete. Incremental Prebuilds can be found under the project settings in your Gitpod dashboard.

## Prebuild filters

A Prebuild filter allows you to configure when a Prebuild should execute, even after a trigger is received. Prebuild filters can help to save costs by not running Prebuilds unnecessarily.

### Configuring Prebuilds in the `.gitpod.yml`

### Branch based filters

For GitLab, if you want to trigger new Gitpod prebuilds for specific branches only, you can configure this in your GitLab [project settings](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#branch-filtering).

See:

-   [BitBucket](/docs/integrations/bitbucket)
-   [GitHub](/docs/integrations/github)
-   [GitLab](/docs/integrations/gitlab)

### Skip Prebuilds by commit

Gitpod can leverage Prebuilds which are made on previous commits, and doesn't require a Prebuild to be created for the exact commit of the workspace. You can configure how many commits to "skip" in your Project settings. You may want to consider skipping a larger number commits if your repository has a large volume of commits. If you don't push new commits often configuring to skip `0` prebuilds could be appropriate.

## Prebuild Modes

Each Prebuild starts with a clean environment. In other words, Gitpod does not cache artifacts between prebuilds.

### Incremental Prebuilds

Incremental prebuilds leverage previously successful Prebuilds to create a new Prebuild more efficiently by re-running the `before` and `init` tasks again to create a newer Prebuild. By re-using the filesystem the Prebuild typically completes more quickly.

Gitpod finds a Prebuild to increment on by traversing up to 100 ancestor (e.g. related) commits to find the last successful prebuild that has an identical `init` task. If no suitable base Prebuild is found, Gitpod will build using a blank file system.

> **Important:** The `.gitpod.yml` tasks will be re-ran so must be idempotent. You can test this by running `gp validate --prebuild` multiple times in your workspace.

### Last Successful Prebuild

Gitpod by default enforces that a Prebuild must match for the exact commit of a workspace. When enabling "last successful Prebuild". Gitpod will traverse the last 100 ancestor commits to use the last successful Prebuild.

## Managing Prebuilds

To configure a Prebuild for a repository you must:

1. Add a `init` or `before` task in the repository [gitpod.yml](https://www.gitpod.io/docs/references/gitpod-yml).
2. Create a corresponding [Project](/docs/configure/projects) for the repository.
3. Enable Prebuilds in the project settings.

> **Tip:** You can test changes to your tasks and `.gitpod.yml` directly in your workspace by running the `gp validate --prebuild` command.

### Configuring Prebuilds

A prebuild cannot be executed unless you tell Gitpod explicitly which steps in your `.gitpod.yml` should be ran in the prebuild. You can do this by ensuring you have either an `init` or `before` task.

The below example `.gitpod.yml` shows a repository that will run `npm install` inside a Prebuild. The `command` task with `npm start` is not executed in the Prebuild as it's assumed to be a long-running process, e.g. running a web server.

```yml
tasks:
    - init: |
          npm install
    - command: |
          npm start
```

### Enabling prebuilds

When a Prebuild ran successfully, you will see the following in your workspace output:

```txt
🤙 This task ran as a workspace prebuild
```

Since prebuilds are included in all our metered [pay-as-you-go](https://www.gitpod.io/docs/configure/billing) plans, configuring prebuild settings in your project should help with managing prebuild usage.

### View prebuilds

You can find a log of past Prebuilds in your Project settings.

1. Go to the [/projects](https://gitpod.io/projects) page.
2. Select the Project.
3. Navigate to the "Prebuilds" tab to see Prebuild history.

### Rerun a Prebuild

Prebuilds can be triggered manually for debugging purposes. To rerun a prebuild:

1. Navigate to your project and select the Prebuilds tab.
2. Select the Prebuild.
3. Select "Rerun Prebuild".

## Prebuild limitations

### Only the `/workspace` directory is persisted from a Prebuild

Once a Prebuild is completed, a snapshot of the filesystem is taken. However, this snapshot only includes the `/workspace` directory. Other directories like the home directory are not saved by Prebuilds. To ensure the necessary files are saved, copy them to the `/workspace` directory before the Prebuild completes, and/or restore those files in your `command` task.

### Prebuilds have a 1 hour time limit

Prebuilds have a timeout of 1 hour. If your `before` and `init` tasks combined exceed 1 hour, your Prebuild will be terminated.

### Prebuilds are executed as the user who enables them

To pull git information into a workspace, Prebuilds are executed on behalf of the user who created the Prebuild.

### Workspaces may behave differently when Prebuilds are disabled

If your scripts are reliant on Project environment variables but no Prebuild is ran, the workspace will not have access to those Project environment variables causing unexpected behaviours. We suggest you:

1. Throw an error when there are missing Project environment variables
2. Ensure your scripts run whether in a Prebuild, or not.

## Frequently Asked Questions (FAQs)

#### Can I use project environment variables in Prebuilds?

Environment variables that are defined in project settings will be visible in Prebuilds.
