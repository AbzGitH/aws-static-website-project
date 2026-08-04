# AWS Static Website Project

## Overview

This project demonstrates the deployment of a static website on AWS. It highlights practical cloud deployment, secure website hosting, and custom domain configuration.

## Architecture

- Amazon S3 hosts the static website files.
- Amazon CloudFront securely delivers the website worldwide and caches content for improved performance.
- Amazon Route 53 routes requests from the custom domain to CloudFront.
- AWS Certificate Manager (ACM) provides the SSL/TLS certificate for HTTPS.

### Architecture Flow

```text
User
  │
  ▼
Route 53 (DNS)
  │
  ▼
CloudFront (CDN + HTTPS)
  │
  ▼
Amazon S3 (Static Website)
```

## AWS Services Used

- Amazon S3
- Amazon CloudFront
- Amazon Route 53
- AWS Certificate Manager (ACM)

## Skills Demonstrated

- Static website hosting
- DNS management
- Content Delivery Networks (CDN)
- HTTPS and SSL certificates
- Custom domain configuration
- Git and GitHub version control

### Challenges & Troubleshooting

During deployment I encountered several real-world issues that required troubleshooting.

- **Challenge:** Live website updates did not always appear after deployment.  
  **Troubleshooting:** Verified the local site, GitHub repository and AWS deployment to isolate the issue.

- **Challenge:** Different versions were displayed on `www.abscloud.dev` and `abscloud.dev`.  
  **Troubleshooting:** Compared both domains and confirmed the issue was related to cached content.

- **Challenge:** CSS changes were not immediately reflected on the live website.  
  **Troubleshooting:** Tested across multiple website endpoints and browsers before making further changes.

## Lessons Learned

This project reinforced several important cloud engineering practices:

- Test changes locally before deploying.
- Verify each stage of the deployment before assuming something is broken.
- Compare multiple endpoints to help identify where an issue exists.
- Make one change at a time when troubleshooting.
- Browser and CDN caching can delay visible updates after deployment.

## Live Website

https://abscloud.dev

## Project Documentation

Supporting deployment documentation and evidence are available below.

- [Deployment Screenshots](screenshots/) – Key stages of the AWS deployment and final live website.
- [Architecture Diagram](diagrams/) – Visual representation of the AWS solution architecture.
