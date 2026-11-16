# deployments

What runs where. One Cloud Run service file per app and environment: `apps/<app>/<env>.yaml` (Knative format, applied with `gcloud run services replace`).

- Files are created by Terraform in the `platform` repo when an app is added. After that, this repo owns them.
- App CI bumps the image in `dev.yaml` on every push to main. Dev deploys straight away.
- `promote` workflow copies the dev image to `prod.yaml` and starts a deploy. Prod deploys wait for approval on the `prod` environment.
- Any other change (memory, scaling, env vars) is a normal edit to the file. Merging it deploys it.
- `metadata.labels.public: "true"` makes the service public (`allUsers` invoker).

Removing a file does not delete the Cloud Run service. Delete it with `gcloud run services delete`.
