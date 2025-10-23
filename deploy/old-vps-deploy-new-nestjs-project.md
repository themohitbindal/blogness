
# 🚀 Deploying Another NestJS App on the Same EC2 Instance

You already have one NestJS app running on your EC2.  
Now you want to deploy **another one** — no need to repeat all the setup steps (like installing Node.js, PM2, etc.).  
We’ll just add the **new app** while keeping the old one running.

---

## 🧠 Intuitive Idea

Your EC2 is like your **computer**.  
Your first app is already installed and running on it — just like having one project folder on your laptop.  
To deploy another app, you just create another project folder, install dependencies, and run it on a **different port**.

---

## 🪜 Step 1: Connect to EC2

SSH into your EC2 as before:

```bash
ssh -i /path/to/your-key.pem ec2-user@<your-ec2-ip>
```

---

## 📂 Step 2: Create a Folder for the New App

Keep things organized:

```bash
cd ~
mkdir second-app
cd second-app
```

> **Intuition:** Think of this like creating a new project directory on your local computer.

---

## 🧾 Step 3: Clone or Upload Your New NestJS App

If it’s on GitHub:

```bash
git clone <your-second-repo-url> .
```

If you’re uploading manually, you can use `scp` or `rsync` from your local PC.

---

## 📦 Step 4: Install Dependencies

Install everything the new app needs:

```bash
npm install
```

> **Intuition:** You’re just giving this new app its own tools — even though Node.js is already installed globally.

---

## ⚙️ Step 5: Configure Environment Variables

Each app should have its own `.env` file (for DB credentials, port, etc.).

```bash
nano .env
```

Make sure to **set a different port**, for example:

```
PORT=4000
```

> **Intuition:** Two apps can’t both talk on port 3000 — it’s like two people trying to use the same microphone.

---

## 🧪 Step 6: Build and Test the App

If your app needs a build:

```bash
npm run build
```

Then test it:

```bash
npm run start
```

Check it works at:

```
http://<your-ec2-ip>:4000
```

If you see it running — perfect!

---

## ⚙️ Step 7: Run the New App with PM2

Now let’s make it run permanently in the background.

```bash
pm2 start dist/main.js --name second-nest-app --env production
pm2 save
```

> **Intuition:** PM2 is already installed and managing your first app — you’re just asking it to manage one more.

You can see all running apps with:

```bash
pm2 list
```

---

## 🌐 Step 8: Allow Access to the New Port

If your new app runs on port 4000, go to **EC2 → Security Group → Inbound Rules** and add:

| Type | Protocol | Port | Source |
|------|-----------|------|--------|
| Custom TCP | TCP | 4000 | 0.0.0.0/0 |

> **Intuition:** You’re telling AWS firewall to allow the new “door” (port 4000) to be open to visitors.

---

## 🧱 Step 9 (Optional): Add Nginx Proxy for Multiple Apps

If you’re using Nginx, you can host both apps behind the same domain or subdomain.

Example Nginx config snippet:

```
server {
    listen 80;
    server_name app1.example.com;

    location / {
        proxy_pass http://localhost:3000;
    }
}

server {
    listen 80;
    server_name app2.example.com;

    location / {
        proxy_pass http://localhost:4000;
    }
}
```

Then restart Nginx:

```bash
sudo systemctl restart nginx
```

> **Intuition:** Nginx is your “traffic controller” — it decides which app gets which visitors based on the address.

---

## ✅ Summary

| Step | What You Did | Why |
|------|---------------|-----|
| 1 | Connected to EC2 | Access your server |
| 2 | Made a new folder | Keep apps separate |
| 3 | Cloned new repo | Bring in second app |
| 4 | Installed deps | Setup for second app |
| 5 | Changed port | Avoid conflicts |
| 6 | Tested locally | Make sure it runs |
| 7 | Used PM2 | Run in background |
| 8 | Opened new port | Allow external access |
| 9 | (Optional) Used Nginx | Handle multiple apps smoothly |

---

Now both of your NestJS apps run independently on the same EC2 — one on port 3000 and the other on port 4000 (or any port you choose).  
Your EC2 is now a **multi-app VPS**, just like hosting multiple websites on one machine 🎉
