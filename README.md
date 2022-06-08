# Workshop Guidebook: Automating Application Security Testing in GitHub Actions

This workshop is designed to help you get started with application security testing in GitHub Actions. Participants get hands-on experience with:

* GitHub Actions workflows
* Dependabot software composition analysis (SCA)
* CodeQL static application security testing (SAST) scanning
* StackHawk dynamic application security test (DAST) scanning

You can find the slide deck for this workshop [here](https://docs.google.com/presentation/d/1875pG-f2LRt9e1AlLjwQvPg6CyqiPeGqQ4_dQdzkkoo/edit?usp=sharing).

Not attending our workshop right now? [Watch it](https://www.youtube.com/watch?v=TI7E14vYWtU) on your own schedule.

## Prerequisites

To get the most out of this workshop, make sure you have the following prerequisites before getting started.

* Discord
  * [Join the StackHawk Discord server](https://discord.gg/89DcM3pz)!
  * Find us in the **[#js-security-testing-in-github-actions](https://discord.com/channels/854097000261222410/940640557426946058)** channel
* GitHub - [Sign up](https://github.com/signup) if you don't have an account

## Step 1: Continuous Integration Workflows in GitHub Actions

Fork the [`vuln_node_express`](https://github.com/kaakaww/vuln_node_express) app:

<https://github.com/kaakaww/vuln_node_express>

Go to the **Code** section of your newly forked repository in GitHub. Create a new file using the **Add file --> Create new file** button. Name the file `.github/workflows/build-and-test.yml`, and add the following contents:

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Node.js 14.x
        uses: actions/setup-node@v3
        with:
          node-version: 14
          cache: npm
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
```

Commit the change.

Go to the **Actions** section of your repository, and you should see the new workflow running.

## Step 2: Dependency Scanning with Dependabot

Go to the **Settings** section of your repo, and find the **Code security & analysis** section in the left pane. Enable the **Dependency graph**, **Dependabot alerts**, and **Dependabot security updates** features in this section. Dependabot is now configured.

Go to the **Security** section of your GitHub repo, and click into the **Dependabot alerts** on the left pane. Examine some of the dependency alerts, and see if you can resolve them.

## Step 3: Static Code Analysis with CodeQL

Go to the **Security** section of your repo. Click on **Set up code scanning**. Click the big green button to **Configure CodeQL alerts**.

Examine the GitHub Actions workflow, `.github/workflows/codeql-analysis.yml`, and commit it to the repo.

Now go to the **Actions** section of your repo, and watch your new CodeQL workflow run.

When CodeQL has finished, examine the results in the **Security** section under **Code scanning alerts** in the left pane.

## Step 4: Dynamic App Scanning with StackHawk 🦅

[Sign up](https://app.stackhawk.com) for a StackHawk Developer account. When prompted, select **Scan My Application**. Follow the Get Started flow to create your StackHawk API key and first application.

### Create a StackHawk API Key

When you first log on to the StackHawk web app, it will prompt you to create and save an API key so that the scanner can send results back to the platform.

Stash your new StackHawk API key in GitHub Secrets. In your repo, navigate to the **Settings** section, and find **Secrets → Actions** in the left pane.

Add a secret named `HAWK_API_KEY`, and add your StackHawk API key as the value.

### Create your First Application in StackHawk

After creating your StackHawk API key, the StackHawk web app will prompt you to create your fist app. Enter the details about your new application using the name, `vuln_node_express`, an environment of: `Development`, and a host url of: `http://localhost:3000`.

For the Application Type, select **Dynamic Web Application**. And for the API Type, select **Other**.

### Commit the `stackhawk.yml` Configuration File

Download the `stackhawk.yml` file that you created in the Get Started flow in the StackHawk platform. Copy the contents into a new file at the base of your repo named `stackhawk.yml`. Commit the file.

### Add a StackHawk Scan to your Build and Test Workflow

Update your Build and Test workflow. Add a step to start the `vuln_node_express`, and a step to run HawkScan using the StackHawk Action at the end:

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Node.js 14.x
        uses: actions/setup-node@v3
        with:
          node-version: 14
          cache: npm
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
        
        ### NEW STEPS BELOW! ###
        
      - name: Daemonize our Node API service
        run: npm run start &
      - name: Run HawkScan
        uses: stackhawk/hawkscan-action@v2.0.0
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
```

Commit this change.

### Check your Scan Results

Go to the **Actions** section of your repo, and watch your updated Build and Test workflow run. Examine the **Run HawkScan** step console logs.

[Check your scan results](https://app.stackhawk.com/scans) on the StackHawk platform.

### Set a HawkScan Failure Threshold

To make the HawkScan break your build for high severity alerts, add the following section to the end of your `stackhawk.yml` configuration file at the root of your repository.

```yaml
hawk:
  failureThreshold: high
```

Rerun your GitHub Actions workflow, and check to make sure the scan succeeds and the Action run fails due to your new failure threshold being exceeded. Triage all high severity alerts in the StackHawk platform. After triaging all high severity alerts, you should be able to rerun the Actions workflow, and the workflow should succeed.

## Workshop Complete

You just automated SCA, SAST, and DAST scanning with GitHub Actions!

Read more about [GitHub Actions](https://docs.github.com/en/actions), [CodeQL](https://codeql.github.com/docs/), and [Dependabot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/configuration-options-for-dependency-updates). And check out the [GitHub Actions Marketplace](https://github.com/marketplace?type=actions), where you can find other Actions to build out your pipeline.

Finally, here are some additional resources for tuning StackHawk to scan *your* applications.

* [HawkDocs](https://docs.stackhawk.com), where you can read all the details on how to configure and run HawkScan in your environment.
* [Authenticated Scanning](https://docs.stackhawk.com/hawkscan/authenticated-scanning.html) - Guides for authenticating HawkScan to your application for deeper scans.
* [Continuous Integration](https://docs.stackhawk.com/continuous-integration/), where you can see our guides for integrating HawkScan with the most popular CI/CD systems.
* [StackHawk Blog](https://www.stackhawk.com/blog), with technical tips, tricks, and walkthroughs to help you secure and test your applications.
