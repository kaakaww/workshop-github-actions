# Workshop Cheat Sheet: Automating Application Security Testing in GitHub Actions

This workshop is designed to help you get started with application security testing in GitHub Actions. Participants get hands-on experience with

* GitHub Actions workflows
* Dependabot software composition analysis (SCA)
* CodeQL static application security testing (SAST) scanning
* StackHawk dynamic application security test (DAST) scanning

## 1: Continuous Integration Workflows in GitHub Actions

```yaml
name: Build and Scan
on:
  push:
  pull_request:
jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - name: Check Out Code
        uses: actions/checkout@v2
      - name: Install Node.js 14.x
        uses: actions/setup-node@v2
        with:
          node-version: 14.x
      - name: Install Dependencies
        run: npm clean-install
```

## 2: Dependency Scanning with Dependabot

See this guide to enable Dependabot easily:
https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/enabling-and-disabling-version-updates

## 3: Static Code Analysis with CodeQL

See this guide to enable CodeQL in Actions
https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/setting-up-code-scanning-for-a-repository#setting-up-code-scanning-using-actions

## 4: Dynamic App Scanning with StackHawk 🦅

Sign up for an account. Follow the Getting Started flow to create your first application and StackHawk API key.

```bash
open https://app.stackhawk.com
```

Stash API key secret in GitHub

Create initial HawkScan config `stackhawk.yml`

Update Actions workflow with the StackHawk Action

```yaml
name: Build and Scan
on:
  push:
  pull_request:
jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - name: Check Out Code
        uses: actions/checkout@v2
      - name: Install Node.js 14.x
        uses: actions/setup-node@v2
        with:
          node-version: 14.x
      - name: Install Dependencies
        run: npm clean-install
      - name: Daemonize our Node API service
        run: nohup npm run start &
      - name: Run HawkScan
        uses: stackhawk/hawkscan-action@v1.3.1
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
```

## All Done

Congratulations!

Now try it on *your* application! Here are some additional resources to help you on your way.

* [HawkDocs](https://docs.stackhawk.com), where you can read all the details on how to configure and run HawkScan in your environment.
* [Continuous Integration](https://docs.stackhawk.com/continuous-integration/), where you can see our guides for integrating HawkScan with the most popular CI/CD systems.
* [StackHawk Blog](https://www.stackhawk.com/blog), where you can find deep-dives on use cases for StackHawk.