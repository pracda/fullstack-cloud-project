# 🌟 Fullstack Cloud Project
Serverless React App with AWS Lambda, API Gateway, DynamoDB & S3
AWS Node.js React DynamoDB

# 📜 Table of Contents
Project Overview
Architecture
Prerequisites
Repository Structure
Setup & Deployment
Usage
Troubleshooting

# 🚀 Project Overview
A serverless full-stack application enabling:
User Signup with email, name, password, and profile image (stored in S3).
Login with JWT token and profile retrieval.
Profile Image Updates (auto-replaces old S3 image, updates DynamoDB).

**Frontend**: React app hosted on S3 + CloudFront.
**Backend**: Node.js Lambda + API Gateway (RESTful endpoints).
**Infrastructure**: Fully automated via AWS CloudFormation.

# 🏗 Architecture
AWS Architecture Diagram (Replace with actual diagram)

# AWS Services Used
Service	Purpose
S3	Hosts React frontend + stores profile images.
CloudFront	CDN for low-latency frontend delivery.
DynamoDB	Stores user data (email, hashed passwords, image URLs, timestamps).
Lambda	Handles auth logic (signup/login) and image updates.
API Gateway	Exposes REST endpoints: POST /signup, POST /login, PUT /updateProfileImage.
IAM	Manages Lambda permissions for S3/DynamoDB.

# 📋 Prerequisites
AWS CLI with deployuser profile configured.
Node.js 20.x (Lambda runtime).
Git + Bash (or WSL/Git Bash on Windows).
PowerShell (Windows-only for Lambda zipping).

# 📂 Repository Structure
bash
fullstack-cloud-project/
├── backend/
│   └── index.mjs              # Lambda auth logic
├── frontend/auth-app/         # React app
│   ├── .env                  # S3 bucket URL
│   └── src/                  # React components
├── main.yml                  # CloudFormation template
├── deploy-infra.sh           # Infrastructure deployment
├── main.sh                   # Lambda code updater
└── lambda_update.log         # Deployment logs

# ⚙️ Setup & Deployment
**1. Clone & Configure**
git clone https://github.com/pracda/fullstack-cloud-project.git
cd fullstack-cloud-project
aws configure --profile deployuser  # Set AWS credentials
**2. Deploy Infrastructure**
chmod +x deploy-infra.sh
./deploy-infra.sh  # Deploys CloudFormation stack + Lambda

Outputs:
**Frontend URL**: http://ReactAppBucket.s3-website-us-east-1.amazonaws.com
**API Gateway**: https://[API_ID].execute-api.us-east-1.amazonaws.com/prod

**3. Deploy Frontend**
cd frontend/auth-app
npm install && npm run build
aws s3 sync build/ s3://$(aws cloudformation describe-stacks --stack-name my-cloud-formationr --query "Stacks[0].Outputs[?OutputKey=='ReactAppBucket'].OutputValue" --output text) --profile deployuser

**4. Update Lambda (Optional)**
./main.sh  # After modifying backend/index.mjs

# 🖥 Usage
**API Endpoints**
Endpoint	Method	Body Example
/signup	POST	{"email":"user@test.com", "name":"John", "password":"secret", "filename":"img.jpg"}
/login	POST	{"email":"user@test.com", "password":"secret"}
/updateProfileImage	PUT	{"email":"user@test.com", "oldImageKey":"img.jpg", "newFilename":"new.jpg"}

Test with cURL:
curl -X POST https://[API_ID].execute-api.us-east-1.amazonaws.com/prod/signup -H "Content-Type: application/json" -d '{"email":"test@test.com", "name":"Test", "password":"test123", "filename":"test.jpg"}'

# 🐞 Troubleshooting
Issue	Solution
Lambda ZIP fails	Verify INDEX_PATH in main.sh points to backend/index.mjs.
CORS errors	Ensure API Gateway has Access-Control-Allow-Origin: '*' headers.
Deployment hangs	Check deploy_log.txt for CloudFormation errors.

# ✨ Key Highlights for Recruiters
Infrastructure-as-Code: CloudFormation automates AWS resource provisioning.
Serverless Best Practices: Lambda + API Gateway scale dynamically.
Security: IAM roles restrict Lambda permissions, passwords hashed in DynamoDB.
CI/CD Ready: Scripted deployments (deploy-infra.sh) enable rapid iterations.
