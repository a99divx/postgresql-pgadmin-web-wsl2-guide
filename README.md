![PostgreSQL and pgAdmin Logo](/assets/setup-postgresql-pgadmin-wsl2.png)


# 🚀 **Setting Up PostgreSQL and pgAdmin on Ubuntu (WSL2) for Web Developers**

Welcome to the ultimate guide for installing **PostgreSQL** and **pgAdmin** on **Ubuntu (WSL2)**, all while running on Windows! 🎉 This guide walks you through every step, making sure your database environment is up and running smoothly, with a little fun along the way! 🌟


> 🚀 **Ready to supercharge your Windows 11 development environment?** Fork this repository to keep track of your progress and customize your setup! 
> 
> [![Fork the Repo](https://img.shields.io/github/forks/a99divx/postgresql-pgadmin-web-wsl2-guide?style=social)](https://github.com/a99divx/postgresql-pgadmin-web-wsl2-guide/fork)
> [![Star the Repo](https://img.shields.io/github/stars/a99divx/postgresql-pgadmin-web-wsl2-guide?style=social)](https://github.com/a99divx/postgresql-pgadmin-web-wsl2-guide)
> 
> 📝 **Track Your Progress**: Use the [Issues](https://github.com/a99divx/postgresql-pgadmin-web-wsl2-guide/issues) tab to create a checklist of tasks as you work through this guide.
> 
> 🔧 **Customize Your Setup**: Share your own tips and tricks by [contributing](https://github.com/a99divx/postgresql-pgadmin-web-wsl2-guide/blob/main/CONTRIBUTING.md) to this guide!
> 
> 💡 **Get Help**: Join our [Discussions](https://github.com/a99divx/postgresql-pgadmin-web-wsl2-guide/discussions) to ask questions and share your experiences with other developers.



## 📚 **Table of Contents**

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Installing PostgreSQL](#installing-postgresql)
4. [Creating and Managing PostgreSQL Clusters](#creating-and-managing-postgresql-clusters)
5. [Installing and Configuring pgAdmin (Web Version)](#installing-and-configuring-pgadmin-web-version)
6. [Final Verification](#final-verification)
7. [Tips for Productivity](#tips-for-productivity)
8. [Conclusion](#conclusion)
9. [Additional Resources](#additional-resources)

---

## 📝 **Introduction**

When it comes to managing databases, you need powerful tools! 💪 **PostgreSQL** is one of the most powerful open-source relational databases available, and **pgAdmin** is the perfect web-based GUI to interact with it. Let’s make sure you have everything set up inside your **Ubuntu (WSL2)** environment. No need for clunky Windows apps—we’ll install pgAdmin directly in **WSL** as a **web version**! 🌐

---

## ⚙️ **Prerequisites**

Before diving in, make sure you have the following:

- **Windows 10/11**: With **WSL2** enabled.
- **Ubuntu (WSL2)**: Installed from the Microsoft Store.
- **Admin Access**: To run `sudo` commands.
- **Internet Connection**: To download all necessary packages.

### 🛠️ **Quick WSL2 Setup (Optional)**

If you haven’t installed WSL2 yet, here’s a quick way to do it:

1. Open **PowerShell** as Administrator and run:
   ```bash
   wsl --install
   ```

2. Download **Ubuntu** from the **Microsoft Store** and launch it to complete the setup (create a username and password).

---

## 🐘 **Installing PostgreSQL**

Let's get PostgreSQL up and running first! PostgreSQL will serve as the core database engine for your web development projects. 🚀

### Step 1: Update Your Package List

Before installing anything, it’s always a good idea to update your system:

```bash
sudo apt-get update
```

### Step 2: Install PostgreSQL

Now, install PostgreSQL along with some additional utilities:

```bash
sudo apt-get install postgresql postgresql-contrib
```

👨‍💻 **What’s Happening?**  
- `postgresql`: The core PostgreSQL database.
- `postgresql-contrib`: Extra useful tools and extensions (like `pgcrypto`, `adminpack`, etc.).

### Step 3: Verify the Installation

To make sure everything is installed correctly, check the version:

```bash
psql --version
```

✅ You should see output like this:

```
psql (PostgreSQL) 14.13 (Ubuntu 14.13-0ubuntu0.22.04.1)
```

Boom! PostgreSQL is installed and ready to go! 🎉

---

## 🛠️ **Creating and Managing PostgreSQL Clusters**

PostgreSQL uses **clusters** to manage databases. We’re going to create a new cluster that runs on a different port, so you can keep things organized.

### Step 1: Create a New Cluster (the Default Cluster named ‘main’ is running on port 5432)

Run the following command to create a cluster on port `5436`:

```bash
sudo pg_createcluster 16 main5436 --port=5436
```

This will create a new cluster for **PostgreSQL version 16**. Clusters are isolated instances of PostgreSQL.

### Step 2: Start the Cluster

Next, start your new cluster:

```bash
sudo service postgresql@16-main5436 start
```

### Step 3: Check Cluster Status

Let’s check if everything is running smoothly:

```bash
sudo service postgresql@16-main5436 status
```

🎉 **Success!** If you see the output saying `Active: active (running)`, your cluster is live and ready to handle databases.

### Step 4: Set a Password for the `postgres` User

Switch to the PostgreSQL user and set a password for your main database user:

```bash
sudo -i -u postgres

## run with the -p parameter to connect to the NEW cluster
psql -p 5436
```

Then set the password for the `postgres` user:

```sql
ALTER USER postgres PASSWORD 'yourpassword';
```

Exit the PostgreSQL prompt by typing `\q`.

### Step 5: Verify that you can login with your `postgres` User:

```bash
exit  # go back to your default shell, or reopen your terminal

## run with the -p parameter to connect to the NEW cluster
psql -h localhost -U postgres -p 5436

# it should ask you for the new password and you should be able to login.
```

Exit the PostgreSQL prompt by typing `\q`.

### Step 5:  🚦 Dealing with the Default Cluster
By default, when you install PostgreSQL, it creates an auto-start cluster named `main` on port `5432`. This cluster is automatically managed by your system’s init process, meaning it starts when your system boots.

If you’ve created additional clusters (like the one on port `5436`), it’s helpful to know how to manage them:

**Checking Active Clusters**
To see all the clusters currently configured (and whether they’re running):

```bash
pg_lsclusters
```
You’ll see each cluster’s PostgreSQL version, name, port, and status.


1. **Stop the Default Cluster**:
    
    ```bash
    sudo pg_ctlcluster 16 main stop
    ```
    
2. **Edit the start.conf File**:
    
    Open the **`start.conf`** file for the default cluster:
    
    ```bash
    sudo nano /etc/postgresql/16/main/start.conf
    ```
    
    If you see the word **`auto`** in that file, change it to **`manual`**.
    
3. **Save and Close**: Press **`Ctrl+O`** to save, then **`Ctrl+X`** to exit.

Now, when you start/restart PostgreSQL via **`sudo service postgresql start`**, the **`16/main`** cluster won’t start automatically. To start it manually at any time, just run:

```bash
sudo pg_ctlcluster 16 main start
```


### Managing Clusters

Use the following commands to manage your clusters:

- **Start a Cluster**:  
  ```bash
  sudo pg_ctlcluster <version> <cluster_name> start
  ```
  
- **Stop a Cluster**:  
  ```bash
  sudo pg_ctlcluster <version> <cluster_name> stop
  ```

- **Restart a Cluster**:  
  ```bash
  sudo pg_ctlcluster <version> <cluster_name> restart
  ```

- **Reload Configuration** (without restarting):  
  ```bash
  sudo pg_ctlcluster <version> <cluster_name> reload
  ```

Now you have full control over your clusters—start only what you need, stop what you don’t, and keep your environment lean and efficient!



---

## 🌐 **Installing and Configuring pgAdmin (Web Version)**

Now, let's move on to **pgAdmin**, the GUI for managing PostgreSQL databases. Instead of using the desktop version, we’ll install **pgAdmin** as a **web-based** application inside **WSL2**. This means you'll access pgAdmin through your browser, making it lightweight and flexible! 💻

### Step 1: Add the pgAdmin Repository

First, add the pgAdmin repository to your package list:

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
```

Then, add the repository to your list of sources:

```bash
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
```

### Step 2: Install pgAdmin

Run the following command to install the web version of pgAdmin:

```bash
sudo apt install pgadmin4-web
```

📢 **Note:** This is a **web version** of pgAdmin, which you will access via your web browser! No need to install the Windows app. 🙌

### Step 3: Configure pgAdmin

After installation, set it up for web mode:

```bash
sudo /usr/pgadmin4/bin/setup-web.sh
```

During setup, you’ll be prompted to:

1. Enter your email address (this will be your login).
2. Create a strong password for the admin account.

### Step 4: Apache Configuration for pgAdmin

The setup script will configure Apache to serve pgAdmin. When prompted to restart Apache, press `y`.

You can now access pgAdmin by navigating to  http://127.0.0.1/pgadmin4   or   http://localhost/pgadmin4

🎉 You’re now able to manage your PostgreSQL databases using pgAdmin, right from your browser! 🚀

---

## ✅ **Final Verification**

Let’s make sure everything is working perfectly.

1. **Access pgAdmin**: Open your browser and navigate to `http://127.0.0.1/pgadmin4` or `http://localhost/pgadmin4`.
2. **Login**: Use the admin credentials you just created.
3. **Connect to PostgreSQL**: Inside pgAdmin, create a new server connection:
   - **Hostname**: `localhost` or `127.0.0.1`
   - **Port**: `5436` (for the new cluster you created)
   - **Username**: `postgres`
   - **Password**: The password you set earlier.

🎉 Congratulations! You now have **PostgreSQL** and **pgAdmin** fully configured and ready to use on **WSL2**!

---

## 💡 **Tips for Productivity**

Here are some quick tips to boost your PostgreSQL and pgAdmin workflow:

- **pgAdmin Bookmarks**: Bookmark the pgAdmin URL (`http://127.0.0.1/pgadmin4` or `http://localhost/pgadmin4`) for quick access.
- **PostgreSQL Tuning**: Fine-tune your PostgreSQL configuration by editing `postgresql.conf` to optimize for development or production.
- **Backup & Restore**: Use pgAdmin’s easy-to-use backup and restore features to safeguard your databases.
- **Docker**: If you prefer containerization, you can also run PostgreSQL in **Docker** alongside this setup for even more flexibility!

---

## 📖 **Conclusion**

You’ve just set up a fully functional **PostgreSQL** and **pgAdmin (web version)** environment inside **WSL2**! 🎉 This setup is optimized for web developers, making database management both simple and efficient. By using the web version of pgAdmin, you avoid the need for Windows-based applications, keeping your development flow seamless.

If you found this guide helpful, feel free to share it with fellow developers, or contribute to enhancing this guide on GitHub! 🙌


---

## 📚 **Additional Resources**

- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/)
- [pgAdmin Documentation](https://www.pgadmin.org/docs/)
- [WSL 2 Documentation](https://docs.microsoft.com/en-us/windows/wsl/)

