# Azure Function App with dotenv for Local Development

This project demonstrates how to build and deploy a Python-based Azure Function App using local `.env` files during development, and Azure Application Settings in production.

## 📁 Project Structure

```
MyFunctionApp/
│
├── .env                   # Local environment variables (DO NOT COMMIT)
├── .gitignore             # Ignore .env, local.settings.json, __pycache__, etc.
├── requirements.txt       # Python dependencies
├── build.yaml             # Azure DevOps pipeline for CI/CD
├── host.json              # Azure Functions host config
├── local.settings.json    # Local dev settings
└── function_app/
    ├── __init__.py        # Main Azure Function logic
    └── function.json      # Function configuration
```

## ⚙️ Local Development

1. Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```

2. Create a `.env` file:
    ```
    DB_NAME=MyLocalDB
    SECRET_KEY=dev-secret-key
    ```

3. Run the function locally:
    ```bash
    func start
    ```

The application uses `python-dotenv` to load variables from `.env` when not running in Azure.

## 🚀 Deployment to Azure

### Environment Variables

Use Azure Portal or CI pipeline to set the following Application Settings:
- `DB_NAME`
- `SECRET_KEY`

These are automatically picked up using `os.getenv()` in your function.

### Azure DevOps Pipeline (`build.yaml`)

The pipeline:
- Uses a Python version
- Installs dependencies
- Archives and deploys the app to Azure
- Optionally sets environment variables on the Function App

### App Settings Task (Optional)
The pipeline includes a task to set environment variables on the Azure Function App:
```yaml
- task: AzureAppServiceSettings@1
  inputs:
    azureSubscription: '$(azureSubscription)'
    appName: '$(functionAppName)'
    resourceGroupName: 'my-resource-group'
    appSettings: |
      [
        {
          "name": "DB_NAME",
          "value": "AzureDB"
        },
        {
          "name": "SECRET_KEY",
          "value": "prod-secret"
        }
      ]
```

## ✅ Best Practices

- Exclude `.env` from version control.
- Use Azure Application Settings for production secrets.
- Separate local and cloud config cleanly.

## 🧭 Azure Deployment Setup Summary

This project is configured to be deployed to Azure using Azure DevOps pipelines. Here's a high-level summary of how everything works:

### 1. 🧪 CI/CD Pipeline (`build.yaml`)
- **Trigger**: Pipeline triggers on commits to `main`.
- **Python Setup**: Selects Python 3.10 using `UsePythonVersion`.
- **Install Dependencies**: Installs requirements from `requirements.txt`.
- **Archive**: Zips the app for deployment.
- **Publish Artifacts**: Stores the zipped function app as a build artifact.
- **Deploy to Azure**: Deploys the artifact to your Azure Function App using `AzureFunctionApp@1`.
- **Set App Settings** *(Optional)*: Uses `AzureAppServiceSettings@1` to push your `.env` variables as secure Azure Application Settings.

### 2. 🛠 Azure Portal Setup (Manual Steps)
Before running the pipeline:
- Create a **Function App** in Azure (Linux, Python 3.10).
- Link it to a **Resource Group** and **App Service Plan**.
- Add any required **Application Settings** in the Configuration tab (like `DB_NAME`, `SECRET_KEY`).

### 3. 📦 Azure DevOps Setup
- Create a **service connection** named `MyAzureServiceConnection`.
- Link your repo to an Azure Pipeline.
- Place `build.yaml` in the root or configure it manually in pipeline settings.

### 4. 🌐 Application Settings in Azure
These are used in place of `.env` during deployment:
- Can be added manually in Azure Portal > Configuration
- Or automatically via pipeline

### ✅ Summary

| Environment | Secrets Source     | dotenv Used | Deployment               |
|-------------|--------------------|-------------|---------------------------|
| Local       | `.env` file        | ✅ Yes      | `func start` or VS Code   |
| Azure Prod  | App Settings       | ❌ No       | Azure DevOps or Portal    |

This ensures your development and production environments remain secure and properly isolated.

---

Feel free to fork, modify, and integrate this setup in your own Azure Function apps!


---
