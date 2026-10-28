# Manual publishing protocol

The public Observatory is a **curated snapshot**, not a mirror of the private Emergent repository.

## Normal update

1. Review the latest research in the private `doukaspetros-coder/Emergent` repository.
2. Decide which findings are mature and appropriate to publish. Preserve uncertainty, limitations, and audit caveats; do not treat a commit or model-generated claim as proof by itself.
3. Manually edit the public `data.json` and any related public-facing text or assets. Include only material approved for public release. Do not copy private run tables, internal trails, credentials, or unreviewed material.
4. Review the diff in the public repository and commit the curated changes.
5. When ready to update the live website, open **Actions → Deploy public Observatory (manual only) → Run workflow** in this repository.
6. Check the workflow result and inspect the live site after deployment.

## What does not happen automatically

- The private Emergent repository does not publish to this repository.
- A push to this public repository does not deploy the website.
- There is no scheduled publication.
- The live website remains on its last successful deployment until a person deliberately runs the deployment workflow.

## Separation of duties

Editing and committing the public snapshot changes the repository's reviewed source files; it does not by itself change the live website. Running the workflow deploys the current public repository contents. Review both the content diff and the deployment result before considering an update complete.
