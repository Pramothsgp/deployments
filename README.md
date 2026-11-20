# deployments

What runs where. One Cloud Run service file per repo, service and environment: `apps/<repo>/<service>/<env>.yaml` (Knative format, applied with `gcloud run services replace`).

- Files are created by Terraform in the `platform` repo when an app is added. After that, this repo owns them.
- App CI bumps the image in every service's `dev.yaml` on each push to main. Dev deploys straight away.
- `promote` workflow copies dev images to `prod.yaml` for all services of a repo (or one, with `service`) and starts a deploy. Prod deploys wait for approval on the `prod` environment.
- Any other change (memory, scaling, env vars) is a normal edit to the file. Merging it deploys it.
- `metadata.labels.public: "true"` makes the service public (`allUsers` invoker).
- If `BETTER_AUTH_URL` is a custom host (not `*.run.app`), deploy creates a Cloud Run domain mapping for it. The
  `deployer` service account must be an owner of the domain in Search Console, and the host needs a CNAME to
  `ghs.googlehosted.com.`

Removing a file does not delete the Cloud Run service. Delete it with `gcloud run services delete`.
