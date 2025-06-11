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

---

Feel free to fork, modify, and integrate this setup in your own Azure Function apps!
