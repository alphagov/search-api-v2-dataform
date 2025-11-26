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

### Alternative development and deployment workflow

1. Make changes in a feature branch.
2. Open a pull request to merge the feature branch into the `integration` branch. If the `integration` branch doesn't already exist, then create it first:

```
git checkout main
git pull
git checkout -b integration
git push
```

3. Merge the pull request.
4. Visit dataform in [integration](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/integration?project=search-api-v2-integration) and click the blue button "New compilation".
5. Click "Start execution" in [integration](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/release-scheduling?project=search-api-v2-integration), and choose the options in the form that pops up. Only those actions relevant to the changes you have made need be executed.

<img width="556" height="849" alt="image" src="https://github.com/user-attachments/assets/995a37e2-f194-4999-8dcd-8f10c14b5bfa" />

5. Check in the workflow execution logs in [integration](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/workflows?project=search-api-v2-integration) that the execution was successful.
6. [Open a pull request](https://github.com/alphagov/search-api-v2-dataform/compare/staging...integration) to merge the `integration` branch into the `staging` branch. This pull request will include a merge commit from having merged the feature branch into the `integration` branch. If the `staging` branch doesn't already exist, then create it first:

```
git checkout main
git pull
git checkout -b staging
git push
```

7. Merge the pull request.
8. Visit dataform in [staging](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/staging?project=search-api-v2-staging) and click the blue button "New compilation".
9. Click "Start execution" in [staging](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/release-scheduling?project=search-api-v2-staging), and choose similar options to the ones in step 5.
10. Check in the workflow execution logs in [staging](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/workflows?project=search-api-v2-staging) that the execution was successful.
11. [Open a pull request](https://github.com/alphagov/search-api-v2-dataform/compare/main...staging) to merge the `staging` branch into the `main` branch. This pull request will include both merge commits from the previous pull requests.
12. Visit dataform in [production](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/production?project=search-api-v2-production) and click the blue button "New compilation".
13. Click "Start execution" in [production](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/release-scheduling?project=search-api-v2-production), and choose similar options to the ones in previous steps.
14. Check in the workflow execution logs in [production](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/workflows?project=search-api-v2-production) that the execution was successful.

## Documentation

See the [Google Dataform documentation](https://cloud.google.com/dataform/docs/overview).

## Team

GOV.UK Search team looks after this repo. If you're inside GDS, you can find us in [#govuk-search](https://gds.slack.com/channels/govuk-search)
