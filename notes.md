## Notes

-   Customers sometimes want to see every image (not just workspace images)

## Current Docs

1. /docs/configure/workspaces/workspace-image#use-a-private-docker-image
2. /docs/configure/authentication
3. /docs/gitpod-dedicated/guides/use-private-ecr-repos-for-workspace-images#using-private-ecr-repositories-for-workspace-images
4. /docs/configure/self-hosted/latest/reference-architecture/single-cluster-ref-arch#oci-image-registry
5. Org level workspace image support

## Current Functionality

### Types of auth

1. User (SSO, SCM - OAuth)
2. Source Control
3. **Workspace Image Auth**
4. Environment Vars / OIDC

### Configuring workspace images

1. **Org level workspace image**

-   Authentication is handled by the installation (no username/password right now)
    -   If Gitpod Cloud (Public Registries)
    -   If Dedicated, same as installation (e.g. Private Registries)

2. **Project level workspace image (auth only)**

-   Authentication is handled at the workspace level
-   We have no support for org-level environment variables
-   **Question:** What component is actually authenticated with the env vars? Workspace? Registry facade?
-   **Question:** Who would use this feature now that we have org-wide images? The use case would probably be for overriding the default.

3. **`.gitpod.yml` (built image, or dockerfile)**

-   Controlled by those who have access to the SCM.
-   Overwrites the org-level
-   **Suggestion:** `gp info` should tell you your workspace image source

4. **Pulling an image in a workspace (e.g. docker pull)**

-   Ideally we talk through or link to Docker docs on how to authenticate (With env vars, OIDC, etc) and other best practices.
-   **Question:** Does the project level auth apply here?

### Accessing and viewing images for other components (Dedicated only)

-   /docs/configure/self-hosted/latest/reference-architecture/single-cluster-ref-arch#oci-image-registry

### Helpers

-   **Workspace Full (and other images)**

### Decision: Blend Dedicated and Gitpod Cloud docs?

-   Example: `/docs/gitpod-dedicated/guides/getting-started#3-set-up-gitpod`
-   Move (some, maybe not all) SSO content to `/docs/configure/authentication`
