# **Deploying OpenStack on an EC2 Instance**

This guide provides detailed instructions on how to set up **OpenStack** on an **AWS EC2 instance** using **DevStack**. By following these steps, you will be able to successfully deploy OpenStack and access the Horizon dashboard for managing your cloud resources.

---

## **Definitions:**

- **OpenStack:** An open-source software platform for cloud computing that enables the creation and management of virtualized infrastructure resources such as compute, networking, and storage. It is commonly used for building private and public cloud environments.

- **EC2 Instance:** A virtual server provided by Amazon Web Services (AWS) as part of the Elastic Compute Cloud (EC2) service. It allows users to run applications and services on virtual machines in the AWS cloud.

- **DevStack:** A set of scripts used to quickly deploy OpenStack on a single machine for development, testing, and learning purposes. It is not recommended for production use but is useful for testing or experimenting with OpenStack.

- **Horizon Dashboard:** The web-based user interface (UI) for managing OpenStack services. It allows users to perform tasks such as creating and managing virtual machines, networks, and volumes through a graphical interface.

- **local.conf:** A configuration file used by DevStack to specify installation options, such as passwords, service configurations, and which services to enable or disable during the OpenStack setup process.

- **Git:** A distributed version control system that tracks changes to files and enables collaboration on projects. Git is required to clone the DevStack repository to install OpenStack.

- **SSH:** A secure network protocol that allows users to remotely access and manage servers. In this guide, SSH is used to connect to the EC2 instance from your local machine.

- **Ubuntu:** A popular Linux-based operating system used for cloud deployments. It is commonly used with OpenStack in cloud environments due to its stability, security, and ease of use.

---

## **Prerequisites**

### **1. AWS EC2 Instance Specifications**

- **AMI (Operating System):** Ubuntu 22.04 LTS (Latest Stable Version)
- **Instance Type:** `t2.large` (Minimum: 2 vCPUs, 8GB RAM)
- **Storage:** Allocate at least **30GB** of storage (the default 8GB is not enough for OpenStack)
- **Key Pair:** Create or use an existing SSH key for secure login.
- **Security Group Configuration:**
  - **SSH (Port 22):** Allow access from your IP address.
  - **HTTP (Ports 80, 443):** Open access for the OpenStack Horizon dashboard.
![git](https://github.com/user-attachments/assets/3813f3e6-6e43-455b-904c-b4351cfd7770)



### **2. Software Requirements**

- **PuTTY** or **Terminal** for SSH access
- **PuTTYgen** (if using PuTTY on Windows) for converting keys
- **Git** (to clone the DevStack repository)
- **Python 3** and its dependencies

---

## **1. Accessing the EC2 Instance**

### **For Windows Users (Using PuTTY)**

1. **Convert PEM to PPK (PuTTY Private Key Format):**

   - Launch **PuTTYgen**.
   - Click **Load**, choose your `.pem` file.
   - Save the key as a `.ppk` file by clicking **Save private key**.

2. **Connect to the EC2 Instance Using PuTTY:**
   - Open **PuTTY**.
   - Enter the public IP of the EC2 instance in the **Host Name** field.
   - Go to **Connection > SSH > Auth**, click **Browse**, and select the `.ppk` file.
   - Click **Open** to connect and log in as `ubuntu`.

![putty](./Images/putty.png)

### **For macOS & Linux Users (Using Terminal)**

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

---

## **2. Update System and Install Dependencies**

Before beginning the installation, ensure that your system is up-to-date and the required packages are installed:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3-dev python3-pip net-tools
```

---

## **3. Clone the DevStack Repository**

Navigate to your home directory and clone the latest version of the DevStack repository:

```bash
git clone https://opendev.org/openstack/devstack.git
cd devstack
```

---

## **4. Create a DevStack Configuration File**

Create and configure a `local.conf` file to define your OpenStack installation preferences:

```bash
nano local.conf
```

Add the following configuration:

```ini
[[local|localrc]]
ADMIN_PASSWORD=SuperSecret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

# Automatically detect the host IP
HOST_IP=$(hostname -I | awk '{print $1}')

# Enable only essential services
disable_service tempest
disable_service cinder
disable_service swift
disable_service heat

# Use the latest OpenStack release
USE_PYTHON3=True
GIT_BASE=https://opendev.org
```

Save and close the file (CTRL+X, then Y, then ENTER).

![local.conf](./Images/local.conf.png)

---

## **5. Install OpenStack Using DevStack**

Execute the DevStack installation script, which may take about **30-45 minutes**:

```bash
./stack.sh
```

![directory](./Images/directory.png)

Upon successful installation, you will see the message: `DevStack installed successfully!`.

![complete](./Images/Screenshot%202025-03-24%20090128.png)

---

## **6. Access the OpenStack Horizon Dashboard**

Once the installation completes, you can access the OpenStack **Horizon UI** by opening a web browser and navigating to:

```bash
http://your-ec2-public-ip/dashboard
```

### **Login Credentials:**

- **Username:** `admin`
- **Password:** `SuperSecret` (as defined in the `local.conf` file)

![dashboard](./Images/dashboard.png)

---

## **7. Verify OpenStack Services**

Before you start managing your cloud resources, verify that the OpenStack services are running:

```bash
source openrc admin admin
openstack service list
```

You should see a list of active OpenStack services if everything is working correctly.

![openrc](./Images/openrc.png)

---

## **8. Conclusion**

Congratulations! You have successfully deployed OpenStack on an AWS EC2 instance. You can now use the Horizon dashboard or OpenStack CLI to manage your cloud infrastructure, including virtual machines, networking, and storage.

For additional configurations or troubleshooting, refer to the [DevStack Documentation](https://github.com/openstack/devstack).

---
