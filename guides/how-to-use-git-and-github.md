---
name: how-to-use-git-and-github
when_to_use: When the user wants to use Git or GitHub from Magic — cloning, committing, pushing, branching, or creating/deleting repositories.
---

# How to use Git and GitHub

Magic contains a range of Git tools (listed below). Before you can use Git or GitHub, you must add configuration settings resembling the following:

```json
{
  "magic": {
    "git": {
      "github": {
        "username": "YOUR_GIT_AND_GITHUB_USERNAME",
        "token": "github_pat_YOUR_GITHUB_TOKEN_HERE",
        "host": "github.com"
      }
    },
   /* ... rest of file ... */
```

Exchange the `username` and `token` parts above with your own. To get a token, go to "https://github.com/settings/personal-access-tokens" and create a new token. Create it with access to _"All repositories"_, and grant it both read and write permissions to _"Administration"_ and _"Contents"_.

Below are the available Git/GitHub tools:

* `git-add-remote` adds a remote upstream to the specified local Git repository
* `git-branch-list` lists all branches in a local Git repository
* `git-checkout-branch` checks out a specific Git branch
* `git-clone-repo` clones a specified Git repo
* `git-commit` creates a new local commit
* `git-create-repo` creates a new Git repository
* `git-delete-repo` deletes an existing repo
* `git-fetch` uses git fetch to update a local repo
* `git-pull` pulls a Git repo
* `git-push` pushes upstreams
* `git-status` returns status of an existing Git repo
* `github-create-repo` creates a new GitHub repo or project
* `github-delete-repo` deletes an existing GitHub repo/project
