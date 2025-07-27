
Before starting Deploy Static Website to AWS S3 using Jenkins Pipeline, make sure:

1. ✅ **Static website project** (HTML, CSS, JS) ready
2. ✅ Pushed to **GitHub repo**
3. ✅ AWS credentials with S3 access
4. ✅ Jenkins is installed (on local or EC2)
5. ✅ Jenkins has internet access (to reach GitHub & AWS)

---

## 🧱 Project Structure Example

```
static-website/
├── index.html
├── about.html
├── css/
├── js/
└── images/
```

---

## 🌍 Step 1: Create S3 Bucket for Static Hosting

1. Go to **AWS S3 → Create bucket**

   * Bucket name: `my-static-website-manish`
   * Region: `us-east-1` (or your preferred)
   * Uncheck ✅ **Block all public access**

2. Enable **Static Website Hosting**

   * Index document: `index.html`
   * Error document: `index.html`

3. Add this **bucket policy**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowPublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-static-website-manish/*"
    }
  ]
}
```

---

## ⚙️ Step 2: Install AWS CLI on Jenkins Server

SSH into the Jenkins server and install the AWS CLI:

```bash
sudo apt update
sudo apt install awscli -y
aws configure
```

Provide:

* AWS Access Key
* AWS Secret Key
* Region: `us-east-1`

---

## 🔐 Step 3: Add AWS Credentials in Jenkins

1. Go to **Jenkins → Manage Jenkins → Credentials**
2. Under **(Global)** → Add two credentials:

| Type        | ID                      | Value (from IAM)           |
| ----------- | ----------------------- | -------------------------- |
| Secret Text | `aws-access-key-id`     | Your AWS Access Key ID     |
| Secret Text | `aws-secret-access-key` | Your AWS Secret Access Key |

---

## 📄 Step 4: Create `Jenkinsfile` in Your GitHub Repo

Create a `Jenkinsfile` in your GitHub repo root:

```groovy
pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'us-east-1'
        S3_BUCKET             = 'my-static-website-manish'
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/Manish636700/static-website.git'
            }
        }

        stage('Deploy to S3') {
            steps {
                sh '''
                echo "Deploying site to S3..."
                aws s3 sync . s3://$S3_BUCKET --delete
                '''
            }
        }
    }
}
```

---

## 🔨 Step 5: Create Jenkins Pipeline Job

1. Go to **Jenkins Dashboard → New Item**

2. Enter name: `static-site-deploy`

3. Choose: **Pipeline**

4. Under **Pipeline → Definition: Pipeline script from SCM**

   * SCM: Git
   * Repository: `https://github.com/Manish636700/static-website.git`
   * Branch: `main`
   * Script Path: `Jenkinsfile`

5. Save → Click **Build Now**

---

## 🌐 Step 6: Visit Your Website

After build success, open:

```
http://my-static-website-manish.s3-website-us-east-1.amazonaws.com
```

You should see your site live!

---

## 🔁 (Optional) Automate with GitHub Webhooks

1. In GitHub → Repo Settings → Webhooks → Add Webhook

   * Payload URL: `http://<your-jenkins-server>:8080/github-webhook/`
   * Content-Type: `application/json`

2. In Jenkins job:

   * Configure → Check ✅ **GitHub hook trigger for GITScm polling**

Now your deployment will trigger automatically when code is pushed.

---

## ✅ Done! Deployment Flow

```plaintext
GitHub Repo (HTML/CSS/JS) ➜ Jenkins ➜ AWS S3 (Live Website)
```

---

Do you want me to:

* Help you build the GitHub repo structure?
* Setup this on a fresh Jenkins server (via Ansible)?
* Add CloudFront or a custom domain (Route 53)?

Let me know!
