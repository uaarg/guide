# Workflows

## Git Branching Model

We use a modified version of [git
flow](https://nvie.com/posts/a-successful-git-branching-model/).

This means all development is seperated into features. Each feature is a
collection of related changes. For example, a feature may fix a *single* known
bug or a faeture may add a new screen to a GUI app.

Work on these features is structured as follows:

1. The contributor (you) creates a branch and opens a "WIP" PR
2. Contributor works on the feature (team leads and/or team members help where needed)
3. Contributor asks for a review
4. The team lead reviews the code
5. If changes are needed, then we go back to (2)
6. We merge the changes into `dev`

Periodically, `dev` will be heavily tested. This will happen when one or more
new features have been merged into `dev`. If any issues are found, fixes are
merged. Finally, when `dev` is beleived to be stable enough, it is merged into
`main`.

This process is in place to ensure that `main` always contains a stable copy of
the code. This is important for regular test flights which must only be done on
tested stable code.
