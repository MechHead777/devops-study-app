# devops-study-app

A study time tracker. You enter the topic you've been studying and how long you spent on it, hit enter, and it appends the entry to the page. Entries are stored in a CSV.

**The app itself is deliberately simple. The point of this repo is the pipeline around it, which is where the majority of work has been done.**

The web frontend listens on `localhost:22111` and the backing API listens on `localhost:22112`.

## Running it locally

First, open the repo in the dev container. Everything below assumes you're inside it, since the toolchain is pinned there and not on your host.

Then install the runtimes and dependencies:

After that, start the API and the frontend with docker

Finally, open `http://localhost:22111` in a browser. Enter a topic and a duration, and hit enter. The entry gets written to the CSV and appended to the page.

     If the page loads but entries don't persist, it's often because the API on 22112 isn't running, so check that before assuming the CSV is at fault.

## What's in the pipeline

Everything in this section is live.

| Piece | What it does |
|---|---|
| Dev containers / DevPod | Reproducible environment, so the toolchain is identical everywhere |
| Docker-in-Docker | Lets the container run its own containers, which is what k3d will need |
| mise | Pins the runtimes |
| uv | Python dependency management |
| ruff | Linting and formatting |
| pre-commit | Runs the checks before anything gets committed |
| commitizen | Keeps commit messages in conventional-commit format |
| trivy | Scans for common vulnerabilities and exploits |
| GitHub Actions | Lints, tests, builds the image and pushes it to GHCR on tag |
| release-please | Reads the commit history, opens a release PR, and handles versioning |

## How changes get in

Every feature or addition goes in on its own branch and comes in through a pull request. The checks have to pass before it can merge, and then it gets squashed and merged into main.

Because it's squashed, the pull request title becomes the only commit message on main, and that title is what release-please reads. So the commits on the branch itself can be as messy as you like, but the PR title has to be a valid conventional commit.

## Still to come

Deployment happens out of a separate GitOps repo rather than from here, so this repo's job ends once the image is published to GHCR. Flux picks it up from there and applies it to the cluster.

Before that, changes get tested against k3d running inside the dev container using docker-in-docker.

