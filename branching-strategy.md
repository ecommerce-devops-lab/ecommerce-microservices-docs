# Environment-Based Branching Strategy for Monorepos

## Overview

This branching strategy is designed for projects that follow a **DevOps approach with a monorepository (monorepo)** structure containing multiple microservices. Unlike traditional GitFlow, which may become cumbersome in a monorepo setup, this strategy emphasizes **clarity, automation, and environment alignment**.

By using dedicated long-lived branches to represent each environment (such as `develop`, `stage`, and `prod`), the strategy supports **continuous integration**, **progressive delivery**, and **service-level independence** within a unified repository.


## Branch Types

### `prod`

* **Purpose**: Represents the code currently deployed in the **production environment**.
* **CI/CD Role**: Triggers production deployments automatically or manually.
* **Permissions**: Protected; only mergeable through pull requests after passing QA/staging validation.
* **Updated from**: `stage`

### `stage`

* **Purpose**: Mirrors the **staging environment**, used for pre-production testing and integration validation.
* **CI/CD Role**: Triggers deployments to the staging environment.
* **Updated from**: `develop`
* **Merged into**: `prod` after staging approval.

### `develop`

* **Purpose**: Default branch for **active development** and service integration.
* **CI/CD Role**: Triggers builds, tests, and optional deployment to development environments or preview instances.
* **Updated from**: `feature/*`, `bugfix/*`, `hotfix/*`
* **Merged into**: `stage`

### `feature/*`

* **Purpose**: Temporary branches for new features or enhancements.
* **Naming**: `feature/<service>-<short-description>` (e.g., `feature/payments-add-paypal`)
* **Created from**: `develop`
* **Merged into**: `develop`
* **Deleted after merge**

### `hotfix/*`

* **Purpose**: Urgent fixes for bugs in production.
* **Naming**: `hotfix/<service>-<short-description>`
* **Created from**: `prod`
* **Merged into**: `prod` and `develop`

### `bugfix/*` *(optional)*

* **Purpose**: Corrective patches found during development or QA.
* **Created from**: `develop`
* **Merged into**: `develop`


## Recommended Folder Structure

```
/
├── microservices/
│   ├── auth/
│   ├── payments/
│   ├── orders/
│   └── notifications/
├── shared-libs/
├── infra/
├── docker/
└── .github/workflows/ or .gitlab-ci.yml
```

Each service can maintain its own CI/CD workflow, and changes can be detected via path filters in the pipeline.

---

## Branch Workflow

```bash
# Start a new feature
git checkout develop
git checkout -b feature/payments-add-paypal

# Complete the feature and merge
git checkout develop
git merge feature/payments-add-paypal
git branch -d feature/payments-add-paypal

# Promote develop to stage
git checkout stage
git merge develop

# Promote stage to production
git checkout prod
git merge stage
```


## Hotfix Workflow

```bash
# Create a hotfix from production
git checkout prod
git checkout -b hotfix/auth-fix-token-expiry

# Fix the issue and merge
git commit -am "Fix token expiry bug in auth service"
git checkout prod
git merge hotfix/auth-fix-token-expiry
git checkout develop
git merge hotfix/auth-fix-token-expiry
git branch -d hotfix/auth-fix-token-expiry
```


## CI/CD Integration Tips

| Feature                  | Recommendation                                                                             |
| ------------------------ | ------------------------------------------------------------------------------------------ |
| Path-based workflows     | Use `paths:` filters to run jobs only when relevant microservices are modified.            |
| Branch-based deployments | Map branches (`develop`, `stage`, `prod`) to separate environments in your CI/CD platform. |
| Tagging                  | Optionally tag services on merge to `prod`, e.g., `auth-v1.4.2`.                           |
| Review gates             | Require pull request approvals and successful checks before merging to `stage` and `prod`. |
| Rollback support         | Maintain history and tagging for fast production rollback if needed.                       |


## Benefits

* Aligned with CI/CD and automation principles
* Simple and scalable for large teams or service architectures
* Clear promotion path: `develop → stage → prod`
* Supports feature isolation and parallel development
* Minimal branching overhead


## When to Use This Strategy

This strategy is ideal when:

* You use a **monorepo** containing multiple microservices
* You deploy continuously or frequently
* You want to **separate deployment concerns by environment**
* You rely heavily on **CI/CD automation**
