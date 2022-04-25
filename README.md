# action-heroku-deploy

A GitHub action for efficient Heroku Deploys and Releases

## Why

Because [Heroku stopped](https://status.heroku.com/incidents/2413) their GitHub integration. Even then, it was not great and lacked enough controls.
The property that sets this action apart from others in the market is its "zero-waste" approach. See, to be able to use the `git push heroku` thing,
you need to download the entire git history of your repo with `actions/checkout` and then push all of that to Heroku, just get the tip of it to be
deployed. This action generates a tarball URL for your repo for the given ref on the fly via GitHub API, then sends that directly to Heroku API so
there are no useless bits going around.

## How

To be able to use this action, you need to create a bot account that can deploy to the Heroku app you want. Keep in mind that even if you put the
API key generated for the account into repo secrets, anyone with write access to the repo can theoretically access them so make sure permissions
are set accordingly. Then do the following:

1. Create a workflow for your repo
1. Add necessary steps such as build, test, etc.
1. Add this action as a next step: `formsort/action-heroku-deploy@v1`
1. Add the following inputs:
   - `github_repo: ${{ github.repository }}`
   - `github_token: ${{ github.token }}`
   - `heroku_api_key: ${{ secrets.HEROKU_API_KEY }}`
   - `heroku_app_name: <your Heroku app name>`
   - `ref: ${{ github.event_name == 'pull_request' && github.event.pull_request.head.sha || github.sha }}`

Alternatively you can use a separate `heroku_app_version` to set your Heroku app's version differently from your git ref.

## What

This action creates the build, follows its logs, then follows the release logs triggered by the build. It provides the following outputs:

1. `source_tarball`: The link to the source tarball that is sent to Heroku. Note that for private repos, these links will expire after 5 minutes [per GitHub's docs](https://docs.github.com/en/rest/repos/contents#download-a-repository-archive-tar).
1. `build`: A JSON string that contains the finished build information from the Heroku API
1. `build_id`: The id of the created Heroku build for convenience.
1. `build_log`: The logs for the created Heroku build
1. `release`: A JSON string that contains the finished release information from the Heroku API
1. `release_id`: The id of the created Heroku release for convenience.
1. `release_log`: The logs for the created Heroku release
