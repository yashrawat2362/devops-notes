# Terraform Notes — Part 1: Beginner Foundations

> **Goal:** Learn Terraform from zero, with simple examples and DevOps-focused understanding.

---

## 1. What is Terraform?

**Terraform** is an **Infrastructure as Code (IaC)** tool.

Instead of manually creating cloud resources from a web portal, you write configuration files and Terraform creates them for you.

### Without Terraform

You might manually:

1. Open AWS/Azure/GCP console.
2. Create a VPC.
3. Create subnets.
4. Create security groups.
5. Create EC2/VM.
6. Configure everything again for another environment.

This is slow and easy to make mistakes.

### With Terraform

You describe what you want:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
}
```

Terraform then works out how to create it.

### Simple definition

> **Terraform lets you define infrastructure using code and manage that infrastructure automatically.**

---

# 2. Why DevOps Engineers Use Terraform

Terraform is useful because it provides:

- **Automation** — create infrastructure automatically.
- **Repeatability** — create the same infrastructure again.
- **Consistency** — reduce manual configuration mistakes.
- **Version control** — store infrastructure code in Git.
- **Collaboration** — teams can review infrastructure changes.
- **Multi-cloud support** — AWS, Azure, GCP, and many other platforms.
- **Change tracking** — Terraform shows what it plans to change.

---

# 3. What is Infrastructure as Code?

**Infrastructure as Code (IaC)** means managing infrastructure through code instead of manually clicking through a cloud console.

For example:

```text
Traditional:
Human → Cloud Console → Create EC2

IaC:
Terraform Code → Terraform → Cloud Provider → Create EC2
```

The important idea is:

> **Infrastructure becomes something you can write, review, version, test, and reproduce.**

---

# 4. Terraform vs Configuration Management

These are related but not exactly the same.

### Terraform

Mainly used to **create and manage infrastructure**.

Examples:

- VPC
- Subnet
- EC2
- Load Balancer
- RDS
- Azure VM
- Storage Account

### Configuration Management

Usually focuses on configuring machines after they exist.

Examples:

- Install Nginx
- Install Docker
- Copy configuration files
- Start services

Tools include Ansible, Puppet, and Chef.

### Simple comparison

```text
Terraform
    ↓
Creates infrastructure
    ↓
EC2 / VPC / ALB / RDS

Ansible
    ↓
Configures machines
    ↓
Install Nginx / Docker / packages
```

In real DevOps environments, these tools can be used together.

---

# 5. Terraform Architecture

A simple Terraform workflow looks like this:

```text
Terraform Configuration
        ↓
     Terraform
        ↓
    Provider
        ↓
AWS / Azure / GCP
        ↓
   Infrastructure
```

For example:

```text
main.tf
   ↓
Terraform
   ↓
AWS Provider
   ↓
EC2 Instance
```

---

# 6. What is HCL?

Terraform configuration is normally written using **HCL**.

HCL means:

> **HashiCorp Configuration Language**

Terraform files usually have the `.tf` extension.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
}
```

HCL is designed to be readable by humans.

---

# 7. Terraform File Extension

Terraform configuration files use:

```text
.tf
```

Examples:

```text
main.tf
variables.tf
outputs.tf
providers.tf
terraform.tf
```

Terraform automatically reads `.tf` files in the current working directory.

---

# 8. Important Terraform Files

You do not need all of these on day one, but you should know them.

| File | Purpose |
|---|---|
| `main.tf` | Main infrastructure configuration |
| `provider.tf` | Provider configuration |
| `variables.tf` | Input variables |
| `outputs.tf` | Output values |
| `terraform.tfvars` | Variable values |
| `versions.tf` | Terraform/provider version requirements |

A small project might initially contain only:

```text
terraform-project/
├── main.tf
└── provider.tf
```

Later it may become:

```text
terraform-project/
├── main.tf
├── provider.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
└── versions.tf
```

---

# 9. What is a Terraform Provider?

Terraform itself does not know how to create an AWS EC2 instance or an Azure VM.

A **provider** is a plugin that allows Terraform to communicate with an external platform or service.

Examples:

```text
AWS       → AWS Provider
Azure     → AzureRM Provider
Google    → Google Provider
Kubernetes → Kubernetes Provider
GitHub    → GitHub Provider
```

Think of a provider as a **translator/connector** between Terraform and a service.

---

# 10. AWS Provider Example

