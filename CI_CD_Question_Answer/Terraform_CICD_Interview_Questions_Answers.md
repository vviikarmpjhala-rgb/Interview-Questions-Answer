# Terraform CI/CD Interview Questions & Answers

> **Interview style:** Simple English, easy to speak and understand.  
> **Pattern:** One-line answer → Explanation → Bullet points → Diagram where useful.

---

# 7. Terraform CI/CD Pipeline

## 1. How do you design a Terraform CI/CD pipeline?

### One-line answer
**Terraform CI/CD pipeline mein code checkout → formatting → validation → security scan → cost check → plan → approval → apply ka flow rakhta hoon.**

### Explanation

Mera normal Terraform pipeline flow:

```text
Developer
   |
   | Terraform Code
   v
GitHub / Azure Repos
   |
   v
PR Validation
   |
   +--> terraform fmt
   +--> terraform validate
   +--> TFLint
   +--> tfsec / Checkov
   +--> Gitleaks / TruffleHog
   +--> Infracost
   |
   v
Terraform Plan
   |
   v
PR Review
   |
   v
Merge to Main
   |
   v
Production Plan
   |
   v
Manual Approval
   |
   v
Terraform Apply
   |
   v
Azure Resources
```

### Important points

- Terraform code Git repository mein hota hai.
- PR create hone par validation pipeline run hoti hai.
- `fmt` code formatting check karta hai.
- `validate` Terraform syntax/configuration check karta hai.
- `TFLint` Terraform-specific issues check karta hai.
- `tfsec` security misconfiguration check karta hai.
- `Gitleaks/TruffleHog` secrets check karta hai.
- `Infracost` estimated cost dikhata hai.
- `plan` batata hai kya infrastructure change hoga.
- PR approval ke baad code `main` mein merge hota hai.
- Production ke liye dobara plan run kar sakte hain.
- Apply se pehle **manual approval** rakhte hain.
- Approval ke baad `terraform apply` execute hota hai.

**Interview line:**

> "I keep the pipeline automated for validation and planning, but production apply is protected with approval and branch controls."

---

## 2. Explain Terraform `init → validate → plan → apply`.

### One-line answer
**Init Terraform ko prepare karta hai, validate configuration check karta hai, plan changes dikhata hai aur apply actual infrastructure create/update karta hai.**

### Flow

```text
terraform init
      ↓
terraform validate
      ↓
terraform plan
      ↓
Approval
      ↓
terraform apply
```

### 1. `terraform init`

Terraform working directory ko initialize karta hai.

Ismein mainly:

- Provider download hota hai.
- Modules download hote hain.
- Backend configure hota hai.
- `.terraform` directory create hoti hai.

```bash
terraform init
```

### 2. `terraform validate`

Terraform configuration mein syntax/configuration errors check karta hai.

```bash
terraform validate
```

Example:

```text
Success! The configuration is valid.
```

### 3. `terraform plan`

Terraform compare karta hai:

```text
Current Infrastructure
        +
Terraform Configuration
        ↓
Required Changes
```

Example:

```text
Plan: 2 to add, 1 to change, 0 to destroy
```

**Important:** Plan normally infrastructure ko change nahi karta.

### 4. `terraform apply`

Plan ke according actual infrastructure create/update/delete karta hai.

```bash
terraform apply
```

Production mein generally:

```text
Plan
 ↓
Review
 ↓
Approval
 ↓
Apply
```

---

## 3. How do you separate Init, Plan and Apply stages?

### One-line answer
**Main CI/CD pipeline ko separate stages mein divide karta hoon: Init/Validation → Plan → Approval → Apply, jisse production changes controlled rahen.**

### Flow

```text
Stage 1
Terraform Init
Terraform Validate
Security Scans
       |
       v
Stage 2
Terraform Plan
       |
       v
Stage 3
Manual Approval
       |
       v
Stage 4
Terraform Apply
```

### Why separate stages?

- Failure identify karna easy hota hai.
- Plan ko review kar sakte hain.
- Production apply automatically nahi hota.
- Approval add kar sakte hain.
- Security checks apply se pehle complete ho jaate hain.

---

## 4. How do you pass Terraform Plan between pipeline stages?

### One-line answer
**Main Terraform plan ko saved plan file mein store karke next stage/job mein artifact ke through pass karta hoon.**

```bash
terraform plan -out=tfplan
```

### Flow

```text
Plan Stage
    |
    | tfplan
    v
Publish Artifact
    |
    v
Approval
    |
    v
Download Artifact
    |
    v
terraform apply tfplan
```

Next stage mein:

```bash
terraform apply tfplan
```

### Why?

Agar apply ke time dobara normal `terraform plan` karte hain, toh plan different ho sakta hai.

Saved plan use karne se:

```text
Reviewed Plan
     =
Applied Plan
```

