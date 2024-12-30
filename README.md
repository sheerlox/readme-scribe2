# readme-scribe2

A GitHub Action that automatically generates & updates markdown content (like your README.md)

## Instructions

1. Create a [markscribe2](https://github.com/sheerlox/markscribe2) template and
place it anywhere in a repository that you automatically want to update. In this
guide we will use `templates/README.md.tpl`.

You can find an [example template on the `markscribe2` repository](https://github.com/sheerlox/markscribe2/blob/main/templates/github-profile.tpl).

2. In order to access some of GitHub's API, you need to provide a valid GitHub
token as a secret called `PERSONAL_GITHUB_TOKEN`. You can create a new token by
going to your profile settings:

`Developer settings` > `Personal access tokens` > `Generate new token`

Depending on your template you will need access to different API scopes. If you
want to support the full set of features, tick the checkboxes next to these
scopes: `read:user`, `repo:status`, `public_repo`, `read:org`. Check out the
[markscribe2](https://github.com/sheerlox/markscribe2) documentation for a detailed
list of required scopes for each individual template function.

Now create a new secret in your repository's `Settings` and enter that token.

3. Create a new GitHub workflow in your repository: `.github/workflows/readme-scribe.yml`

```yml
name: Update README

on:
  push:
  schedule:
    - cron: "0 */1 * * *"

jobs:
  markscribe:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@main

      - uses: sheerlox/readme-scribe2@main
        env:
          GITHUB_TOKEN: ${{ secrets.PERSONAL_GITHUB_TOKEN }}
        with:
          template: "templates/README.md.tpl"
          writeTo: "README.md"

      - uses: stefanzweifel/git-auto-commit-action@v4
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          commit_message: Update generated README
          branch: main
          commit_user_name: readme-scribe 🤖
          commit_user_email: actions@github.com
          commit_author: readme-scribe 🤖 <actions@github.com>
```

Careful: if you use `master` instead of `main` as the default branch, you will
need to update the above config for `git-auto-commit-action` accordingly.

This action will be triggered once per hour, parses `templates/README.md.tpl`
and generates a new `README.md` for you, and eventually pushes the changes to
the `main` branch. Make sure to adjust the input values `template` and
`writeTo` to suit your needs.

## Example output

![readme-scribe2 example output](https://github.com/sheerlox/readme-scribe2/raw/main/assets/template_example.png)
