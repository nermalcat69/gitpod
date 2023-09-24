---
section: projects
title: Gitpod Prebuilds
description: Learn how to configure prebuilds for your Gitpod projects. Prebuilds reduce wait time,by installing dependencies or running builds before you start a new workspace.
---

# Prebuilds

Prebuilds speed up the time to open a workspace in Gitpod by running defined installation tasks from the `.gitpod.yml` stored in the repository asynchronously ahead of time. Prebuilds work similarly to Continuous Integration (CI) systems by responding to an SCM trigger (webhooks) and remotely executing instructions.

`youtube: DwkoOz1GSVg`

## Projects, Prebuilds and the `.gitpod.yml`

The `.gitpod.yml` stored in your repository is a declarative instruction set of how a repository should be started. Gitpod runs these instructions automatically when workspaces start, regardless of whether Prebuilds are enabled for the repository or not.

The project entity is complimentary to a repository `.gitpod.yml` definition. By defining the `init` and `before` tasks in your `.gitpod.yml` you are telling Gitpod which parts of your setup script can be handled in a Prebuild, when enabled.

## Prebuild triggers

For a Prebuild to run, it must first receive a trigger, either:

1. Via Webhook
2. Manually

#### Webhook Prebuild triggering

#### Manual Prebuild triggering