**Interview point:**

> "For controlled deployments, I prefer to generate a saved plan and pass that plan artifact to the apply stage."

---

## 5. How do you implement approval before Apply?

### One-line answer
**Production apply se pehle manual approval/environment approval lagata hoon, taaki reviewed plan ke baad hi infrastructure change ho.**

### Flow

```text
Terraform Plan
      |
      v
Plan Output
      |
      v
Reviewer
      |
   APPROVE
      |
      v
Terraform Apply
```

### Important points

- Developer PR create karta hai.
- Pipeline plan generate karti hai.
- Reviewer plan check karta hai.
- Production environment protected hota hai.
- Approval milne ke baad apply stage start hota hai.
- Approval reject hua toh apply nahi chalega.

---

## 6. How do you integrate security checks into Terraform pipeline?

### One-line answer
**Security checks ko Terraform apply se pehle CI/PR pipeline mein integrate karta hoon, taaki insecure infrastructure production tak na jaaye.**

### Flow

```text
Terraform Code
      ↓
terraform fmt
      ↓
terraform validate
      ↓
      +------> TFLint
      |
      +------> tfsec / Checkov
      |
      +------> Gitleaks
      |
      +------> Infracost
      |
      ↓
terraform plan
      ↓
Approval
      ↓
Apply
```

### Tools

| Tool | Purpose |
|---|---|
| `terraform fmt` | Formatting |
| `terraform validate` | Configuration validation |
| TFLint | Terraform linting |
| tfsec | Terraform security scan |
| Checkov | IaC security scan |
| Gitleaks | Secret detection |
| TruffleHog | Secret/credential detection |
| Infracost | Cost estimation |

---

## 7. Where do you integrate tfsec?

### One-line answer
**tfsec ko Terraform validation ke baad aur plan/apply se pehle run karta hoon.**

```text
terraform fmt
      ↓
terraform validate
      ↓
tfsec
      ↓
terraform plan
      ↓
Approval
      ↓
apply
```

### tfsec kya check karta hai?

Terraform configuration mein security problems.

Example:

```hcl
public_network_access_enabled = true
```

Ya overly permissive configuration.

### Pipeline behavior

```text
tfsec
  |
  v
Critical Issue
  |
  v
Pipeline FAILED
  |
  X
No Apply
```

---

## 8. Where do you integrate TFLint?

### One-line answer
**TFLint ko Terraform validate ke around CI validation stage mein run karta hoon, plan se pehle.**

```text
Terraform Code
      ↓
fmt
      ↓
validate
      ↓
TFLint
      ↓
Security Scan
      ↓
plan
```

### TFLint ka purpose

TFLint Terraform code ke issues identify karta hai.

For example:

- Invalid resource configuration
- Provider-specific mistakes
- Naming/configuration problems
- Unused or suspicious configuration
- Azure resource related best-practice issues

### Simple difference

```text
validate → Terraform configuration valid hai?
TFLint   → Configuration mein quality/issues hain?
tfsec    → Security issue hai?
```

---

## 9. Where do you integrate TruffleHog/Gitleaks?

### One-line answer
**Gitleaks/TruffleHog ko Terraform validation ke early stage mein run karta hoon, taaki secrets repository ya Terraform files se production tak na jaayen.**

```text
Git Checkout
     ↓
Gitleaks / TruffleHog
     ↓
fmt
     ↓
validate
     ↓
TFLint
     ↓
tfsec
     ↓
plan
```

### What do they detect?

```text
AWS Access Key
Azure Secret
GitHub Token
API Key
Password
```

Agar secret detect ho:

```text
Secret Found
    ↓
Pipeline Failed
    ↓
No Plan/Apply
```

### Important interview point

Secret accidentally commit ho gaya ho toh sirf file delete karna enough nahi hai.

- Secret ko revoke/rotate karo.
- Git history check karo.
- Secret ko Key Vault/secret manager mein move karo.
- Pipeline dobara run karo.

---

## 10. Where do you integrate Infracost?

### One-line answer
**Infracost ko Terraform plan ke baad integrate karta hoon, kyunki actual resource changes ke basis par estimated cost dikhana useful hota hai.**

```text
Terraform Code
      ↓
Terraform Plan
      ↓
Infracost
      ↓
Cost Difference
      ↓
PR Review
```

Example:

```text
Current cost:     $500/month
New cost:         $780/month
Increase:         $280/month
```

Reviewer ko pata chal jaata hai ki proposed infrastructure ka cost kitna badhega.

---

## 11. At which stage do you perform cost estimation?

### One-line answer
**Cost estimation Terraform plan ke baad aur production apply se pehle karta hoon.**

```text
Terraform Plan
      ↓
Infracost
      ↓
Cost Review
      ↓
Approval
      ↓
Apply
```

