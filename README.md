# AddressBook — Jenkins CI/CD Demo

A simple Java Maven web application used to demonstrate an end-to-end Jenkins CI/CD pipeline.

The project is intentionally lightweight so that students can focus on understanding how Jenkins automates:

- Source Code Checkout
- Compilation
- Unit Testing
- WAR Packaging
- Artifact Archiving
- Deployment to Tomcat

---

## Architecture

```text
Developer
    |
    | git push
    v
GitHub
    |
    | Source Code
    v
Jenkins Controller
    |
    v
AgentA
    |
    +-- Checkout
    +-- Compile
    +-- Test
    +-- Package
    |
    v
addressbook.war
