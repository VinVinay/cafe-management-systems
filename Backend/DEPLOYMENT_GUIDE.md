# Backend Deployment Guide

## Hosting Options for Spring Boot Backend

### 1. **Railway.app** (Recommended - Simple & Free Tier)
- Zero configuration needed
- Automatic MySQL database provisioning
- GitHub integration
- Free tier available

**Steps:**
1. Push code to GitHub
2. Go to https://railway.app
3. Click "New Project" → "Deploy from GitHub repo"
4. Select your repo
5. Add MySQL plugin
6. Railway automatically sets environment variables

### 2. **Render.com**
- Free tier available
- Easy MySQL setup
- Pay-as-you-go

### 3. **AWS Elastic Beanstalk**
- Professional deployment
- Scalable
- Paid service

### 4. **Heroku** (Phased out - Not recommended)

---

## Quick Deployment with Railway.app

### Prerequisites
- GitHub account with the code pushed
- Railway.app account (sign up with GitHub)

### Steps

1. **Create database on Railway:**
   - Go to Railway Dashboard
   - New → MySQL
   - Note the connection details

2. **Update Backend Configuration:**
   - Keep `application.properties` as template
   - Railway will provide environment variables

3. **Add Procfile (Optional):**
   - Create `Procfile` in Backend root
   - Content: `web: java -Dserver.port=$PORT $JAVA_OPTS -jar target/*.jar`

4. **Set Environment Variables in Railway:**
   ```
   SPRING_DATASOURCE_URL=jdbc:mysql://[host]:[port]/cafe?serverTimezone=UTC
   SPRING_DATASOURCE_USERNAME=[username]
   SPRING_DATASOURCE_PASSWORD=[password]
   SPRING_MAIL_USERNAME=[your-email]
   SPRING_MAIL_PASSWORD=[app-password]
   SPRING_JPA_HIBERNATE_DDL_AUTO=create
   ```

5. **Deploy:**
   - Railway automatically deploys on push to main branch

---

## Environment-Based Configuration

### Create `application-prod.properties`

File: `Backend/src/main/resources/application-prod.properties`

```properties
spring.application.name=CafeManagementSystem
server.port=8080

spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.open-in-view=false

# These will be set via environment variables
spring.datasource.url=${SPRING_DATASOURCE_URL}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD}

spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL57Dialect
spring.jpa.generate-ddl=true
spring.jpa.show-sql=false

spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=${SPRING_MAIL_USERNAME}
spring.mail.password=${SPRING_MAIL_PASSWORD}
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

---

## Build for Production

```bash
cd Backend
mvn clean package
```

This generates: `target/com.inn.cafe-0.0.1-SNAPSHOT.jar`

---

## Update Frontend API URL

After getting the backend URL from Railway:

**File:** `Frontend/src/environments/environment.prod.ts`
```typescript
export const environment = {
  production: true,
  apiUrl: 'https://your-railway-app.railway.app',
};
```

Then rebuild and deploy frontend to Netlify.

---

## Gmail App Password Setup

1. Enable 2FA on Gmail
2. Go to https://myaccount.google.com/apppasswords
3. Generate app password for "Mail"
4. Use in `SPRING_MAIL_PASSWORD` environment variable

---

## Database Migration

### Initial Setup
1. Railway creates empty MySQL database
2. Set `spring.jpa.hibernate.ddl-auto=create` first deployment
3. After first run, change to `update`

### Restore from SQL Dump (if needed)
```bash
mysql -h [host] -u [user] -p [password] cafe < Database.sql
```

---

## Check Application Status

```bash
# View logs on Railway
railway logs

# Test endpoint (after deployment)
curl https://your-railway-app.railway.app/api/health
```

---

## Common Issues

### "Connection refused"
- Check environment variables are set correctly
- Verify database is running and accessible

### "Port already in use"
- Railway handles port automatically via `$PORT` env var

### Build fails
- Ensure Java 17 is used
- Check all dependencies are available

---

## Cost Estimate
- **Railway.app:** ~$5-10/month (MySQL + Spring Boot app)
- **Render.com:** Similar pricing
- **AWS:** Variable, typically $20-50/month for small apps