### Why plan ke baad?

Plan batata hai:

```text
What resources are changing?
```

Infracost batata hai:

```text
What will those changes approximately cost?
```

### Interview example

> "If a developer adds 10 VMs, Infracost shows the expected monthly cost increase before we approve the production deployment."

---

## 12. How do you prevent developers from bypassing Terraform security checks?

### One-line answer
**Security checks ko sirf developer-side script par depend nahi karta; Git branch protection aur mandatory PR checks ke through enforce karta hoon.**

### Flow

```text
Developer
   |
   v
Feature Branch
   |
   v
Pull Request
   |
   v
Mandatory Checks
   |
   +--> TFLint
   +--> tfsec
   +--> Gitleaks
   +--> Validate
   |
   v
All Passed?
   |
   +---- NO ---> Merge Blocked
   |
   YES
   |
   v
Approval
   |
   v
Merge
```

### Controls

- Protect `main`.
- Disable direct push.
- Require PR.
- Require mandatory status checks.
- Require reviewer approval.
- Restrict who can modify workflow files.
- Production apply permission limited rakho.

**Interview point:**

> "A scan is useful only when it is enforced as a required check."

---

## 13. How do you standardize Terraform across 20+ cloud accounts?

### One-line answer
**Common Terraform modules, standard pipeline templates, remote state, naming/tagging standards aur security policies use karke Terraform ko standardize karta hoon.**

### Architecture

```text
                 Git Repository
                       |
             ---------------------
             |                   |
        Terraform Modules    Pipeline Template
             |                   |
             ---------------------
                       |
       --------------------------------
       |        |        |            |
    Account1 Account2 Account3 ... Account20
       |        |        |            |
       v        v        v            v
    Azure     Azure    Azure        Azure
```

### Standardization ke liye

**1. Reusable Modules**

```text
modules/
├── resource-group
├── vnet
├── subnet
├── key-vault
├── storage
└── aks
```

**2. Standard naming**

```text
rg-prod-india
vnet-prod-india
snet-app-prod
```

**3. Standard tags**

```text
Environment = Prod
Owner        = Network
Application  = ABC
CostCenter   = 1001
```

**4. Standard pipeline**

Har account mein same:

```text
fmt
validate
TFLint
tfsec
Gitleaks
Infracost
plan
approval
apply
```

**5. Remote State**

Har environment/account ka Terraform state centrally controlled backend mein maintain karte hain.

**6. Policy**

Azure Policy/Cloud governance ke through mandatory security and compliance rules enforce kar sakte hain.

---

# Terraform CI/CD — Quick Revision

| # | Question | Interview One-Liner |
|---|---|---|
| 1 | CI/CD design | **Checkout → Validate → Security → Cost → Plan → Approval → Apply** |
| 2 | Init → Apply | **Init prepares, validate checks, plan previews, apply changes infrastructure.** |
| 3 | Separate stages | **Init/Validation, Plan, Approval and Apply ko separate stages mein rakhta hoon.** |
| 4 | Pass Plan | **Saved `tfplan` ko artifact ke through Apply stage mein pass karta hoon.** |
| 5 | Approval | **Production Apply se pehle manual/environment approval lagata hoon.** |
| 6 | Security | **Security scans Apply se pehle mandatory rakhta hoon.** |
| 7 | tfsec | **Validate ke baad aur Plan se pehle.** |
| 8 | TFLint | **Terraform validation stage mein, Plan se pehle.** |
| 9 | Gitleaks | **Early stage mein secrets detect karta hoon.** |
| 10 | Infracost | **Plan ke baad estimated infrastructure cost check karta hoon.** |
| 11 | Cost stage | **Plan → Infracost → Review → Approval → Apply.** |
| 12 | Bypass prevention | **Branch protection + mandatory checks + PR approval.** |
| 13 | 20+ accounts | **Modules + standard pipelines + remote state + policies + tagging/naming standards.** |

---

# Complete Terraform CI/CD Interview Diagram

```text
                 Developer
                     |
                     v
               Feature Branch
                     |
                     v
                Pull Request
                     |
                     v
        +---------------------------+
        |      CI VALIDATION        |
        |                           |
        | terraform fmt             |
        | terraform validate        |
        | TFLint                    |
        | tfsec / Checkov           |
        | Gitleaks / TruffleHog     |
        +---------------------------+
                     |
                     v
              Terraform Plan
                     |
                     v
                Infracost
                     |
                     v
              PR Review/Approval
                     |
                     v
               Merge to Main
                     |
                     v
             Production Plan
                     |
                     v
             Manual Approval
                     |
                     v
             Terraform Apply
                     |
                     v
              Azure Resources
```

### Final interview line

> **"My main focus is that no unvalidated, insecure, or unreviewed Terraform code should reach production."**
