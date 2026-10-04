# AWS Cloud Resume Challenge

A cloud-hosted personal portfolio website built as an AWS Cloud Resume Challenge project. It combines a static frontend, AWS-managed delivery infrastructure, a serverless visitor counter, IAM-based security, and automated GitHub-to-AWS deployment.

## Live Website

**Portfolio:** https://portfolio.vanditjaiswal.in

<img width="2539" height="1326" alt="image" src="https://github.com/user-attachments/assets/2aada451-f648-412f-b7b1-16e7756027c8" />


## Architecture

<img width="1083" height="1081" alt="image" src="https://github.com/user-attachments/assets/5e2ec971-5bb7-4c37-8abe-384c551e7716" />


## Frontend

The responsive portfolio includes a professional profile, AWS certification, technical stack, projects, research publication, contact links, and a serverless visitor counter.

<img width="2540" height="1333" alt="image" src="https://github.com/user-attachments/assets/1db56073-f86a-4452-be85-bec1a3db5846" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="2544" height="1343" alt="image" src="https://github.com/user-attachments/assets/9704ed5a-b65a-441c-9beb-861f7a906726" />

## AWS Infrastructure

### Amazon S3

The static website is stored in the `vandit-cloud-resume-challenge` S3 bucket. GitHub Actions synchronizes the repository's `website` directory with the bucket.

<img width="2559" height="783" alt="image" src="https://github.com/user-attachments/assets/9c666da7-36f0-4d69-816d-0dcf257ff00a" />


### Amazon CloudFront

CloudFront delivers the website through a CDN with the custom domain `portfolio.vanditjaiswal.in` and an ACM-managed certificate.

<img width="2559" height="783" alt="image" src="https://github.com/user-attachments/assets/49087a98-0496-49dd-969c-4c8aeb696019" />


### Amazon Route 53

Route 53 manages DNS for `vanditjaiswal.in`. Alias A/AAAA records route the portfolio subdomain to CloudFront.

<img width="2559" height="955" alt="image" src="https://github.com/user-attachments/assets/68842bfa-e51a-43b8-903d-294f8a41c43d" />


### AWS Certificate Manager

ACM provides the SSL/TLS certificate used by CloudFront.

<img width="2559" height="502" alt="image" src="https://github.com/user-attachments/assets/afd1f0b8-c1cb-49a8-976e-dcd215721570" />


## Serverless Visitor Counter

The visitor counter follows:

<img width="1061" height="637" alt="image" src="https://github.com/user-attachments/assets/885ed292-79b5-4f78-b256-ec2fb5d63ce1" />

The Lambda function performs an atomic DynamoDB `UpdateItem` operation to increment the counter.

<img width="2558" height="1234" alt="image" src="https://github.com/user-attachments/assets/82b4fb4d-56e7-4a41-9f54-1de6257a634f" />

### DynamoDB

The visitor count is stored in the `vandit-cloud-resume-challenge` DynamoDB table using on-demand capacity.

<img width="2559" height="728" alt="image" src="https://github.com/user-attachments/assets/dc940069-1d82-46f1-b80c-6e5582ca5571" />

### Lambda Function URL and CORS

The Lambda backend is exposed through a Function URL. CORS allows requests from the portfolio domain and the required GET method.

<img width="2559" height="1151" alt="image" src="https://github.com/user-attachments/assets/c1ba784f-2b7c-48f3-8e8d-0f22975cef2a" />

## IAM and Security

A dedicated `GitHubActions-PortfolioDeploy` IAM role is used by GitHub Actions. Authentication uses GitHub OIDC instead of long-lived AWS access keys.

The trust relationship is restricted to the repository and `main` branch, while the deployment policy grants the S3 and CloudFront permissions required by the pipeline.

<img width="2559" height="1076" alt="image" src="https://github.com/user-attachments/assets/206441e4-66db-4955-b3c8-ecc9b23509ab" />

## CI/CD with GitHub Actions

Repository structure:

```text
aws-cloud-resume-challenge/
├── .github/
│   └── workflows/
│       └── front-end-ci-cd.yml
└── website/
    └── index.html
```

Every push to `main` triggers:

<img width="1407" height="1077" alt="image" src="https://github.com/user-attachments/assets/99b83b06-1cb6-4bab-8e37-5895b6d34d26" />

The completed pipeline successfully authenticates with AWS, uploads the website to S3, and invalidates the CloudFront cache.

<img width="2559" height="788" alt="image" src="https://github.com/user-attachments/assets/fb1571b2-c783-4b2f-ad3d-17b439a85d52" />

### Final Workflow

```yaml
name: Deploy Website

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Upload website to S3
        run: |
          aws s3 sync ./website s3://${{ secrets.AWS_S3_BUCKET }}             --delete

      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation             --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }}             --paths "/*"
```

<img width="1620" height="1074" alt="image" src="https://github.com/user-attachments/assets/dfc71b28-c874-4c0d-990d-da84c5a600c1" />


## Technologies

- Amazon S3
- Amazon CloudFront
- Amazon Route 53
- AWS Certificate Manager
- AWS Lambda
- Amazon DynamoDB
- AWS IAM
- GitHub Actions
- GitHub OIDC
- HTML, CSS, JavaScript
- Git and GitHub

## Key Learning Outcomes

- Static website hosting and CDN delivery on AWS
- Custom DNS and HTTPS configuration
- Serverless development with Lambda and DynamoDB
- Atomic DynamoDB updates
- Lambda Function URLs and CORS
- IAM roles and least-privilege permissions
- GitHub OIDC federation with AWS
- Automated CI/CD with GitHub Actions
- S3 synchronization and CloudFront invalidation

## Project Status

- **Frontend CI/CD:** Complete
- **AWS Hosting:** Complete
- **Custom Domain + HTTPS:** Complete
- **Serverless Visitor Counter:** Complete
- **GitHub OIDC Authentication:** Complete

## Author

**Vandit Jaiswal**
Cloud & DevOps Enthusiast

