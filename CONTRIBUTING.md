# Contributing

Thanks for contributing to this project.

## Development Flow

1. Install the required tooling: Go 1.26+, Node.js, Docker, Just, and Git.
2. Bootstrap the repository:

```sh
just init
just web-install
```

3. Create a feature branch and make your changes.
4. Run the quality checks before opening a pull request:

```sh
just fmt
just lint
just test
just build
```

5. Open a pull request against `main`.

## Commit Messages

This repository follows the [Conventional Commits](https://www.conventionalcommits.org/) format.

```text
<type>(<optional scope>): <subject>
```

Common commit types:

- `feat`
- `fix`
- `docs`
- `refactor`
- `test`
- `build`
- `ci`
- `chore`
- `style`
- `revert`

Guidelines:

- keep the subject lowercase
- avoid a trailing period
- keep the line concise

Use `!` when the change is breaking, for example `feat!: replace legacy redirect flow`.

## Branching

- `main` is the default branch
- all changes should land through pull requests
- release tags should follow semantic versioning such as `v1.2.3`

## Release Process

Typical release flow:

```sh
git checkout main
git pull --ff-only
just changelog
git tag -a v1.2.3 -m "v1.2.3"
git push origin v1.2.3
```

You can also build local release archives with:

```sh
just --set VERSION v1.2.3 release-binaries
```

## Quality Expectations

Prefer simple, readable, testable solutions. Keep pull requests focused, verify behavior with tests where appropriate, and avoid unrelated refactors in the same change set.
