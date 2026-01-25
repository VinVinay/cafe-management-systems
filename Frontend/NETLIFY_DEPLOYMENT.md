# Netlify Deployment Guide for Cafe Management System

## Prerequisites
- Node.js v14+ installed
- Angular CLI installed globally
- Netlify account
- Git repository

## Setup Instructions

### 1. Install Dependencies
```bash
cd Frontend
npm install
```

### 2. Build for Production
```bash
npm run build
```
The build output will be in `dist/Frontend/`

### 3. Deploy to Netlify

#### Option A: Using Netlify CLI
```bash
npm install -g netlify-cli
netlify deploy --prod
```

#### Option B: Using Netlify UI (Recommended)
1. Push your code to GitHub/GitLab/Bitbucket
2. Go to https://app.netlify.com
3. Click "New site from Git"
4. Choose your repository
5. Set build command: `npm run build`
6. Set publish directory: `dist/Frontend`
7. Click "Deploy site"

### 4. Environment Variables
Set the following environment variables in Netlify dashboard:
- `NODE_OPTIONS` = `--openssl-legacy-provider` (if using Node 17+)

### 5. Custom API URL
Update the API URL in `src/environments/environment.prod.ts`:
```typescript
apiUrl: 'https://your-api-domain.com'
```

## Important Notes
- The `netlify.toml` file handles SPA routing redirects automatically
- All assets are cached with proper cache-busting
- Security headers are configured

## Troubleshooting

### Build fails with OpenSSL error
Add environment variable: `NODE_OPTIONS=--openssl-legacy-provider`

### 404 on page refresh
The `netlify.toml` is configured to redirect all routes to `index.html` (SPA routing)

### API calls fail
Check that the API URL in `environment.prod.ts` is correct and the backend is accessible
