

# 📘 Host a Static Website on Amazon S3

This guide walks you through creating an S3 bucket and configuring it to host a static website. It includes setup, permissions, file upload, and public access steps.

---

## 🪣 Step 1: Create an S3 Bucket

1. Go to the **AWS S3 Console** and click **Create bucket**
2. Set the following:
   - **Bucket name**: Must be globally unique (e.g., `s3demo-restaurant-bucket`)
   - **Region**: Choose your preferred AWS region (e.g., `us-east-1`)
   - **Bucket type**: Select **General purpose**

---

## 🔐 Step 2: Configure Bucket Settings

### Object Ownership
- Select **ACLs disabled (recommended)** → This enforces *bucket owner control*

### Block Public Access
- Initially, **enable all four options** to block public access:
  - Block all public access
  - Block access via new ACLs
  - Block access via any ACLs
  - Block access via public bucket policies

> ⚠️ You’ll disable these later to allow public website access.

### Versioning & Encryption
- **Versioning**: Leave **Disabled** unless you want to track file changes
- **Encryption**: Choose **SSE-S3** (Amazon-managed keys) for simplicity

---

## 🌐 Step 3: Enable Static Website Hosting

1. Go to the bucket’s **Properties** tab
2. Scroll to **Static website hosting**
3. Enable it and set:
   - **Index document**: `index.html`
   - **Error document**: `error.html` (optional)

---

## 📜 Step 4: Set Bucket Policy for Public Access

1. Go to the **Permissions** tab → **Bucket Policy**
2. Paste the following JSON to allow public read access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::s3demo-restaurant-bucket/*"
    }
  ]
}
```

> Replace `s3demo-restaurant-bucket` with your actual bucket name.

3. Save the policy

---

## 🚫 Step 5: Disable Block Public Access

1. Go to **Permissions** → **Block Public Access**
2. Click **Edit**
3. **Turn off “Block all public access”**
4. Confirm changes

---

## 📁 Step 6: Upload Website Files

1. Go to the **Objects** tab
2. Click **Upload**
3. Add your `index.html`, `error.html`, and any assets (CSS, JS, images)
4. Click **Upload**

---

## 🌍 Step 7: Access Your Website

- Go to **Properties** → **Static website hosting**
- Copy the **Endpoint URL**
- Paste it in your browser — your site is live!

---

## ✅ Final Notes

- **No need to use ACLs** — bucket policies are sufficient
- **Object Lock** is disabled unless you need WORM compliance
- **Tags** are optional but useful for cost tracking
- **Encryption** is recommended for sensitive content, but not required for public websites
