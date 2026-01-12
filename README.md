# GOV.UK Search API v2 Dataform

## What's in this repo

This repo contains:

- [`definitions/`](definitions/): Google Dataform SQLX pipeline definitions for processing and transforming GA4 data into Google Verex AI Search datasets
- [`workflow_setting.yaml`](workflow_settings.yaml): Google Dataform workflow settings that apply to all pipelines

### What's not in this repo

Terraform definitions for dataform resources used to provision corresponding workflow and release configurations - [alphagov/govuk-infrastructure/blob/main/terraform/deployments/search-api-v2/dataform.tf](https://github.com/alphagov/govuk-infrastructure/blob/main/terraform/deployments/search-api-v2/dataform.tf).

## Usage

Install the [Dataform CLI](https://cloud.google.com/dataform/docs/use-dataform-cli) for local development.

Currently no [Dataform Workspace](https://cloud.google.com/dataform/docs/workspaces) is established for Google Cloud Console based pipeline development - all development is performed locally before pushing progressively into each environment/branch. Each Search API v2 GCP project/environment has a seperate Dataform Release configuration which maps onto the corresponding branch (`integration`, `staging` and `main`).

## Development and deployment workflow

This repo follows an unusual workflow to accommodate the integration, staging and production environments in Dataform having fixed, named, deployment branches:

- The integration environment deploys from the branch `integration`.
- The staging environment deploys from the branch `staging`.
- The production environment deploys from the branch `main`.

 This is part of the Dataform set up, and means that we can't easily run a typical CI/CD workflow, where we have the flexibility in integration to test any feature branch and also deploy from main. This workflow tries to follow a typical workflow as closely as possible, whilst allowing us to test changes on integration.

### Step 1: Develop and test a new feature

1.1.  Make changes in a feature branch.

1.2. Force-push the feature branch to the `integration` branch (which will be created if it doesn't exist).

```
git push --force origin my-feature:integration
```

1.3. Deploy the changes to integration: Click the button "New compilation" in the [integration Release configuration details](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/integration?project=search-api-v2-integration) page.

1.4. Manually run the workflow(s): Click the button "Start execution" in the [integration Releases and scheduling](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/release-scheduling?project=search-api-v2-integration) page. Choose the "Default Dataform service account" from the options. Under "select actions to execute", select the actions that you want to test (usually these will be the files you have made changes to).

<img width="556" height="849" alt="image" src="https://github.com/user-attachments/assets/995a37e2-f194-4999-8dcd-8f10c14b5bfa" />

1.5. Check in the [workflow execution logs](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/details/workflows?project=search-api-v2-integration) that the workflow(s) has run successfully, and that the results in BigQuery are as expected.

### Step 2: Code review

2.1. Open a pull request from the feature branch into the `main` branch.

2.2. Any substantial changes made following code review should be re-tested on integration, using steps 1.2-1.5 above.

2.3. Once approved, merge the pull request from the feature branch into the `main` branch.

### Step 3: Deployment

#### Integration

3.1. Force-push the `main` branch into the `integration` branch.

```
git push --force origin main:integration
```

3.2. Deploy the changes to integration: Click the button "New compilation" in the [integration Release configuration details](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/integration?project=search-api-v2-integration) page.

#### Staging

3.3. Force-push the `main` branch into the `staging` branch (which will be created if it doesn't exist).

```
git push --force origin main:staging
```

3.4.  Deploy the changes to staging: Click the button "New compilation" in the [staging Release configuration details](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/staging?project=search-api-v2-staging) page.


#### Production

3.5.  Deploy the changes to production: Click the button "New compilation" in the [production Release configuration details](https://console.cloud.google.com/bigquery/dataform/locations/europe-west2/repositories/search_api_v2/release-configurations/production?project=search-api-v2-production) page.

## Documentation

See the [Google Dataform documentation](https://cloud.google.com/dataform/docs/overview).

## Team

GOV.UK Search team looks after this repo. If you're inside GDS, you can find us in [#govuk-search](https://gds.slack.com/channels/govuk-search)
