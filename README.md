# Dynamixs.AI – Partner Custom Build Template

This template shows how to create a custom build of the Dynamixs.AI platform for your customer.
It uses the Maven WAR Overlay mechanism to extend and customize the Dynamixs.AI base UI
without touching the platform core.

## Prerequisites

- Java 17+
- Maven 3.9+
- Docker
- A Dynamixs.AI Partner Account on GitHub

> **Becoming a Partner**
> To access the private Dynamixs.AI Maven Registry you need a partner account.
> Contact us at partner@dynamixs.ai

---

## Getting Started

### 1. Configure your Maven credentials

Add the following to your local `~/.m2/settings.xml`:

```xml
<settings>
    <servers>
        <server>
            <id>github-dynamixs</id>
            <username>YOUR_GITHUB_USERNAME</username>
            <!-- Personal Access Token with read:packages permission -->
            <password>YOUR_GITHUB_TOKEN</password>
        </server>
    </servers>
</settings>
```

Create a GitHub Personal Access Token with `read:packages` permission here:
https://github.com/settings/tokens

### 2. Clone this template

```bash
git clone https://github.com/dynamixs-ai/partner-template.git acme-workflow
cd acme-workflow
```

### 3. Customize `pom.xml`

Change `groupId`, `artifactId` and `finalName` to match your customer project:

```xml
<groupId>com.acme</groupId>
<artifactId>acme-workflow</artifactId>
...
<finalName>acme-workflow</finalName>
```

### 4. Build

```bash
mvn clean package
```

The custom WAR file will appear in `target/`.

---

## How the WAR Overlay works

This template is based on the Maven WAR Overlay mechanism. The Dynamixs.AI platform UI
(`dynamixs-platform-ui`) is used as the base WAR. Your files in `src/main/webapp/`
automatically take precedence over files from the base WAR.

The build chain looks like this:

```
imixs-office-workflow-app   (WAR, public on Sonatype)
        ↓ overlay
dynamixs-platform-ui        (WAR, Dynamixs.AI GitHub Packages)
        ↓ overlay
acme-workflow               (WAR, your custom build)
```

Each layer only overrides what it needs - everything else is inherited from below.

---

## Customizing the UI

To override a file (CSS, XHTML, JSF component) simply copy it into `src/main/webapp/`.
Maven will automatically prefer your version over the base.

To inspect all available files you can override, unpack the base WAR:

```bash
mvn dependency:unpack -Dartifact=ai.dynamixs:dynamixs-platform-ui:LATEST:war -DoutputDirectory=target/platform-ui
```

Browse `target/platform-ui/` and copy any file you want to customize into `src/main/webapp/`.

**Example: override the default stylesheet**

```
src/main/webapp/resources/css/custom.css
```

---

## Adding Customer-specific Code

Add your own CDI beans and services under `src/main/java/`. For example a connector
to an ERP system:

```
src/main/java/
└── com/acme/
    └── erp/
        └── SAPConnector.java
```

This code is owned by your customer - not by Dynamixs.AI.

Add the corresponding dependency in your `pom.xml` if needed:

```xml
<dependency>
    <groupId>com.acme</groupId>
    <artifactId>sap-connector</artifactId>
    <version>1.0.0</version>
</dependency>
```

---

## Project Structure

```
your-project/
├── pom.xml                        ← extend this
├── docker/                        ← Docker Compose setup (provided by Dynamixs.AI)
└── src/main/
    ├── java/
    │   └── com/acme/              ← your custom CDI beans and services
    └── webapp/
        ├── WEB-INF/
        │   └── web.xml            ← optional: add your own servlets/filters
        └── resources/
            ├── css/
            │   └── custom.css     ← override platform styles
            └── components/        ← add custom JSF components here
```

---

## Deployment

The resulting WAR is deployed on Wildfly. Use the Docker Compose setup
provided by Dynamixs.AI (available to partners) which includes:

| Service    | Description              |
|------------|--------------------------|
| Wildfly 32 | Jakarta EE App Server    |
| PostgreSQL  | Primary database         |
| Cassandra  | Archive / document store |
| OpenAI     | AI integration proxy     |

---

## Upgrading the Platform Version

To upgrade to a new version of Dynamixs.AI, change the version property in your `pom.xml`:

```xml
<ai.dynamixs.version>1.1.0</ai.dynamixs.version>
```

Then rebuild with `mvn clean package`. Your customizations in `src/main/webapp/`
are never touched by the upgrade.

---

## BPMN Process Models

Ready-to-use BPMN process templates are available in our public library:
https://github.com/dynamixs-ai/bpmn-library

---

## Resources

| Resource               | URL                                         |
|------------------------|---------------------------------------------|
| Dynamixs.AI Website    | https://www.dynamixs.ai                     |
| Platform Documentation | https://doc.dynamixs.ai                     |
| Imixs-Workflow         | https://www.imixs.org                       |
| Imixs-Office-Workflow  | https://doc.office-workflow.com             |
| Custom Build Guide     | https://doc.office-workflow.com/build/      |
| Partner Support        | support@dynamixs.ai                         |
| Partner Onboarding     | partner@dynamixs.ai                         |