For AWS:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}
```

Here:

- `terraform` → Terraform settings.
- `required_providers` → declares required providers.
- `aws` → provider name.
- `source` → where the provider comes from.
- `version` → allowed provider version.
- `provider "aws"` → configures AWS.
- `region` → AWS region.

> Provider versions change over time. In real projects, choose and pin a version compatible with your project rather than blindly copying an old example.

---

# 11. What is a Resource?

A **resource** is something Terraform creates or manages.

Examples:

```text
AWS EC2
AWS VPC
AWS S3 Bucket
AWS RDS
Azure VM
Azure Storage Account
```

Basic syntax:

```hcl
resource "RESOURCE_TYPE" "NAME" {
  # arguments
}
```

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
}
```

There are two important names here:

```text
aws_instance
     ↓
Resource type

web
     ↓
Local resource name
```

---

# 12. Resource Address

Terraform identifies a resource using its address.

For this:

```hcl
resource "aws_instance" "web" {
  ...
}
```

The address is:

```text
aws_instance.web
```

You will see this address frequently in Terraform commands and output.

---

# 13. Terraform Basic Workflow

The most important Terraform workflow is:

```text
Write Code
    ↓
terraform init
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
terraform apply
```

After changes:

```text
Change Code
    ↓
terraform plan
    ↓
terraform apply
```

---

# 14. `terraform init`

Run:

```bash
terraform init
```

### What does it do?

It initializes the Terraform working directory.

It can:

- Download providers.
- Set up the working directory.
- Configure modules.
- Prepare backend configuration.

You normally run it when starting a Terraform project and when configuration dependencies change.

Example:

```bash
mkdir terraform-demo
cd terraform-demo
terraform init
```

---

# 15. `terraform fmt`

Run:

```bash
terraform fmt
```

It formats Terraform files into Terraform's standard style.

Example:

```bash
terraform fmt
```

Useful because it keeps your code clean and consistent.

You can also format recursively:

```bash
terraform fmt -recursive
```

---

# 16. `terraform validate`

Run:

```bash
terraform validate
```

It checks whether your Terraform configuration is syntactically and structurally valid.

Example:

```bash
terraform validate
```

Typical successful result:

```text
Success! The configuration is valid.
```

Important:

> `validate` does **not** mean Terraform has checked whether the infrastructure will actually work exactly as intended in your cloud account.

---

# 17. `terraform plan`

Run:

```bash
terraform plan
```

This is one of the most important Terraform commands.

Terraform compares:

```text
Your configuration
        +
Current Terraform state
        +
Real infrastructure
        ↓
Proposed changes
```

It shows what Terraform intends to do.

Common symbols:

```text
+   create
-   destroy
~   update in-place
-/+ replace
```

Example:

```text
+ aws_instance.web
```

means Terraform plans to create the resource.

### Golden habit

> **Always review `terraform plan` before applying important infrastructure changes.**

---

# 18. `terraform apply`

Run:

```bash
terraform apply
```

Terraform shows the proposed changes and asks for confirmation.

You normally enter:

```text
yes
```

Terraform then creates or changes the infrastructure.

You can also save a plan:

```bash
terraform plan -out=tfplan
```

Then apply exactly that saved plan:

```bash
terraform apply tfplan
```

This is especially useful in controlled CI/CD workflows.

---

# 19. `terraform destroy`

Run:

```bash
terraform destroy
```

It removes infrastructure managed by the current Terraform configuration/state.

Example:

```bash
terraform destroy
```

Terraform normally asks for confirmation.

### WARNING

Do not casually run:

```bash
terraform destroy
```

against production infrastructure.

It can delete real resources and potentially cause downtime or data loss.

---

# 20. A Very Small Example

Suppose we want to create an AWS EC2 instance.

### `main.tf`

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t2.micro"
}
```

### Workflow

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

Terraform will attempt to create the EC2 instance.

> The AMI ID must exist in the AWS region you are using. AMI IDs are region-specific.

---

# 21. Terraform State

One of the most important Terraform concepts is **state**.

Terraform usually stores information about infrastructure in a state file:

```text
terraform.tfstate
```

State helps Terraform understand:

```text
What Terraform manages
        +
What currently exists
        ↓
What needs to change
```

For example:

```text
Terraform code says:
1 EC2 instance

State says:
aws_instance.web exists

Terraform checks reality and state
        ↓
Determines whether changes are needed
```

---

# 22. Why State Is Important

Imagine Terraform created:

```text
EC2 instance
VPC
Security Group
```

Terraform needs to remember the relationship between your configuration and those real resources.

State provides that information.

Without proper state management, Terraform cannot safely manage infrastructure.

---

# 23. Never Treat `terraform.tfstate` as a Normal File

Important rules:

- Do not manually edit state unless you really understand Terraform state operations.
- Do not casually delete state.
- State can contain sensitive information.
- In team environments, local state is usually not enough.

For teams, Terraform commonly uses a **remote backend** so state can be shared and protected.

---

# 24. Local State vs Remote State

### Local state

```text
Your computer
    ↓
