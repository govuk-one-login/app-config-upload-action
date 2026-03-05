# App Config Upload Action

This repository contains a specialised GitHub Action for uploading a configuration file to S3 for use in the configuration pipeline.

This package signs, zips and pushes the config file (named `config.zip`) to the designated AWS account

## Usage Instructions

### Action inputs

| Input                     | Required | Description                                                                        | Example                                                                |
|---------------------------|----------|------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| aws-role-arn              | true     | The AWS role the action will assume before uploading the file                      | arn:aws:iam::123456789000:role/role-name                               |
| aws-region                | false    | The name of the region to use when authenticating with AWS. Default is `eu-west-2` | eu-west-2                                                              |
| config-file               | true     | The name of the configuration file to upload                                       | config.yaml                                                            |
| config-zip-name           | false    | The name of the zipped configuration pushed to s3                                  | config.zip                                                             |
| artefact-signing-key      | true     | The ARN of the signing key used to sign the configuration file                     | arn:aws:kms:eu-west-2:123456789000:key/12345678-a1b2-3bc5-12a3b45c67d6 |
| config-source-bucket-name | true     | The name of the S3 bucket where the configuration file will be stored              | config-pipelinebucket-abcdef1ghijk                                     |
| environment               | true     | The environment to upload the file to. Defaults to `dev`                           | dev                                                                    |
| working-directory         | false    | Path to folder containing the config file                                          | .                                                                      |

### Usage Example

Pull the action into your workflow like in the example below, specify the version you want to use

```YAML
- name: Sign and upload config file
  uses: govuk-one-login/app-config-upload-action@<version_number>
  with:
    aws-role-arn: ${{ secrets.AWS_ROLE_ARN }}
    config-file: config.yaml
    config-zip-name: config.zip
    artefact-signing-key: ${{ secrets.SIGNING_KEY_ARN }}
    config-source-bucket-name: ${{ secrets.CONFIG_SOURCE_BUCKET_NAME }}
    environment: dev
    working-directory: config
```

### Requirements

- Pre-commit needs to be installed
- ```bash
    brew install pre-commit
    pre-commit
    ```
### Releasing Updates
We follow recommended best practices for releasing new versions of the action.

#### Non-breaking changes
Release a new minor or patch version as appropriate. Then, update the base major version release (and any minor versions) to point to this latest commit. For example, if the latest major release is v2, and you have added a non-breaking feature, release v2.1.0 and point v2 to the same commit as v2.1.0.

#### Breaking changes
Release a new major version as normal following semantic versioning.

#### Bug fixes
Once your PR is merged and the bug is fixed, make sure to float tags affected by the bug to the latest stable commit.

For example, let's say commit `abcd001` introduced a bug and is tagged with `v2.3.1`. You then merge commit `dcba002` with a fix to your solution:

🐛 `abcd001` `v2.3.1`

✅ `dcba002`

Instead of creating a new tag for the fix, you can update the `v2.3.1` tag to the latest stable commit with the following command:

```
git tag -s -af v2.3.1 dcba002
git push origin v2.3.1 -f
```
🐛 `abcd001`

✅ `dcba002` `v2.3.1`

This will make sure users benefit from the fix immediately, without the need to manually bump their action version.

#### How to release an update
When working on a PR branch, create a release with the target version, but append -beta to the post-fix tag name.

e.g.

`git tag v3.1-beta`

You can then navigate to the release page, and create a pre-release to validate that the tag is working as expected. After you've merged the PR, then apply the correct tag for your release.

Please ensure all pre-release versions have been tested prior to creation, you are able to do this via updating uses: property within a GitHub actions workflow to point to a branch name rather than the tag, see example below:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    permissions:
      id-token: write
      contents: read
    steps:
      - name: Upload and tag
        uses: govuk-one-login/app-config-upload-action@<BRANCH_NAME>
```
