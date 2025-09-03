Project Overview

This project demonstrates the integration of Snyk Software Composition Analysis (SCA) into a CI/CD pipeline using GitHub Actions for a Maven-based Java application. The goal is to identify and manage open-source dependency vulnerabilities as part of a mature DevSecOps practice, ensuring security is embedded early in the software development lifecycle (Shift-Left security).

Implementation Highlights for Mature DevSecOps

Automated SCA Scan with Snyk:

Detects known vulnerabilities in open-source libraries during every push, pull request, or manual trigger.

Custom Quality Gates:

Pipeline enforces security thresholds:

Fail build if any Critical vulnerabilities exist.

Fail build if High severity > 5.

Centralized Reporting:

Uploads SARIF reports to GitHub Security tab for visibility.

Sends results to Snyk Dashboard for centralized vulnerability management.

Continuous Monitoring:

Snyk Monitor enables ongoing tracking of dependencies for new vulnerabilities post-build.

Scalable & Secure CI/CD:

Uses GitHub Actions secrets for API tokens.

Supports multi-module Maven projects without requiring a buildStep 1: Create a Snyk Account

Go to https://snyk.io
 and sign up (or log in).

After login, go to Account Settings → API Token.

Copy your API Token (this is your SNYK_TOKEN).

 Step 2: Add SNYK_TOKEN to GitHub Secrets

Navigate to your repo → Settings → Secrets and variables → Actions.

Click New repository secret:

Name: SNYK_TOKEN

Value: (paste your Snyk API token)

Save it.

 Step 3: Add Vulnerable Dependency (for Testing)

Edit your pom.xml (or sub-module) and add a known vulnerable dependency like:

<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>vulnerable-sample</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <name>Vulnerable Sample Project</name>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Old Gson version (low severity issues) -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.2.4</version>
        </dependency>

        <!-- Old Commons IO (low severity issue) -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>

        <!-- Old JUnit (low severity CVEs) -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>


Commit and push.

 Step 4: Create GitHub Actions Workflow

In your repo, create this file:

.github/workflows/snyk-security-scan.yml


Paste the following:

name: Snyk Security Scan

on:
  workflow_dispatch: # Manual trigger
  push:
    branches:
      - main
  pull_request:

jobs:
  snyk:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: . # root (where pom.xml exists)

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Install Snyk
        run: npm install -g snyk

      - name: Authenticate Snyk
        run: snyk auth ${{ secrets.SNYK_TOKEN }}

      #  Run Snyk test and save JSON for quality gate check
      - name: Snyk Test (JSON + SARIF)
        id: snyk-test
        run: |
          snyk test --file=pom.xml --json > snyk.json || true
          snyk test --file=pom.xml --sarif-file-output=snyk.sarif || true

      #  Upload SARIF report (always run)
      - name: Upload SARIF report
        if: always()
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: snyk.sarif

      #  Apply Custom Quality Gate
      - name: Apply Quality Gate (Critical > 0 OR High > 5)
        run: |
          CRITICAL=$(jq '.vulnerabilities | map(select(.severity=="critical")) | length' snyk.json)
          HIGH=$(jq '.vulnerabilities | map(select(.severity=="high")) | length' snyk.json)

          echo "Found $CRITICAL critical vulnerabilities"
          echo "Found $HIGH high vulnerabilities"

          if [ "$CRITICAL" -gt 0 ] || [ "$HIGH" -gt 5 ]; then
            echo " Quality Gate Failed: $CRITICAL critical and $HIGH high vulnerabilities"
            exit 1
          else
            echo " Quality Gate Passed: $CRITICAL critical and $HIGH high vulnerabilities"
          fi

      #  Monitor (always run, even if previous step failed)
      - name: Snyk Monitor
        if: always()
        run: snyk monitor --file=pom.xml || true

 Step 5: Trigger the Workflow

Push changes to main OR

Go to Actions tab → Select Snyk Security Scan → Click Run workflow.

 Step 6: Check Outputs

GitHub Actions Logs:

See vulnerability count.

If Critical > 0 OR High > 5, build fails.

GitHub Security tab:

SARIF report uploaded → you see issues in Code Scanning Alerts.

Snyk Dashboard:

Go to Projects → Your repo appears with vulnerabilities.

 Quality Gate Logic:

Fail if any Critical vulnerabilities exist.

Fail if more than 5 High vulnerabilities.

Pass otherwise.

 Optional: Allow other jobs to run even if this fails

Add this to the jobs.snyk-scan level:

continue-on-error: true


This will mark the job as failed but continue the workflow.

 What This Setup Provides

✔ Snyk SCA scan for Maven project
✔ Upload SARIF to GitHub Security tab
✔ Monitor results in Snyk Dashboard
✔ Custom Quality Gate enforcement
✔ Manual trigger + PR/Push automation


<img width="1867" height="575" alt="image" src="https://github.com/user-attachments/assets/af0518ce-c026-4f3d-88b8-ac8530ab0c77" />
<img width="1918" height="782" alt="image" src="https://github.com/user-attachments/assets/8619dd62-9fc4-45d9-859b-ff812395385b" />
<img width="1882" height="862" alt="image" src="https://github.com/user-attachments/assets/b9e59d78-a197-4550-bbdf-0676f7f8573d" />


Complete Project :
https://github.com/jeetCl/SCA-Synk-1


