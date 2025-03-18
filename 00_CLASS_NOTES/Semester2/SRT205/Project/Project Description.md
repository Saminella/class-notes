---
class: 
topics: 
tags: 
creation_date: 2025-02-09 07:43
last_updated: 2025-02-09 07:43
---
#### To Do
- [ ]
---
# Project Description
# **SRT205 Automation Group Project**

---

## **Overview of CIS Compliance**

The **Center for Internet Security (CIS)** provides best practices for securing systems against cyber threats. CIS develops **configuration benchmarks** to help organizations improve their security posture. These benchmarks ensure systems are securely configured, reducing vulnerabilities and exposure to attacks.

### **Why is CIS Compliance Important?**

- **Enhanced Security** – CIS benchmarks ensure secure configurations for operating systems and applications.
- **Industry Standards** – Compliance aligns with industry regulations and cybersecurity frameworks.
- **Risk Reduction** – Prevents security misconfigurations and reduces vulnerabilities.
- **Audit & Compliance Readiness** – Simplifies security audits and regulatory compliance efforts.

---

## **Project Objective**

This project leverages **Ansible** to automate **CIS compliance tasks** on **Windows and Ubuntu systems**. Students will:

- Develop **Ansible Playbooks** to enforce security configurations.
- Modularize configurations using **Ansible Roles**.
- Secure sensitive data using **Ansible Vault**.
- Generate **dynamic compliance reports** using **Jinja2**.
- Implement **robust error handling** to enhance automation reliability.
- **Collaborate effectively** using GitHub for version control and documentation.

---

## **Deliverables**

### 1️⃣ **Automated CIS Compliance Tasks**

- Implement **10 CIS compliance tasks (each)** for **Windows** and **Ubuntu** systems.
- Ensure configurations follow **Ansible best practices**.

### 2️⃣ **Dynamic Role Implementation**

- Organize automation tasks into **Ansible Roles** for modularity:
  - `cis_ubuntu`: Manages CIS compliance for Ubuntu.
  - `cis_windows`: Manages CIS compliance for Windows.
  - `audit_compliance`: Implements security auditing and reporting.

### 3️⃣ **Security Using Ansible Vault**

- Secure all sensitive credentials (e.g., SSH keys, Windows passwords) using **Ansible Vault**.

### 4️⃣ **Dynamic Jinja2 Reporting**

- Use **Jinja2 templates** to generate **dynamic compliance reports**.

### 5️⃣ **Comprehensive Playbook Execution**

- Develop a **main playbook (`main.yml`)** that invokes all relevant roles.

### 6️⃣ **Debugging and Error Handling**

- Implement **robust error handling** using `assert`, `fail`, `block`, and `rescue`.

### 7️⃣ **Version Control and Collaboration**

- Maintain a structured **GitHub repository**:
  - Meaningful **commit messages**.
  - Organized **project structure**.
  - Clear **documentation (README.md, contributions, execution instructions, etc.)**.

### 8️⃣ **Final Presentation**

- Deliver a **10-minute group presentation** covering:
  - **Objectives and outcomes**.
  - **Demonstration of playbooks**.
  - **Challenges and solutions**.
  - **Lessons learned and future improvements**.

---

## **Team Member Responsibilities**

Each team member has a designated role to ensure smooth collaboration:

### **🔹 Role 1 - Linux Specialist**

- Develop and test the **`cis_ubuntu` role**.
- Configure **SSH connections**, **Inventory file** and **Variables** for Ubuntu systems.
- Implement **Ubuntu-specific CIS tasks**.
- Encrypt **Ubuntu-related secrets** using **Ansible Vault**.
- **participate in preparation of the final presentation**

---

### **🔹 Role 2 - Windows Specialist**

- Develop and test the **`cis_windows` role**.
- Configure **WinRM connections**, **Inventory file** and **Variables** for Windows systems.
- Implement **Windows-specific CIS tasks**.
- Encrypt **Windows-related secrets** using **Ansible Vault**.
- **participate in preparation of the final presentation**

---

### **🔹 Role 3 - Team Lead (Security, Reporting, & Collaboration)**

- **Lead the team, coordinate collaboration, and ensure progress tracking**.
- Develop and test the **`audit_compliance` role**.
- Develop audit tasks that verifies system compliance.
- Develop a **Jinja2 compliance report** summarizing system configurations.
- **Oversee version control and GitHub collaboration**
- **participate in preparation of the final presentation**

## **Grading Rubric (22% of Total Course Grade)**

| **Criteria**                         | **Weight**        | **Details**                                                                   |
| ------------------------------------ | ----------------- | ----------------------------------------------------------------------------- |
| **CIS Compliance Implementation**    | 20% + (Bonus 10%) | Correct implementation of security configurations for Ubuntu and Windows.     |
| **Proper Ansible Project Structure** | 15%               | Proper modularization and well developed tasks.                               |
| **Security Best Practices**          | 10%               | Effective use of Ansible Vault for securing credentials.                      |
| **Comprehensive Reporting**          | 10%               | Detailed compliance report using Jinja2.                                      |
| **Error Handling & Debugging**       | 15%               | Use of `assert`, `fail`, `block`, and `rescue` for resilience.                |
| **GitHub Collaboration**             | 15%               | Proper repository structure, proper README documentation and commit messages. |
| **Final Presentation**               | 15%               | Clarity, depth, and effectiveness of the presentation.                        |

---

## **Submission Guidelines**

### **📌 Required Submissions**

1. **GitHub Repository** – Submit the repository URL with:

   - **README.md** with setup/execution instructions and team contributions.
   - Well-organized **Ansible roles and playbooks**.
   - **Meaningful commit messages** for tracking changes.

2. **Screenshots & Evidence**:

   - Playbook execution logs.
   - Generated **compliance reports**.
   - GitHub commit history.

3. **Final Presentation**:
   - A **10-minute presentation** covering:
     - Project design.
     - Playbook demonstration.
     - Challenges and solutions.
     - Lessons learned.

---

## **Why This Project Matters**

This project provides **hands-on experience** with **Ansible in a cybersecurity context**, fostering **teamwork, real-world problem-solving skills, and presentation abilities**.

By completing this project, students will:
✅ Develop automation skills for cybersecurity compliance.  
✅ Gain experience in securing IT infrastructure.  
✅ Improve collaboration and project management skills.  
✅ Strengthen technical documentation and reporting abilities.








