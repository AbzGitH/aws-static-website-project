# Website Updates & Deployment Guide

## Overview

The portfolio at https://abscloud.dev uses manual deployment.

Updating GitHub does not automatically update the live website.

**Deployment flow:**

1. Edit and test HTML/CSS locally.
2. Review, commit and push changes to GitHub.
3. Upload updated website files to Amazon S3.
4. Invalidate the Amazon CloudFront cache.
5. Verify the live website on both domains.

## Prerequisites

- Git and AWS CLI installed.
- AWS CLI configured with authorised credentials.
- S3 upload and CloudFront invalidation permissions.
- Terminal opened in the repository root.

**Existing deployment configuration:**

- AWS Region: `eu-west-2` (London)
- S3 bucket: `abs-cloud-p1`
- Domains: `abscloud.dev` and `www.abscloud.dev`
- CloudFront distribution: identify and verify using the CLI.

## 1. Review and Push to GitHub

Review changed files:

```bash
git status --short
git diff --check
git diff
```

Stage only the intended files, for example:

```bash
git add index.html README.md docs/deployment.md .gitignore
git diff --cached --check
git diff --cached
```

Review the staged changes before proceeding. Include `style.css`
in `git add` only when it has been intentionally modified.
Never stage private local documents.

Commit and push approved changes:

```bash
git commit -m "docs: document portfolio website updates"
git push origin main
git status --short
```

**Result:** The GitHub repository is updated.
The live AWS website is not updated yet.

## 2. Verify the AWS Deployment Destination

Confirm the active AWS identity:

```bash
aws sts get-caller-identity
```

Confirm the bucket region:

```bash
aws s3api get-bucket-location --bucket abs-cloud-p1
```

Identify the CloudFront distribution:

```bash
aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items && contains(Aliases.Items, 'abscloud.dev')].[Id,DomainName,Origins.Items[0].DomainName]" --output table
```

Check that the distribution points to the expected S3 origin.
Stop if the AWS account, bucket or distribution does not match.

## 3. Upload Updated Website Files to S3

For an HTML update:

```bash
aws s3 cp index.html s3://abs-cloud-p1/index.html --content-type "text/html" --region eu-west-2
```

For a CSS update:

```bash
aws s3 cp style.css s3://abs-cloud-p1/style.css --content-type "text/css" --region eu-west-2
```

Upload only changed website assets.

**What this does:** Copies local website files into the live S3
bucket. GitHub files and documentation are not automatically uploaded.

## 4. Invalidate the CloudFront Cache

Retrieve the distribution ID:

```bash
DIST_ID=$(aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items && contains(Aliases.Items, 'abscloud.dev')].Id | [0]" --output text)
printf 'Distribution ID: %s\n' "$DIST_ID"
```

Verify that the ID matches the intended distribution.
Stop if the result is empty or `None`.

Then request a full cache invalidation:

```bash
aws cloudfront create-invalidation --distribution-id "$DIST_ID" --paths '/*'
```

**Why:** CloudFront may otherwise continue serving cached versions
of the old HTML or CSS.

Record the invalidation ID returned by AWS. Check its status:

```bash
aws cloudfront get-invalidation --distribution-id "$DIST_ID" --id YOUR_INVALIDATION_ID --query 'Invalidation.Status' --output text
```

Replace `YOUR_INVALIDATION_ID` with the actual returned ID.
Expected final status: `Completed`.

CloudFront invalidations may incur charges depending on usage.

## 5. Verify the Live Website

Open both:

- https://abscloud.dev
- https://www.abscloud.dev

Confirm:

- The latest HTML content is visible.
- CSS styling is correct.
- All GitHub project links work.
- Both domains display consistent versions.
- Desktop and mobile layouts remain usable.

Check that GitHub contains the intended source updates.

## Troubleshooting

If updates are missing:

1. Verify the correct S3 bucket and uploaded object paths.
2. Confirm the CloudFront distribution matches the website.
3. Wait for invalidation status `Completed`.
4. Compare both domains.
5. Refresh the browser or test in a private window.
6. Refer to [Troubleshooting](troubleshooting.md).

**Important:** A successful Git push does not prove that
the live website has been deployed successfully.
