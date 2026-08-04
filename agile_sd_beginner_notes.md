# What is Agile?

**Agile** is a way of developing software where teams build software in **small pieces**, get feedback quickly, improve continuously, and deliver value to customers faster.

Instead of spending **1 year building everything** and releasing it at the end, Agile says:

> "Build a small part, release it, get feedback, improve it, then repeat."

---

## Traditional Method (Waterfall)

Imagine you're building a house.

The traditional approach looks like this:

```
Planning
    ↓
Design
    ↓
Development
    ↓
Testing
    ↓
Deployment
```

Only after **everything is finished** does the customer see the house.

If they say,

> "I actually wanted three bedrooms instead of two."

It becomes expensive and time-consuming to change.

---

## Agile Method

Now imagine building the same house differently.

Week 1

```
One room is ready.
```

Customer visits.

> "Looks good, but I want larger windows."

Week 2

```
Kitchen is ready with larger windows.
```

Customer gives more feedback.

Week 3

```
Bathroom completed.
```

The customer is involved throughout the project.

This reduces mistakes and ensures the final product matches customer needs.

---

# Why Agile Was Created

Earlier software projects had many problems:

* Customers waited months or years.
* Requirements changed frequently.
* Huge bugs were discovered late.
* Teams worked in isolation.
* Projects often failed.

Agile solves these by:

* Delivering software frequently
* Accepting changing requirements
* Encouraging teamwork
* Getting continuous customer feedback

---

# Agile Manifesto

In 2001, 17 software experts created the **Agile Manifesto**.

It contains four core values.

### 1. Individuals and interactions over processes and tools

People matter more than tools.

Example:

Instead of arguing over Jira or Excel,

developers should communicate directly.

---

### 2. Working software over comprehensive documentation

Documentation is useful,

but working software is more valuable.

Instead of writing a 500-page document,

deliver a feature users can actually use.

---

### 3. Customer collaboration over contract negotiation

Customers should stay involved during development.

Not just at the beginning and the end.

---

### 4. Responding to change over following a plan

Requirements change.

Agile welcomes change instead of resisting it.

---

# Agile Principles

The Agile Manifesto also defines 12 guiding principles. Some of the most important are:

* Deliver software frequently
* Welcome changing requirements
* Developers and business people work together
* Build projects around motivated individuals
* Face-to-face communication is best
* Working software is the main measure of progress
* Maintain a sustainable pace
* Keep improving continuously

---

# What is an Agile Team?

An Agile team is usually small.

Example:

```
Product Owner

Scrum Master

Developers

QA Engineers

UI/UX Designers

DevOps Engineers
```

Everyone works together.

---

# Real Example

Imagine you're developing a food delivery app.

Instead of building everything at once,

the team builds features step by step.

Sprint 1

```
Login
Registration
```

Sprint 2

```
Restaurant List
```

Sprint 3

```
Food Ordering
```

Sprint 4

```
Online Payment
```

Sprint 5

```
Order Tracking
```

Users start using the app early.

The company gets feedback immediately.

---

# Agile Workflow

```
Idea
   ↓
Planning
   ↓
Sprint Planning
   ↓
Development
   ↓
Testing
   ↓
Deployment
   ↓
Customer Feedback
   ↓
Repeat
```

Notice the cycle repeats continuously.

---

# What is a Sprint?

A **Sprint** is a fixed period during which the team completes a small set of work.

Usually:

```
1 Week

2 Weeks

3 Weeks

4 Weeks
```

Most companies use **2-week sprints**.

---

# What Happens During a Sprint?

Example:

Sprint Duration:

```
14 Days
```

The team commits to completing:

* Login Page
* Password Reset
* Dashboard

After 14 days,

they demonstrate the completed work.

---

# Agile Ceremonies

Every sprint includes several regular meetings.

### Sprint Planning

The team decides what to build during the sprint.

---

### Daily Stand-up

A short meeting (usually 15 minutes).

Everyone answers:

* What did I do yesterday?
* What will I do today?
* Is anything blocking me?

---

### Sprint Review

The team demonstrates completed work to stakeholders.

---

### Sprint Retrospective

The team discusses:

* What went well?
* What didn't?
* What should improve next sprint?

---

# Product Backlog

Think of it as a master to-do list.

Example:

```
User Login

Payment

Notifications

Search

Profile

Dark Mode
```

---

# Sprint Backlog

Only the work selected for the current sprint.

Example:

Sprint 1

```
Login

Registration

Forgot Password
```

---

# Increment

At the end of every sprint,

the software should be in a usable state.

Each sprint adds another working piece.

---

# User Story

A User Story describes a feature from the user's perspective.

Example:

> As a customer, I want to reset my password so that I can regain access to my account.

Simple structure:

```
As a ______

I want ______

So that ______
```

---

# Acceptance Criteria

These define when the story is considered complete.

Example:

Password reset story:

* Email is sent
* Reset link expires after 30 minutes
* User can set a new password
* Success message appears

---

# Agile Estimation

Teams estimate effort using **Story Points** instead of hours.

Common values:

```
1

2

3

5

8

13

21
```

These represent relative effort, not exact time.

---

# Popular Agile Frameworks

Agile is a mindset. Teams often follow frameworks to put it into practice.

| Framework                    | Best For                                     |
| ----------------------------- | --------------------------------------------- |
| **Scrum**                    | Most software teams                          |
| **Kanban**                   | Continuous work and support teams            |
| **Extreme Programming (XP)** | High-quality code with engineering practices |
| **Lean**                     | Eliminating waste and improving flow         |

---

# Where DevOps Fits

Agile focuses on:

```
Develop software faster.
```

DevOps extends this by ensuring software can also be:

```
Built automatically

Tested automatically

Released automatically

Monitored continuously
```

Together:

```
Agile + DevOps

↓

Faster Development

↓

Faster Delivery

↓

Continuous Improvement
```

---

# Example from a DevOps Team

Suppose you're working as a DevOps Engineer.

Sprint Goal:

```
Automate deployment of the application.
```

Tasks:

* Write a Dockerfile
* Create a Jenkins pipeline
* Configure GitHub Actions
* Deploy to AWS
* Monitor using CloudWatch

At the sprint review, the team demonstrates:

> "Now every code push automatically builds, tests, and deploys the application."

---

# Agile vs Waterfall

| Feature             | Waterfall                | Agile                  |
| -------------------- | ------------------------- | ------------------------ |
| Planning            | Done once                | Continuous             |
| Delivery            | End of project           | Frequent               |
| Customer Feedback   | Late                     | Continuous             |
| Requirement Changes | Difficult                | Welcome                |
| Risk                | Higher                   | Lower                  |
| Releases            | One large release        | Many small releases    |
| Testing             | Mostly after development | Throughout development |

---

# Why Agile Matters for DevOps

If you're preparing for DevOps roles, you'll frequently hear terms like:

* Sprint
* Scrum
* User Story
* Backlog
* Stand-up
* Retrospective
* CI/CD
* Increment
* Velocity
* Story Points

A DevOps engineer participates in Agile ceremonies, collaborates with developers and testers, automates delivery pipelines, and helps ensure every sprint can produce a releasable product.

---

# Key Takeaways

* Agile is a **mindset** focused on delivering value quickly and adapting to change.
* Work is divided into **small iterations** called **sprints**.
* Teams collaborate closely with customers and each other.
* Feedback is gathered continuously to improve the product.
* **Scrum** is the most widely used Agile framework.
* DevOps complements Agile by automating the build, test, deployment, and monitoring processes.

---