terraform.tfstate
```

Good for:

- Learning
- Small experiments

### Remote state

```text
Developer 1 ─┐
Developer 2 ─┼→ Remote Terraform State
CI/CD       ─┘
```

Useful for:

- Teams
- CI/CD
- Collaboration
- State locking/support depending on backend

Common approaches include storing state in cloud/object storage with appropriate locking or backend capabilities.

---

# 25. Desired State

Terraform is **declarative**.

This is extremely important.

You generally tell Terraform:

> "I want this infrastructure."

You do not have to write every individual action Terraform should perform.

Example:

```hcl
resource "aws_instance" "web" {
  instance_type = "t2.micro"
}
```

You describe the desired result.

Terraform determines the actions required to reach that result.

---

# 26. Imperative vs Declarative

### Imperative

You describe **how** to do something.

```text
1. Create VM
2. Create network
3. Attach network
4. Configure firewall
5. Start VM
```

### Declarative

You describe **what you want**:

```text
I want:
- a VM
- a network
- a firewall
- the VM connected to the network
```

Terraform calculates the required operations.

---

# 27. Terraform Idempotency

A useful DevOps concept is **idempotency**.

Suppose your configuration says:

```text
I want 1 EC2 instance.
```

After Terraform creates it, running:

```bash
terraform apply
```

again should normally result in:

```text
No changes.
```

assuming nothing has changed outside Terraform and configuration/state remain consistent.

This makes infrastructure automation safer and repeatable.

---

# 28. Terraform Dependency

Resources can depend on other resources.

Example:

```text
VPC
 ↓
Subnet
 ↓
EC2
```

Terraform builds a dependency graph and determines an appropriate order of operations.

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
}
```

This expression:

```hcl
aws_vpc.main.id
```

creates an implicit dependency.

Terraform understands:

```text
VPC must exist
       ↓
Subnet can be created
```

---

# 29. Terraform Variables

Variables allow you to avoid hardcoding values.

Instead of:

```hcl
resource "aws_instance" "web" {
  instance_type = "t2.micro"
}
```

You can use:

```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}

resource "aws_instance" "web" {
  instance_type = var.instance_type
}
```

Now:

```text
var.instance_type
```

means:

> Get the value of the `instance_type` variable.

---

# 30. Why Variables Are Useful

Without variables:

```text
dev  → t2.micro
test → t2.small
prod → t3.small
```

You might repeatedly modify the code.

With variables:

```text
same Terraform code
        ↓
different input values
        ↓
different environments
```

This improves reuse.

---

# 31. Outputs

Outputs display useful information after Terraform applies infrastructure.

Example:

```hcl
output "instance_id" {
  value = aws_instance.web.id
}
```

After apply, Terraform can show something like:

```text
instance_id = "i-0123456789abcdef0"
```

Another example:

```hcl
output "public_ip" {
  value = aws_instance.web.public_ip
}
```

Outputs are useful when another person, module, or system needs an important value.

---

# 32. Data Sources

A **data source** reads information that already exists instead of creating it.

For example:

```text
Resource
→ Terraform creates something

Data source
→ Terraform reads something
```

Example idea:

```hcl
data "aws_ami" "ubuntu" {
  ...
}
```

Terraform can use the information returned by the data source.

This is useful when you do not want to hardcode values such as an AMI ID.

---

# 33. Resource vs Data Source

Remember this simple rule:

```text
resource → CREATE / MANAGE

data     → READ
```

Example:

```hcl
resource "aws_instance" "web" {
  ...
}
```

Terraform manages the EC2 instance.

```hcl
data "aws_ami" "ubuntu" {
  ...
}
```

Terraform reads information about an AMI.

---

# 34. Comments in Terraform

Single-line comment:

```hcl
# This creates an EC2 instance
```

You may also see:

```hcl
// This creates an EC2 instance
```

Multi-line comments:

```hcl
/*
This is a
multi-line comment.
*/
```

---

# 35. Terraform Directory

A simple beginner project:

```text
terraform-demo/
│
├── main.tf
├── provider.tf
├── variables.tf
└── outputs.tf
```

Terraform reads all `.tf` files in the directory.

The file names themselves are mostly for organization.

For example, Terraform does not require your resource to be inside `main.tf`.

---

# 36. Important Terraform Commands

