**Built a Spring MVC application to showcase DevSecOps CI/CD integration with SonarQube, enabling automated code quality checks and security vulnerability scanning as part of the pipeline**.
### **What is SonarCloud?**

- **SonarCloud** is a cloud-based service for **continuous code quality and security analysis**.
- It automatically scans code for:
    - **Bugs**
    - **Code smells**
    - **Vulnerabilities**
    - **Security hotspots**
    - **Code coverage & duplications**
- Works with major **languages** like Java, Python, C#, JavaScript, and frameworks like Maven, Gradle, .NET, etc
### **Typical DevSecOps Pipeline with SonarCloud**

Here’s a sample **pipeline flow**:

1. **Code Development**
    - Developer writes code.
    - Optional: **SonarLint** checks in IDE.
2. **Pre-commit / PR Scan**
    - CI pipeline triggers SonarCloud scan.
    - Checks **Quality Gate** (fails build if thresholds not met).
3. **Build & Test**
    - Build artifacts created.
    - Unit tests and integration tests run.
4. **SonarCloud Analysis**
    - Scan for vulnerabilities, bugs, security hotspots.
    - Results shown in **SonarCloud dashboard**.
5. **Merge / Deployment**
    - Only code passing **Quality Gate** moves to production.

## **SonarCloud Account Setup**

1. **Sign Up / Login** https://www.sonarsource.com/products/sonarcloud/signup/
    -

    - You can sign up using:
        - **GitHub**
        - **Bitbucket**
        - **GitLab**
        - **Azure DevOps**
    - For DevSecOps pipelines, **GitHub is the most common**.
2. **Organization Creation**
    - After login, create an **organization**:
        - Name: e.g., `my-org`
        - Link it to your GitHub/Bitbucket account.
    - Organization manages multiple projects.

Note: SonarCloud is SaaS, so no local server installation is needed. Only scanner installation in your CI/CD is require

 Go to SonarCloud.

 <img width="1851" height="868" alt="30" src="https://github.com/user-attachments/assets/d23044eb-241b-4701-9623-7cb752c12b14" />
 <img width="1471" height="685" alt="31" src="https://github.com/user-attachments/assets/e9519143-12d4-4360-aaa1-97da3a1f891b" />

<img width="1520" height="728" alt="35" src="https://github.com/user-attachments/assets/57987afe-c923-4e01-afcc-67b6181db43a" />
<img width="1517" height="743" alt="34" src="https://github.com/user-attachments/assets/2f7a81eb-f52e-451f-a601-1e3df32f0e47" />
<img width="1375" height="600" alt="33" src="https://github.com/user-attachments/assets/e852240d-ca77-48c9-8a4d-8a554b2fea4e" />
<img width="1518" height="568" alt="39" src="https://github.com/user-attachments/assets/60bbb3d8-c5b5-47c1-8d21-686343523b16" />
<img width="1493" height="780" alt="38" src="https://github.com/user-attachments/assets/1d876775-3cab-4314-a014-4ab7f2f54e61" />
<img width="1448" height="685" alt="37" src="https://github.com/user-attachments/assets/1cf7a223-ef07-45c9-95b3-f230a517df19" />
<img width="1491" height="592" alt="36" src="https://github.com/user-attachments/assets/8f411ed6-967f-4e74-9c4d-17a9b0defdef" />

## 
3. **Create a Project**
- Connect a repository (GitHub repo, Bitbucket, GitLab, Azure Repo).
- Select your project and programming language.
- SonarCloud will generate a **project key** and instructions for integration
<img width="1535" height="813" alt="45" src="https://github.com/user-attachments/assets/1225760a-68a3-45f9-924c-d1a8df2c2860" />
<img width="1535" height="486" alt="40" src="https://github.com/user-attachments/assets/f747fec2-e271-44c3-9b86-06c3c11c87bc" />


## **Create GitHub Workflow `.github/workflows/build.yml`**

1. Create folder `.github/workflows` in your repo.
2. Create file `build.yml`

![46.png](attachment:f74bbe93-14b7-45d5-a3b5-b29ea02e4c86:46.png)

## **Add Token as GitHub Secret**

