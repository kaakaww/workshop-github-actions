# Workshop Cheat Sheet: Automating Application Security Testing in GitHub Actions

This workshop is designed to help you get started with application security testing in GitHub Actions. Participants get hands-on experience with

* GitHub Actions workflows
* Dependabot software composition analysis (SCA)
* CodeQL static application security testing (SAST) scanning
* StackHawk dynamic application security test (DAST) scanning

## Prerequisites

To get the most out of this workshop, make sure you have the following prerequisites before getting started.

* Discord
  * [Install](https://discord.com/) Discord
  * [Join](https://discord.gg/2XsFrkMVdc) the GitNation Server
  * Join us in **#oct12-js-security-testing** – it's under the ⚡VUE.JS FREE WORKSHOPS category
* Docker -- [install](https://docs.docker.com/get-docker) the latest version
* HawkScan -- ```docker pull stackhawk/hawkscan```
* The GitHub CLI
  * [Install](https://github.com/cli/cli#installation) the CLI (`gh`)
  * Login -- ```gh auth login```

## 1: Continuous Integration Workflows in GitHub Actions

Fork the [`vuln_node_express`](https://github.com/kaakaww/vuln_node_express) app repository with the GitHub CLI:

```shell
gh repo fork kaakaww/vuln_node_express
```

...or from the website:

```shell
open https://github.com/kaakaww/vuln_node_express
```

Clone it to your workstaion with the GitHub CLI:

```shell
gh repo clone vuln_node_express
```

...or with `git`:

```shell
git clone <YOUR-GITHUB-ORG>/vuln_node_express
```

Enter your cloned project directory:

```shell
cd vuln_node_express
```

Create a file, `.github/workflows/build-and-scan.yml`, in your project repo with the following contents:

```yaml
# .github/workflows/build-and-scan.yml
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

Push your changes to GitHub:

```shell
git add .
git commit -m "Added an Actions workflow"
git push
```

## 2: Dependency Scanning with Dependabot

See this guide to enable Dependabot easily:
<https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/enabling-and-disabling-version-updates>

## 3: Static Code Analysis with CodeQL

See this guide to enable CodeQL in Actions
<https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/setting-up-code-scanning-for-a-repository#setting-up-code-scanning-using-actions>

## 4: Dynamic App Scanning with StackHawk 🦅

Sign up for a StackHawk Developer account. Follow the Getting Started flow to create your first application and StackHawk API key.

```bash
open https://app.stackhawk.com
```

Stash your StackHawk API key in GitHub Secrets:

```shell
gh secret set HAWK_API_KEY --repos="vuln_node_express"
```

Update Actions workflow, adding the StackHawk Action:

```yaml
# .github/workflows/build-and-scan.yml
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
