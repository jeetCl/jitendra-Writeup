
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