| Command | Purpose |
|---|---|
| `terraform init` | Initialize project |
| `terraform fmt` | Format code |
| `terraform validate` | Validate configuration |
| `terraform plan` | Preview changes |
| `terraform apply` | Create/update infrastructure |
| `terraform destroy` | Destroy managed infrastructure |
| `terraform show` | Show state/plan information |
| `terraform output` | Show outputs |
| `terraform state list` | List resources in state |
| `terraform version` | Show Terraform version |
| `terraform providers` | Show provider requirements |

You will learn advanced state commands later.

---

# 37. Typical Beginner Workflow

When starting a new Terraform project:

```bash
mkdir terraform-demo
cd terraform-demo
```

Create your `.tf` files.

Then:

```bash
terraform init
```

Format:

```bash
terraform fmt
```

Validate:

```bash
terraform validate
```

Preview:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

When finished with a disposable lab:

```bash
terraform destroy
```

---

# 38. Terraform and Git

Terraform code should normally be stored in Git.

Example:

```text
GitHub Repository
│
└── terraform-project/
    ├── main.tf
    ├── provider.tf
    ├── variables.tf
    └── outputs.tf
```

This gives you:

- History
- Code review
- Collaboration
- Rollback of configuration changes
- CI/CD integration

---

# 39. Important `.gitignore` Rules

Do **not** blindly commit Terraform-generated/local files.

A common `.gitignore` includes:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfplan
crash.log
crash.*.log
```

Be especially careful with:

```text
terraform.tfstate
terraform.tfstate.backup
```

State can contain sensitive information.

Also avoid committing secrets such as:

```text
passwords
API keys
access keys
private keys
```

---

# 40. Terraform Registry

Terraform providers and modules are commonly published through the **Terraform Registry**.

You can find:

- Providers
- Modules
- Documentation
- Resource examples
- Data source documentation

For official provider documentation, always check the provider's current documentation because resource arguments and provider versions change.

---

# 41. Terraform Modules

A **module** is a reusable collection of Terraform configuration.

Imagine you repeatedly need:

```text
VPC
├── Public Subnets
├── Private Subnets
├── Route Tables
└── Internet Gateway
```

Instead of rewriting everything for every project, you can create a module.

Think:

```text
Normal function in programming
        ≈
Reusable Terraform module
```

You will learn modules in a later part.

---

# 42. Terraform Lifecycle — Big Picture

You should understand this overall flow:

```text
                Terraform Code
                     │
                     ▼
              terraform init
                     │
                     ▼
             terraform validate
                     │
                     ▼
               terraform plan
                     │
                     ▼
              terraform apply
                     │
                     ▼
             Cloud Infrastructure
                     │
                     ▼
               Terraform State
```

When code changes:

```text
Change .tf files
      ↓
terraform plan
      ↓
Review changes
      ↓
terraform apply
```

---

# 43. Terraform Mental Model

As a beginner, remember these five things:

```text
1. .tf files
   ↓
   Describe desired infrastructure

2. Provider
   ↓
   Connects Terraform to AWS/Azure/etc.

3. Resource
   ↓
   Thing Terraform creates/manages

4. State
   ↓
   Records Terraform's knowledge of managed infrastructure

5. Plan
   ↓
   Shows what Terraform wants to change
```

If these five concepts are clear, your Terraform foundation is strong.

---

# 44. Common Beginner Mistakes

### Mistake 1 — Running `apply` without checking the plan

Better:

```bash
terraform plan
terraform apply
```

---

### Mistake 2 — Hardcoding secrets

Avoid:

```hcl
password = "MyPassword123"
```

Use safer secret-management approaches instead.

---

### Mistake 3 — Deleting `terraform.tfstate`

Do not delete state just because something looks wrong.

First understand the problem.

---

### Mistake 4 — Confusing resource name with resource type

In:

```hcl
resource "aws_instance" "web" {
}
```

```text
aws_instance → type
web          → local name
```

---

### Mistake 5 — Thinking Terraform only creates resources

Terraform can:

```text
Create
Update
Replace
Destroy
Read
Manage
```

resources depending on configuration and provider behavior.

---

# 45. Terraform vs Cloud Console

### Cloud Console

Good for:

- Learning a service
- Quick experiments
- Inspecting resources

### Terraform

Good for:

- Repeatable infrastructure
- Automation
- Team environments
- Version control
- CI/CD
- Large infrastructure

A DevOps engineer should understand **both** the cloud platform and Infrastructure as Code.

---

# 46. Beginner Practice Task

Before moving to the next part, try this learning exercise:

### Task

Create a Terraform project that:

1. Configures AWS.
2. Uses the `ap-south-1` region.
3. Creates one simple resource.
4. Runs:
   - `terraform init`
   - `terraform fmt`
   - `terraform validate`
   - `terraform plan`
   - `terraform apply`
5. Checks the resource in AWS.
6. Runs `terraform destroy` after the experiment.

Do not copy commands blindly.

Try to understand what every line does.

---

# 47. Important Concepts to Remember

```text
Terraform
    ↓
