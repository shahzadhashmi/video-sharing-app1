# Simple Azure Deployment Guide - Video Sharing App

## Quick Start Deployment (No Credit Loss Method)

### Prerequisites
- Azure account (free tier available)
- Git installed
- Node.js 18+ installed

### Step 1: Prepare Your Application

1. **Update environment variables** for production:
   ```bash
   # Create .env.production file in backend/
   PORT=8080
   NODE_ENV=production
   MONGO_URI=your_azure_cosmos_db_connection_string
   JWT_SECRET=your_production_secret_key
   ```

2. **Build your frontend**:
   ```bash
   cd frontend
   npm run build
   ```

### Step 2: Deploy Backend to Azure App Service

#### Option A: Using Azure Portal (Web Interface)
1. Go to [portal.azure.com](https://portal.azure.com)
2. Click "Create a resource" → "Web App"
3. Fill in:
   - **Name**: video-sharing-api-[your-name]
   - **Runtime**: Node 18 LTS
   - **Region**: East US
   - **App Service Plan**: B1 (free tier available)

4. After creation, go to "Deployment Center"
5. Choose "Local Git" as source
6. Push your backend code:
   ```bash
   cd backend
   git init
   git add .
   git commit -m "Initial commit"
   git remote add azure <your-git-url-from-portal>
   git push azure main
   ```

#### Option B: Using Azure CLI (One Command)
```bash
# Install Azure CLI and login
az login

# Create and deploy in one command
az webapp up \
  --resource-group video-sharing-rg \
  --name video-sharing-api \
  --runtime "NODE:18-lts" \
  --location eastus
```

### Step 3: Deploy Frontend to Azure Static Web Apps

#### Using Azure Portal
1. Go to [portal.azure.com](https://portal.azure.com)
2. Create "Static Web App"
3. Connect your GitHub repository
4. Configure build settings:
   - **App location**: `/frontend`
   - **Output location**: `dist`
5. The app will auto-deploy on every push

#### Manual Deployment
1. Build frontend:
   ```bash
   cd frontend
   npm run build
   ```

2. Deploy using Azure CLI:
   ```bash
   az staticwebapp create \
     --name video-sharing-frontend \
     --resource-group video-sharing-rg \
     --source https://github.com/your-username/video-sharing-app \
     --location eastus
   ```

### Step 4: Set Up Database (Azure Cosmos DB)

1. **Create Cosmos DB**:
   ```bash
   az cosmosdb create \
     --name video-sharing-cosmos \
     --resource-group video-sharing-rg \
     --kind MongoDB
   ```

2. **Get connection string**:
   ```bash
   az cosmosdb keys list \
     --name video-sharing-cosmos \
     --resource-group video-sharing-rg \
     --type connection-strings
   ```

3. **Update backend settings** in Azure Portal → Configuration → Application settings

### Step 5: Configure CORS and Environment

1. **Set CORS in backend**:
   - Azure Portal → Your Web App → CORS
   - Add your frontend URL

2. **Set environment variables**:
   - Azure Portal → Your Web App → Configuration
   - Add all required environment variables

### Step 6: Test Your Deployment

1. **Backend health check**:
   ```
   https://video-sharing-api.azurewebsites.net/api/health
   ```

2. **Frontend URL**:
   ```
   https://video-sharing-frontend.azurestaticapps.net
   ```

## Cost Optimization (Free Tier)
- **App Service**: B1 tier (free with 60 minutes/day)
- **Static Web Apps**: Free tier
- **Cosmos DB**: 400 RU/s free tier
- **Storage**: First 5GB free

## Troubleshooting
- Check logs in Azure Portal → Monitoring → Logs
- Ensure all environment variables are set
- Verify CORS configuration
- Check database connection

## Quick Commands Summary
```bash
# Deploy backend
az webapp up --resource-group video-sharing-rg --name video-sharing-api --runtime "NODE:18-lts"

# Deploy frontend
az staticwebapp create --name video-sharing-frontend --resource-group video-sharing-rg --source https://github.com/your-username/video-sharing-app

# Get URLs
az webapp show --name video-sharing-api --query defaultHostName
az staticwebapp show --name video-sharing-frontend --query defaultHostname
```

## Support
- Azure Documentation: [docs.microsoft.com](https://docs.microsoft.com/azure)
- Azure Support: Create support ticket in portal