1. Go to your GitHub repository.
2. Click **Settings → Secrets and variables → Actions → New repository secret**.
3. Add:
    - **Name:** `SONAR_TOKEN`
    - **Value:** Paste the token you copied from SonarCloud
4. Click **Add secret**.

> GitHub will store it securely, and it won’t be visible in workflow logs.
><img width="1475" height="670" alt="48" src="https://github.com/user-attachments/assets/080c9e2b-3a2a-41a3-ba4a-395348473688" />
<img width="1451" height="750" alt="47" src="https://github.com/user-attachments/assets/444449c8-fbeb-48bf-8179-0e02998fc49a" />
Add Below code into build.yml file

name: Sonar Scan

on:
  push:
    branches:
      - master
  pull_request:
    types: [opened, synchronize, reopened]
  workflow_dispatch: #  Added manual trigger

jobs:
  build:
    name: Build and Analyze with SonarCloud
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history is needed for better analysis

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: 17
          distribution: 'zulu'

      - name: Cache SonarCloud packages
        uses: actions/cache@v4
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar
          restore-keys: ${{ runner.os }}-sonar

      - name: Install Sonar Scanner
        run: |
          curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
          unzip sonar-scanner.zip -d $HOME
          export PATH=$HOME/sonar-scanner-5.0.1.3006-linux/bin:$PATH
          echo "$HOME/sonar-scanner-5.0.1.3006-linux/bin" >> $GITHUB_PATH

      - name: Build with Ant
        run: |
          ant -f AntSpringMVC/build.xml

      - name: Run Sonar Scanner
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        run: |
          sonar-scanner \
            -Dsonar.projectKey=jenkins-testing-antspringmvc \
            -Dsonar.organization=jeetcl \
            -Dsonar.sources=AntSpringMVC/src \
            -Dsonar.tests=AntSpringMVC/src/com/mkyong/test \
            -Dsonar.java.binaries=AntSpringMVC/war/WEB-INF/classes \
            -Dsonar.java.libraries=AntSpringMVC/lib/*.jar \
            -Dsonar.junit.reportPaths=AntSpringMVC/report/TEST-*.xml \
            -Dsonar.coverage.jacoco.xmlReportPaths=AntSpringMVC/target/jacoco.xml \
            -Dsonar.jacoco.reportPaths=AntSpringMVC/target/jacoco.exec \
            -Dsonar.host.url=https://sonarcloud.io \
            -Dsonar.login=${{ secrets.SONAR_TOKEN }}

<img width="1718" height="868" alt="image" src="https://github.com/user-attachments/assets/cc7275d2-7f51-4ebd-a753-e3b2f2565ea5" />

## **Verify Workflow Execution in GitHub**

1. Go to your repository on GitHub.
2. Click on **Actions** tab.
3. You will see a list of workflow runs.
    - Look for the workflow name defined (e.g.,`SonarQube Analysis`).
4. Click on the latest run.
5. Check the **status of each step**:
    - ✅ Green checkmark → step succeeded
    - ❌ Red cross → step fail



            <img width="1535" height="631" alt="51" src="https://github.com/user-attachments/assets/9fba8738-b8c3-42c4-85d2-bc2a85200b25" />
<img width="1535" height="570" alt="50" src="https://github.com/user-attachments/assets/a38dafbc-e12b-4553-8911-126cb6494c70" />


## **Verify SonarCloud Scan Results**

1. Log in to SonarCloud.
2. Go to **Projects → Your Project**.
3. Check the **dashboard** for:
    - **Bugs**
    - **Vulnerabilities**
    - **Code smells**
    - **Security hotspots**
    - **Code coverage**
    - **Duplications**
4. **Quality Gate**:
    - If your workflow has `waitForQualityGate: true`, GitHub Actions will **pass/fail based on Quality Gate status**.
    - Otherwise, you can see **Quality Gate status in the SonarCloud project dashboard**.
  
<img width="1535" height="758" alt="53" src="https://github.com/user-attachments/assets/2502e2bb-9de5-4050-bd82-10103adbc6c7" />
<img width="1535" height="763" alt="52" src="https://github.com/user-attachments/assets/18659b13-d6e2-4f73-b7e9-071973202f8f" />

Reference - 
Github Repo "https://github.com/jeetCl/AntSpringMVC"
