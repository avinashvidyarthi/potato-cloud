# Potato Cloud Home Site

Static website served by nginx and packaged as a Docker image.

## Local development

Serve the site locally with any static file server, or build and run the container:

```bash
docker build -t potato-cloud-home .
docker run --rm -p 8080:80 potato-cloud-home
```

Open http://localhost:8080

## CI/CD

The workflow at [`../.github/workflows/deploy-home.yml`](../.github/workflows/deploy-home.yml) runs when changes under `home/` are merged to `main`. It builds the Docker image and pushes it to GCP Artifact Registry.

### Required GitHub configuration

**Repository variables**

| Variable | Value |
| --- | --- |
| `GCP_PROJECT_ID` | `avinashvidyarthi` |
| `GCP_REGION` | `asia-south1` |
| `GCP_ARTIFACT_REGISTRY_REPO` | `potato-cloud` |

**Repository secrets**

| Secret | Description |
| --- | --- |
| `GCP_SA_KEY` | JSON key for `potato-cloud-home-deploy@avinashvidyarthi.iam.gserviceaccount.com` |

### GCP setup (one-time)

Service account (already created):

| Field | Value |
| --- | --- |
| Account ID | `potato-cloud-home-deploy` |
| Email | `potato-cloud-home-deploy@avinashvidyarthi.iam.gserviceaccount.com` |
| Display name | Potato Cloud Home Site - GitHub Actions Deploy |
| Role | `roles/artifactregistry.writer` |

Artifact Registry repository (already exists in `asia-south1`):

```bash
gcloud artifacts repositories describe potato-cloud \
  --location=asia-south1 \
  --project=avinashvidyarthi
```

To recreate the service account or key manually:

```bash
gcloud iam service-accounts create potato-cloud-home-deploy \
  --project=avinashvidyarthi \
  --display-name="Potato Cloud Home Site - GitHub Actions Deploy" \
  --description="Pushes the Potato Cloud home static site Docker image to Artifact Registry from GitHub Actions"

gcloud projects add-iam-policy-binding avinashvidyarthi \
  --member="serviceAccount:potato-cloud-home-deploy@avinashvidyarthi.iam.gserviceaccount.com" \
  --role="roles/artifactregistry.writer"

gcloud iam service-accounts keys create potato-cloud-home-deploy-key.json \
  --iam-account=potato-cloud-home-deploy@avinashvidyarthi.iam.gserviceaccount.com \
  --project=avinashvidyarthi
```

Copy the full contents of `home/potato-cloud-home-deploy-key.json` into the GitHub `GCP_SA_KEY` secret. The key file is stored locally and is listed in the root `.gitignore` so it is never committed.

Pushed image path:

```
asia-south1-docker.pkg.dev/avinashvidyarthi/potato-cloud/home:latest
```
