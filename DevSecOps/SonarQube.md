<img width="1517" height="743" alt="34" src="https://github.com/user-attachments/assets/0b6e789c-d485-41f0-b65b-1546e49479e3" />
<img width="1520" height="728" alt="35" src="https://github.com/user-attachments/assets/d0d91cb6-2aef-426d-a227-87d12aa22b8b" />
<img width="1491" height="592" alt="36" src="https://github.com/user-attachments/assets/6911d3c7-2a7e-41b2-a6e8-660936c982fd" />
<img width="1448" height="685" alt="37" src="https://github.com/user-attachments/assets/44c5fc8b-5545-4940-9596-0178b2a5b7b0" />
<img width="1518" height="568" alt="39" src="https://github.com/user-attachments/assets/cd942492-7585-47e9-9da3-f81a144b8eaf" />
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

 Go to SonarCloud.<img width="1851" height="868" alt="30" src="https://github.com/user-attachments/assets/8d47df62-fe1c-4495-9d37-60e1d386bee8" />
<img width="1471" height="685" alt="31" src="https://github.com/user-attachments/assets/a1c6c8fd-e942-49b7-9a8a-184c1a890d5d" />
<img width="1375" height="600" alt="33" src="https://github.com/user-attachments/assets/34a5e4f6-4d92-4cbc-b511-5c059aeb831d" />
<img width="1517" height="743" alt="34" src="https://github.com/user-attachments/assets/b2079a4c-85e1-46ac-84c8-98007745ae89" />
<img width="1520" height="728" alt="35" src="https://github.com/user-attachments/assets/c0c01ff5-607d-43b5-bca5-07b54562014b" />
<img width="1491" height="592" alt="36" src="https://github.com/user-attachments/assets/9d7d9273-981b-4379-b784-e491500eead0" />
<img width="1448" height="685" alt="37" src="https://github.com/user-attachments/assets/75fe635b-1855-46c3-9511-0b0d7ca0babe" />
<img width="1493" height="780" alt="38" src="https://github.com/user-attachments/assets/f3635aa9-19ae-455a-b55a-cf5445be8ed7" />
<img width="1518" height="568" alt="39" src="https://github.com/user-attachments/assets/95594953-a150-4eaa-a6eb-58ee3d3a6f5f" />
