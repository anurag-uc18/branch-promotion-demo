# Dev → Test → Prod Tutorial

This file demonstrates how code moves through three environments.

## Our branches

* `dev` — where new development first arrives
* `test` — where completed development is tested
* `prod` — the production-ready version

## Promotion Flow

```text
Developer
    |
    v
   DEV
    |
    | Testing looks good
    v
   TEST
    |
    | QA passes
    v
   PROD
```

## Important Concept

We do not manually recreate this file in Test or Production.

Instead, Git moves the exact same committed version forward:

```text
commit
  |
  v
 dev
  |
  v
 test
  |
  v
 prod
```

This means the version tested in Test is the same version that eventually reaches Production.

## Current Experiment

This file was originally created on the `dev` branch.

If this same file later appears on the `test` branch, our Dev → Test promotion worked.

If it then appears on the `prod` branch, our Test → Prod promotion worked.

