
# 🚀 Deploying NestJS App on AWS EC2 (Step-by-Step + Intuitive Guide)

This guide is made for beginners — especially if you're coming from a **normal VPS** and just started using **AWS EC2**.  
We’ll go from zero (fresh instance) to a live NestJS backend running 24/7.

---

## 🧠 Intuitive Overview

Think of your EC2 instance as your **new computer in the cloud**.  
Just like your old VPS, you can log in using SSH, install stuff, and run apps — but AWS adds a few more steps for **security and flexibility**.

We’ll do 10 steps — all easy to follow.

---

## 🪜 Step 1: Connect to Your EC2

Open your terminal and connect using your **.pem key**:

```bash
ssh -i /path/to/your-key.pem ec2-user@<your-ec2-ip>
```

> Example:  
> `ssh -i my-key.pem ec2-user@13.233.45.67`

If you see something like `[ec2-user@ip-xxx ~]$`, you’re **inside your EC2** 🎉

---

## ⚙️ Step 2: Update the System

Always keep your EC2 updated before installing anything.

```bash
sudo yum update -y        # For Amazon Linux
# or for Ubuntu:
# sudo apt update && sudo apt upgrade -y
```

> **Why:** Think of it like updating your Windows or macOS before installing software — it avoids conflicts and missing libraries.

---

## 🧰 Step 3: Install Node.js and npm

NestJS runs on Node.js. Install version 18+.

```bash
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs
```

Check if it’s installed:

```bash
node -v
npm -v
```

> **Intuition:** Node.js is like the "engine" that runs your NestJS code. npm is the “app store” that gives it all the tools it needs.

---

## 🧾 Step 4: Install Git

We’ll use Git to bring your project code from GitHub.

```bash
sudo yum install git -y
```

Then clone your repo:

```bash
git clone <your-repo-url>
cd <your-project-folder>
```

---

## 🧩 Step 5: Install Project Dependencies

Inside your project folder:

```bash
npm install
```

> **Intuition:** npm reads your `package.json` and installs everything your app depends on — like setting up your toolbox before work.

---

## 🔐 Step 6: Add Environment Variables

If your app has `.env` or `.env.example`, create or edit it:

```bash
cp .env.example .env
nano .env
```

Add database URLs, secrets, etc.  

> **Tip:** If using Postgres (like in RDS or EC2), make sure the database is reachable.

---

## 🧪 Step 7: Test the App

Start the app manually first to ensure it works:

```bash
npm run start
```

You should see:

```
[Nest] 1234  - Listening on port 3000
```

Try opening your EC2 public IP with `:3000` (e.g., `13.233.45.67:3000`) in your browser.

---

## ⚙️ Step 8: Run App in Background (PM2)

To keep your app running even after logout, use **PM2**.

Install PM2 globally:

```bash
sudo npm install -g pm2
```

Start your app:

```bash
pm2 start dist/main.js --name nestjs-app
```

Save PM2 so it auto-starts on reboot:

```bash
pm2 startup
pm2 save
```

> **Intuition:** PM2 is like a “manager” that keeps your app alive — if the app crashes or the EC2 reboots, PM2 restarts it automatically.

---

## 🌐 Step 9: Allow Web Access (Security Group)

Go to **AWS Console → EC2 → Security Groups → Inbound Rules**  
Add:

| Type | Protocol | Port | Source |
|------|-----------|------|--------|
| Custom TCP | TCP | 3000 | 0.0.0.0/0 |

> **Meaning:** This opens your app’s port (3000) to the internet.  
> Think of it like telling AWS, “Let people knock on my app’s door.”

---

## 🧱 Step 10 (Optional): Use Nginx as Reverse Proxy

Nginx helps route normal HTTP traffic (port 80) to your NestJS app (port 3000).

Install and start Nginx:

```bash
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

Edit its config to forward traffic:

```bash
sudo nano /etc/nginx/nginx.conf
```

Find the `location /` block and modify it like this:

```
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Now you can visit your EC2’s public IP **without :3000** — just `http://13.233.45.67`.

> **Intuition:** Nginx stands at the door (port 80) and forwards all visitors to your NestJS app (port 3000) behind the scenes.

---

## ✅ Summary

| Step | What You Did | Why |
|------|---------------|-----|
| 1 | SSH into EC2 | Connect to your remote computer |
| 2 | Update system | Keep things secure and fresh |
| 3 | Install Node.js | Engine for your app |
| 4 | Clone project | Bring your code to EC2 |
| 5 | Install dependencies | Set up your app’s tools |
| 6 | Configure `.env` | Store secrets and DB info |
| 7 | Test locally | Ensure it runs before production |
| 8 | Run via PM2 | Keep it alive in background |
| 9 | Open port 3000 | Allow public access |
| 10 | Use Nginx | Make it production-ready |

---

## 💡 Final Note

This setup is **perfect for beginners** — simple, stable, and 100% under your control.  
Once you’re comfortable, you can later explore:  
- **AWS RDS** (for managed Postgres)  
- **Elastic Beanstalk** (for automatic app deployment)  
- **Docker + ECS** (for scalable production)  

But for now — enjoy your working EC2 + NestJS app! 🎉
