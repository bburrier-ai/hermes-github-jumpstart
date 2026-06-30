# Hermes GitHub Jumpstart

This repository documents the setup path used to connect a local Hermes Agent environment to a sandboxed, AI-focused GitHub organization through a scoped GitHub App.

The goal was to give the agent a constrained GitHub identity that can create and manage repositories without relying on a personal access token or a human user account.

## What was set up

A GitHub App was created and installed on a sandbox organization. The app was granted only the repository permissions needed for agent-driven development workflows, such as repository contents, pull requests, issues, statuses, workflows, and actions.

A private key for the GitHub App was generated from GitHub and saved locally on the Hermes host. The private key itself is not stored in this repository and should never be committed, pasted into chat, or shared through ordinary messaging systems.

The Hermes host was then configured to use GitHub App installation authentication:

1. A short-lived JSON Web Token is signed locally with the GitHub App private key.
2. The JWT is exchanged with GitHub for an installation access token.
3. The installation token is used for GitHub API requests and HTTPS git operations.
4. Tokens are generated on demand and expire automatically.

This gives the agent a scoped bot identity rather than a long-lived personal credential.

## Local helper scripts

Several local helper scripts were created on the Hermes host:

- A token helper that generates GitHub App installation access tokens.
- A GitHub API curl wrapper that adds a fresh installation token to each request.
- A git credential helper that supplies a fresh installation token for HTTPS git operations.
- A configuration helper that records non-secret app metadata and validates the installation.

These scripts remain local to the Hermes machine. They are intentionally not included in this repository because they reference local paths and secret-bearing material.

## Local environment configuration

The Hermes environment stores only non-secret metadata and the local path to the private key.

The configuration follows this pattern:

```text
GITHUB_APP_ID=<app id>
GITHUB_APP_INSTALLATION_ID=<installation id>
GITHUB_APP_PRIVATE_KEY_PATH=<local path to private key pem>
GITHUB_SANDBOX_ORG=<sandbox org name>
```

## Git workflow

A dedicated local workspace was created for repositories belonging to the sandbox organization.

Inside that workspace, git is configured to use:

- the GitHub App bot identity as the commit author
- the local GitHub App credential helper for HTTPS remotes

The configuration is conditional, so unrelated repositories outside the sandbox workspace do not automatically receive the GitHub App credential helper.

This keeps the scoped app token from being offered to unrelated GitHub repositories.

## Verification performed

The setup was verified by checking that:

- the private key file exists locally and parses as a valid private key
- GitHub App API authentication works
- installation token generation works
- the GitHub App installation is visible to the API
- the app has the expected repository permissions
- git can use the bot identity and credential helper within the sandbox workspace
- this repository can be created and populated through the scoped app credentials

## Security notes

Do not commit private keys, installation tokens, personal access tokens, passwords, seed phrases, or any other secrets.

Do not paste private keys or credentials into chat systems, including bot-based messaging platforms.

Prefer short-lived, scoped, revocable credentials for agent workflows.

Keep the GitHub App installed only on repositories or organizations where agent access is intended.

Use local credential helpers and conditional git configuration so credentials are scoped to the intended workspace.

## Current purpose of this repository

This repository is a minimal starting point. It exists to record the bootstrap process for connecting Hermes Agent to a sandbox GitHub organization through a scoped GitHub App.

Future repositories in the sandbox organization can use the same authentication pattern for agent-driven development, issue management, pull requests, workflow updates, and repository automation.