Infrastructure as Code

HCL
    ↓
Language used to write Terraform configuration

Provider
    ↓
Connects Terraform to a platform/service

Resource
    ↓
Infrastructure Terraform manages

Data Source
    ↓
Information Terraform reads

Variable
    ↓
Input to configuration

Output
    ↓
Useful value returned by Terraform

State
    ↓
Terraform's record of managed infrastructure

Plan
    ↓
Preview of changes

Apply
    ↓
Actually performs changes

Destroy
    ↓
Removes managed infrastructure
```

---

# 48. What We Will Learn in Later Parts

Terraform is too large to learn properly in one file, so learn it step-by-step.

### Part 1 — Foundations
- What is Terraform
- IaC
- HCL
- Providers
- Resources
- State
- Basic commands
- Basic workflow

### Part 2 — Terraform Language
- Blocks
- Arguments
- Attributes
- Variables
- Data types
- Lists
- Maps
- Sets
- Objects
- Tuples
- Expressions
- Functions
- Conditional expressions

### Part 3 — Resources and Dependencies
- Resource references
- Implicit dependencies
- Explicit dependencies
- Dependency graph
- `depends_on`
- Resource lifecycle
- Replacement
- `count`
- `for_each`

### Part 4 — Variables, Outputs and Locals
- Input variables
- Variable types
- Defaults
- Validation
- Sensitive variables
- `terraform.tfvars`
- Environment variables
- Outputs
- Local values

### Part 5 — Data Sources and Dynamic Configuration
- Data sources
- Built-in functions
- Dynamic blocks
- `for` expressions
- Conditional logic
- Useful real-world patterns

### Part 6 — Modules
- What modules are
- Root module
- Child modules
- Module inputs
- Module outputs
- Module sources
- Reusable infrastructure

### Part 7 — State and Backends
- State in depth
- Remote state
- Backends
- State locking
- State commands
- Importing resources
- Moving resources
- Removing resources from state

### Part 8 — Terraform + AWS
- VPC
- Subnets
- Route tables
- Security groups
- EC2
- ALB
- RDS
- IAM
- S3
- Multi-AZ infrastructure

### Part 9 — Terraform + Azure
- AzureRM provider
- Resource groups
- Virtual networks
- Subnets
- NSGs
- VMs
- Storage
- Azure-specific patterns

### Part 10 — Terraform for DevOps
- Terraform with Git
- GitHub Actions
- CI/CD
- Plan/apply workflows
- Remote state
- Secrets
- Environment management
- Infrastructure review

### Part 11 — Production Practices
- Project structure
- Naming conventions
- Version pinning
- Security
- State security
- Code quality
- Testing
- Drift
- Documentation

### Part 12 — Real DevOps Project
Build a complete infrastructure project using Terraform.

Example:

```text
Terraform
   ↓
AWS VPC
   ↓
Public + Private Subnets
   ↓
ALB
   ↓
EC2 / Auto Scaling
   ↓
RDS
   ↓
GitHub Actions
   ↓
Automated Infrastructure Deployment
```

---

# 49. Final Cheat Sheet

```bash
# Start a project
terraform init

# Format
terraform fmt

# Validate
terraform validate

# Preview changes
terraform plan

# Create/update infrastructure
terraform apply

# Destroy infrastructure
terraform destroy

# Show outputs
terraform output

# Show current state/resources
terraform state list

# Check Terraform version
terraform version
```

### Remember:

> **Write → Init → Format → Validate → Plan → Apply**

And for a temporary lab:

> **Destroy it when you are finished.**

---

## Part 1 — Key Takeaway

If you remember only one diagram from Part 1, remember this:

```text
              YOU
               │
               ▼
          Terraform Code
             (.tf)
               │
               ▼
          terraform plan
               │
               ▼
        Review the changes
               │
               ▼
         terraform apply
               │
               ▼
       Cloud Infrastructure
               │
               ▼
       Terraform State
```

**Do not try to memorize everything.**

First understand:

**Terraform → Provider → Resource → State → Plan → Apply**

That foundation will make the advanced topics much easier.
