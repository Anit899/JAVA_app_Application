# MyWebApp

A simple Java Spring Boot web application, ready to deploy to Azure App Service.

## Run locally

```bash
mvn spring-boot:run
```

Then open http://localhost:8080

## Build a runnable jar

```bash
mvn clean package
java -jar target/MyWebApp-1.0.0.jar
```

## Deploy to Azure App Service

### Option A: Azure CLI (quickest, one-off deploy)

```bash
# 1. Build the jar
mvn clean package

# 2. Create a resource group (skip if you already have one)
az group create --name myResourceGroup --location eastus

# 3. Create an App Service plan (Linux, B1 tier)
az appservice plan create --name myAppServicePlan \
  --resource-group myResourceGroup --sku B1 --is-linux

# 4. Create the Web App with the Java 11 runtime
az webapp create --resource-group myResourceGroup \
  --plan myAppServicePlan --name my-webapp-service \
  --runtime "JAVA:11-java11"

# 5. Deploy the jar
az webapp deploy --resource-group myResourceGroup \
  --name my-webapp-service --src-path target/MyWebApp-1.0.0.jar --type jar
```

Your app will be live at `https://my-webapp-service.azurewebsites.net`.

### Option B: Azure Pipelines (CI/CD, this repo)

This repo includes `azure-pipelines.yml`, which builds the app with Maven and
deploys it to Azure App Service automatically on every push to `main`.

Before running it:
1. Create the App Service in Azure (see steps 2–4 above, or via the Portal).
2. In Azure DevOps, go to **Project Settings > Service Connections** and create
   an Azure Resource Manager connection. Note its name.
3. Edit `azure-pipelines.yml` and set:
   - `webAppName` to your App Service name
   - `azureSubscription` to your service connection name
4. Push to `main` — the pipeline builds and deploys automatically.

## Endpoints

- `/` — home page
- `/health` — health check, returns `{"status":"UP"}`
