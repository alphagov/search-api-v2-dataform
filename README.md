# GOV.UK Search API v2 Dataform

## What's in this repo

This repo contains:

- [`definitions/`](definitions/): Google Dataform SQLX pipeline definitions for processing and transforming GA4 data into Google Verex AI Search datasets
- [`workflow_setting.yaml`](workflow_settings.yaml): Google Dataform workflow settings that apply to all pipelines

### What's not in this repo

Terraform definitions for dataform resources used to provision corresponding workflow and release configurations - [alphagov/govuk-infrastructure/blob/main/terraform/deployments/search-api-v2/dataform.tf](https://github.com/alphagov/govuk-infrastructure/blob/main/terraform/deployments/search-api-v2/dataform.tf).

## Usage

Install the [Dataform CLI](https://cloud.google.com/dataform/docs/use-dataform-cli) for local development.

Currently no [Dataform Workspace](https://cloud.google.com/dataform/docs/workspaces) is established for Google Cloud Console based pipeline development - all development is performed locally before pushing progressively into each environment/branch. Each Search API v2 GCP project/environment has a seperate Dataform Release configuration which maps onto the corresponding branch (i.e. `integration`, `staging` etc.).

## Development and deployment workflow

1. Make changes in a feature branch.
2. Force-push the feature branch to the `integration` branch (which will be created if it doesn't exist).

```
git push --force origin my-feature:integration
```

4. Click the button "New compilation" in the [Release configuration details](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/integration?project=search-api-v2-integration) page.
5. Click the button "Start execution" in the [Releases and scheduling](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/release-scheduling?project=search-api-v2-integration) page. Choose the "Default Dataform service account" from the options.  Only those actions relevant to the changes you have made need be executed.
<img width="556" height="849" alt="image" src="https://github.com/user-attachments/assets/995a37e2-f194-4999-8dcd-8f10c14b5bfa" />
6. Check the [workflow execution logs](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/workflows?project=search-api-v2-integration) and the results in BigQuery.
7. (Optional) Repeat steps 2 to 5 for the `staging` branch and GCP project.
8. Merge a pull request from the feature branch into the `main` branch.
9. Force-push the `main` branch into the `integration` and `staging` branches.
10. Repeat step 4 for each of the `main`, `integration` and `staging` branches and GCP projects.

## Documentation

See the [Google Dataform documentation](https://cloud.google.com/dataform/docs/overview).

## Team

GOV.UK Search team looks after this repo. If you're inside GDS, you can find us in [#govuk-search](https://gds.slack.com/channels/govuk-search)
