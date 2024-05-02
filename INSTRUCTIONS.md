# Instructions for Technical Assessment
Creating a technical assessment base repository is simple. Follow along with the steps below to 
learn more.

## Update the README.md
The `README.md` file is the first thing the candidate will see when they open the repository. This
template includes a basic README.md file. You can update the file to include any information you
want the candidate to see.

## Using the .copyignore
The `.copyignore` file is used to ignore files and directories when copying the base repository 
to the candidate's repository. This is useful for ignoring files that are not needed for the 
assessment. For example, you want to ignore the `.github` directory so that the candidate does 
not have access to the GitHub Actions workflows.

## Adding your base repository to Webhook Actions
There are two ways to add your base repository to Webhook Actions. For additional information see
the [documentation](https://github.com/rise8-us/webhook-actions-docs).
### Add to `webhook-actions-config` repository
If your organization has a `webhook-actions-config` repository, then you must add your configuration
to the `webhook-actions-config/repositories.json` file. See the Webhook Actions documentation for 
more details.

```yaml
{
  "technical-assessment-{{ type }}": {
    "events": {
      "pull_request_opened": {
        "filter": {
          "repository.name": {
            # The following regex will match any repository that starts with "technical-assessment-{{ type }}-"
            "$regex": "^technical-assessment-{{ type }}-.*$"
          }
        }
      },
      "pull_request_review_submitted": {
        "filter": {
          "pull_request.base.ref": "candidate",
          # The following regex will match your base repository
          "repository.name": "technical-assessment-{{ type }}",
          "review.state": {
            "$in": [
              "approved",
              "changes_requested"
            ]
          }
        }
      }
    }
  }
}
```

### Add to base repository
You can add your base repository to Webhook Actions by adding the following to your base repository's
`.github/webhook-actions/config.json`

```yaml
{
  "events": {
    "pull_request_opened": {
      "filter": {
        "repository.name": {
          # The following regex will match any repository that starts with "technical-assessment-{{ type }}-"
          "$regex": "^technical-assessment-{{ type }}-.*$"
        }
      }
    },
    "pull_request_review_submitted": {
      "filter": {
        "pull_request.base.ref": "candidate",
        # The following regex will match your base repository
        "repository.name": "technical-assessment-{{ type }}",
        "review.state": {
          "$in": [
            "approved",
            "changes_requested"
          ]
        }
      }
    }
  }
}
```

## Dev container
The `.devcontainer` directory contains the configuration for the GH Codespaces development 
container. This is useful if you want to provide the candidate with a development environment
that has all the tools they need to complete the assessment. Please see the GitHub Devcontainer 
[documentation](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers)

## Automated assessment tests
Please add your traditional workflow files to the `.github/workflows` directory. The automated
assessment will run the workflow files in this directory. There is an included `tests.yml` file
to help you get started.

## Branches
The `candidate` branch is the branch that the candidate will be working on. The `main` branch is
the branch you will use to update the base repository.

## Branch protection
Add branch protection for the `candidate` branch with the following rules:
- Branch name pattern: `candidate`
- Require pull request reviews before merging
  -  Require Approvals: 1
- Allow specified actors to bypass required pull requests: {{ bot service account }}
- Require status checks to pass before merging
- Require branches to be up-to-date before merging
- Lock branch

Add branch protection to the `main` branch as you normally would.


## Reviewing Candidate Pull Requests
A CODEOWNERS file has been added to the base repository. This file will automatically add all the 
collaborators to the pull request review. You can add org teams as well. This will only occur if 
your branch protection rules require pull request reviews from the CODEOWNERS, so ensure that you 
have set that branch protection rule.

When reviewing a candidates pull request, please use the following guidelines:
- Review the candidate's code and provide feedback
- If the candidate's code is acceptable, approve the pull request. This will move the candidate to
  the next step in the hiring pipeline.
- If the candidate's code is not acceptable, request changes and provide feedback. This will
  disqualify the candidate.


## Tech Docs
The `tech-docs` directory contains documentation for the technical assessment. You do not need to 
keep this directory. It is only available so that you may understand how the technical assessment
automation works.
