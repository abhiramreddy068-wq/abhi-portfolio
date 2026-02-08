# Portfolio Deployment Guide

## Prerequisites
- AWS Account
- GitHub Repository
- AWS CLI installed locally

## Step 1: Create S3 Bucket

```bash
aws s3 mb s3://your-portfolio-bucket-name --region us-east-1
```

Enable static website hosting:
1. Go to S3 bucket settings
2. Properties → Static website hosting → Enable
3. Index document: `portfolio.html`
4. Error document: `portfolio.html`

## Step 2: Create IAM User for GitHub Actions

1. Go to IAM → Users → Create user
2. Name: `github-actions-user`
3. Attach policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": [
        "arn:aws:s3:::your-portfolio-bucket-name",
        "arn:aws:s3:::your-portfolio-bucket-name/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "cloudfront:CreateInvalidation"
      ],
      "Resource": "*"
    }
  ]
}
```

4. Create access key and save credentials

## Step 3: Add GitHub Secrets

In your GitHub repository:
1. Settings → Secrets and variables → Actions
2. Add these secrets:
   - `AWS_ACCESS_KEY_ID`: Your IAM user access key
   - `AWS_SECRET_ACCESS_KEY`: Your IAM user secret key
   - `S3_BUCKET_NAME`: your-portfolio-bucket-name
   - `CLOUDFRONT_DISTRIBUTION_ID`: (optional, for caching)

## Step 4: Push to GitHub

```bash
git init
git add .
git commit -m "Initial portfolio commit"
git branch -M main
git remote add origin https://github.com/abhiramreddy068-wq/abhi-portfolio.git
git push -u origin main
```

## Step 5: Access Your Portfolio

Your portfolio will be available at:
`http://your-portfolio-bucket-name.s3-website-us-east-1.amazonaws.com`

Or with a custom domain via CloudFront/Route53.

## Automatic Deployment

Every push to the `main` branch will automatically:
1. Deploy files to S3
2. Invalidate CloudFront cache (if configured)
