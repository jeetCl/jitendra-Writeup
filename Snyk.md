Step 1: Create a Snyk Account

Go to https://snyk.io
 and sign up (or log in).

After login, go to Account Settings → API Token.

Copy your API Token (this is your SNYK_TOKEN).

✅ Step 2: Add SNYK_TOKEN to GitHub Secrets

Navigate to your repo → Settings → Secrets and variables → Actions.

Click New repository secret:

Name: SNYK_TOKEN

Value: (paste your Snyk API token)

Save it.

✅ Step 3: Add Vulnerable Dependency (for Testing)

Edit your pom.xml (or sub-module) and add a known vulnerable dependency like:

<dependency>
    <groupId>commons-collections</groupId>
    <artifactId>commons-collections</artifactId>
    <version>3.2.1</version> <!-- Known vulnerable -->
</dependency>


Commit and push.

✅ Step 4: Create GitHub Actions Workflow

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
  snyk-scan:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: . # root where pom.xml exists

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

      # ✅ Run Snyk test (JSON + SARIF for analysis)
      - name: Snyk Test (JSON + SARIF)
        id: snyk-test
        run: |
          snyk test --file=pom.xml --json > snyk.json || true
          snyk test --file=pom.xml --sarif-file-output=snyk.sarif || true

      # ✅ Upload SARIF report for GitHub Security tab
      - name: Upload SARIF to GitHub Security Tab
        if: always()
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: snyk.sarif

      # ✅ Apply custom quality gate: Fail if Critical > 0 OR High > 5
      - name: Apply Custom Quality Gate
        run: |
          sudo apt-get update && sudo apt-get install -y jq
          
          CRITICAL=$(jq '.vulnerabilities | map(select(.severity=="critical")) | length' snyk.json)
          HIGH=$(jq '.vulnerabilities | map(select(.severity=="high")) | length' snyk.json)

          echo "Found $CRITICAL Critical vulnerabilities"
          echo "Found $HIGH High vulnerabilities"

          if [ "$CRITICAL" -gt 0 ] || [ "$HIGH" -gt 5 ]; then
            echo "❌ Quality Gate Failed: $CRITICAL Critical and $HIGH High vulnerabilities"
            exit 1
          else
            echo "✅ Quality Gate Passed: $CRITICAL Critical and $HIGH High vulnerabilities"
          fi

      # ✅ Send results to Snyk Dashboard (always run)
      - name: Snyk Monitor
        if: always()
        run: snyk monitor --file=pom.xml || true

✅ Step 5: Trigger the Workflow

Push changes to main OR

Go to Actions tab → Select Snyk Security Scan → Click Run workflow.

✅ Step 6: Check Outputs

GitHub Actions Logs:

See vulnerability count.

If Critical > 0 OR High > 5, build fails.

GitHub Security tab:

SARIF report uploaded → you see issues in Code Scanning Alerts.

Snyk Dashboard:

Go to Projects → Your repo appears with vulnerabilities.

✅ Quality Gate Logic:

Fail if any Critical vulnerabilities exist.

Fail if more than 5 High vulnerabilities.

Pass otherwise.

✅ Optional: Allow other jobs to run even if this fails

Add this to the jobs.snyk-scan level:

continue-on-error: true


This will mark the job as failed but continue the workflow.

✅ What This Setup Provides

✔ Snyk SCA scan for Maven project
✔ Upload SARIF to GitHub Security tab
✔ Monitor results in Snyk Dashboard
✔ Custom Quality Gate enforcement
✔ Manual trigger + PR/Push automation