All prebuilds require a [Gitpod project](/docs/configure/projects#add-a-new-project) for the repository.

For a GitLab or Bitbucket repository, allow Gitpod to install repository webhooks, by granting `api` permissions in your [git provider integrations](https://gitpod.io/integrations)

For GitLab, if you want to trigger new Gitpod prebuilds for specific branches only, you can configure this in your GitLab [project settings](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#branch-filtering).

By default, Gitpod prepares prebuilt workspaces for all changes on the default branch and for pull/merge requests coming from the same repository.

> **Note**: Prebuilds are executed as the user who enabled them. This means that if you want to use
> prebuilds on a private repository, you must give Gitpod access to private repositories.

Prebuilds are configured in your repository's [`.gitpod.yml`](/docs/references/gitpod-yml) file with the following start tasks:

-   `before`
-   `init`

Prebuilds have a timeout of 1 hour. If your `before` and `init` tasks combined exceed 1 hour, your prebuild will fail. Subscribe to [this issue](https://github.com/gitpod-io/gitpod/issues/6283) for updates when this limit will be lifted.

Each prebuild starts with a clean environment. In other words, Gitpod does not cache artifacts between prebuilds.

Incremental Prebuilds use an earlier, successful Prebuild as the base. This can reduce the duration of your Prebuilds significantly, especially if they normally take more than ten minutes to complete. Incremental Prebuilds can be found under the project settings in your Gitpod dashboard.

## Prebuild filters

A Prebuild filter allows you to configure when a Prebuild should execute, even after a trigger is received. Prebuild filters can help to save costs by not running Prebuilds unnecessarily.

### Skip Prebuilds by commit

Gitpod can leverage Prebuilds which are made on previous commits, and doesn't require a Prebuild to be created for the exact commit of the workspace. You can configure how many commits to "skip" in your Project settings. You may want to consider skipping a larger number commits if your repository has a large volume of commits. If you don't push new commits often configuring to skip `0` prebuilds could be appropriate.

## Prebuild limitations

### Only the `/workspace` directory is persisted

Prebuilds save **only** the workspace directory. Other directories like the home directory are not saved by prebuilds.

To use global installs like 'npm install -g', we suggest using a custom Dockerfile or a `before` task, which runs before the init task, and runs again before the command task.

## Managing Prebuilds

### Enabling prebuilds

A prebuild cannot be executed unless you tell Gitpod explicitly which steps in your `.gitpod.yml` should be ran in the prebuild. You can do this by ensuring you have either an `init` or `before` task.

```yml
tasks:
    - init: |
          npm install
    - command: |
          npm start
```

**Caption:** The above example `.gitpod.yml` will runs `npm install` in the Prebuild, and `npm start` when the workspace starts.

1. **The `before` task [reference](/docs/references/gitpod-yml#tasksnbefore)** - A task executed on every workspace start.
2. **The `init` task [reference](/docs/references/gitpod-yml#tasksninit)** - Executed only once, either in the Prebuild if Prebuilds are enabled, or on the workspace start if Prebuilds are disabled.

When a Prebuild runs successfully, you will see the following message in your workspace terminal output:

```txt
🤙 This task ran as a workspace prebuild
```

The `command` task is not executed in Prebuilds as it's presumed to be long-running, e.g. starting a long-running web server.

1. Define an `init` or `before` [task](https://www.gitpod.io/docs/configure/workspaces/tasks) in your repository [gitpod.yml](https://www.gitpod.io/docs/references/gitpod-yml).
2. Validate the change by running `gp validate --prebuild`
3. Create a [Project](/docs/configure/projects) for the repository.
4. Enable Prebuilds in the project settings.
5. Start a workspace based on the project.

Since prebuilds are included in all our metered [pay-as-you-go](https://www.gitpod.io/docs/configure/billing) plans, configuring prebuild settings in your project should help with managing prebuild usage.

### View prebuilds

You can find a log of past Prebuilds in your Project settings.

1. Go to the [/projects](gitpod.io/projects) page.
2. Select the Project.
3. Navigate to the "Prebuilds" tab to see Prebuild history.

### Rerun a Prebuild

Prebuilds can be triggered manually for debugging purposes. To rerun a prebuild:

1. Navigate to your project and select the Prebuilds tab.
2. Select the Prebuild.
3. Select "Rerun Prebuild".

### Configuring prebuilds

> **Important:** The following Prebuild configuration method **only applies to GitHub**. There is no way to configure Prebuild filters in the `.gitpod.yml` for GitLab or Bitbucket.

The `github` namespace in the `.gitpod.yml` file defines when prebuilds should be executed. By default, prebuilds are run on push to the default branch and for each pull request coming from the same repository.

```yml
github:
    prebuilds:
        # enable for the default branch (defaults to true)
        master: true
        # enable for all branches in this repo (defaults to false)
        branches: false
        # enable for pull requests coming from this repo (defaults to true)
        pullRequests: true
        # enable for pull requests coming from forks (defaults to false)
        pullRequestsFromForks: false
        # add a check to pull requests (defaults to true)
        addCheck: true
        # add a "Review in Gitpod" button as a comment to pull requests (defaults to false)
        addComment: false
        # add a "Review in Gitpod" button to the pull request's description (defaults to false)
        addBadge: false
```

#### Additional GitHub integration

Once the GitHub app is installed, Gitpod can add helpful annotations to your pull requests.

#### Checks

By default, Gitpod registers itself as a check to pull requests - much like a continuous integration system would do.

The default behavior, however, would not make the checks fail when the prebuild failed.
This can be enabled with the following snippet:

```yml
github:
    prebuilds:
        addCheck: prevent-merge-on-error
```

You can disable this behaviour in the `.gitpod.yml` file in your default branch:

```yml
github:
    prebuilds:
        addCheck: false
```

#### Comment

Gitpod can add a comment with an "Open in Gitpod" button to your pull requests.

You can enable this behaviour in the `.gitpod.yml` file in your default branch:

```yml
github:
    prebuilds:
        addComment: true
```

#### Badge

Instead of adding a comment, Gitpod can also modify the description of a pull request to add the "Open in Gitpod" button.
This approach produces fewer GitHub notifications, but can also create a concurrent editing conflict when the bot and a user try to edit the description of a pull request at the same time.

You can enable this behaviour in the `.gitpod.yml` file in your default branch:

```yml
github:
    prebuilds:
        addBadge: true
```

The `addComment` and `addBadge` behaviours are not mutually exclusive (i.e. enabling one does not disable the other).
If you don't want the comments to be added, disable them using `addComment: false`.

## Prebuild Modes

### Incremental Prebuilds

Incremental prebuilds leverage previously successful Prebuilds to create a new Prebuild more efficiently by re-running the `before` and `init` tasks again to create a newer Prebuild. By re-using the filesystem the Prebuild typically completes more quickly.

Gitpod finds a Prebuild to increment on by traversing up to 100 ancestor (e.g. related) commits to find the last successful prebuild that has an identical `init` task. If no suitable base Prebuild is found, Gitpod will build using a blank file system.

> **Important:** The `.gitpod.yml` tasks will be re-ran so must be idempotent. You can test this by running `gp validate --prebuild` multiple times in your workspace.

### Last Successful Prebuild

Gitpod by default enforces that a Prebuild must match for the exact commit of a workspace. When enabling "last successful Prebuild". Gitpod will traverse the last 100 ancestor commits to use the last successful Prebuild.

## Frequently Asked Questions (FAQs)

#### Can I use project environment variables in Prebuilds?

Environment variables that are defined in project settings will be visible in Prebuilds.
