---
title: "Install Pritunl Zero in Proxmox Using Docker"
date: 2024-06-11T10:34:30Z
draft: false
---

## A. What is Pritunl
Pritunl is an open-source software-defined networking (SDN) solution that provides secure remote access to networks and resources. It offers a Zero Trust network architecture, allowing organizations to create secure and scalable networks for remote access and site-to-site connectivity.

Here are some key features and functionalities of Pritunl:

1. **Zero Trust Network**: Pritunl follows the Zero Trust security model, which means it assumes that every access attempt, whether internal or external, is a potential threat. It implements strict access controls and authentication mechanisms to verify users and devices before granting access to resources.

2. **VPN Alternative**: Pritunl serves as an alternative to traditional VPN solutions, providing secure remote access to networks and resources over the internet. Users can connect to the Pritunl network from anywhere, using a variety of devices, and access resources securely.

3. **Web-based Management Interface**: Pritunl comes with a user-friendly web-based management interface that allows administrators to easily configure and manage the network. Administrators can set up user accounts, define access policies, monitor connections, and more, all through the web interface.

4. **Centralized Authentication**: Pritunl supports various authentication methods, including username/password, multi-factor authentication (MFA), and integration with external identity providers such as LDAP, Active Directory, and SAML. This allows organizations to centralize user authentication and enforce strong security measures.

5. **Scalability and High Availability**: Pritunl is designed to be scalable and highly available, making it suitable for organizations of all sizes. It supports clustering and replication for high availability deployments, ensuring uninterrupted access to resources even in the event of failures.

6. **Integration with Third-party Tools**: Pritunl integrates with other networking and security tools, allowing organizations to extend its functionality and integrate it into existing infrastructure seamlessly. It supports APIs for automation and integration with third-party applications and services.

Overall, Pritunl provides a robust and flexible solution for secure remote access and network connectivity, making it suitable for businesses, enterprises, and organizations looking to implement Zero Trust networking principles and enhance their cybersecurity posture.

Here's a step-by-step guide to setting up Pritunl Zero using Docker Compose, including the `.env` file and necessary instructions:
## B. References

## C. Installation procedure
### Step 1: Create the `.env` File

Create a `.env` file to store environment variables for Pritunl Zero:

```sh
touch .env
```

Add the following content to the `.env` file:

```
PRITUNL_MONGODB_URI=pritunl-mongo-uri
PRITUNL_SECRET=your_secret_key
PRITUNL_SERVER_URL=https://yourdomain.com
```

Replace `pritunl-mongo-uri` with the MongoDB connection URI for Pritunl Zero. You can use a MongoDB instance running locally or hosted elsewhere. Replace `your_secret_key` with a secure secret key. Replace `https://yourdomain.com` with the URL where Pritunl Zero will be accessible.

### Step 2: Create the `docker-compose.yml` File

Create a `docker-compose.yml` file to define the Docker services:

```
version: '3.8'

services:
  pritunl:
    image: pritunl/pritunl-zero
    container_name: pritunl-zero
    restart: always
    ports:
      - "9580:9580"
      - "9543:9543"
    environment:
      - MONGODB_URI=${PRITUNL_MONGODB_URI}
      - SECRET=${PRITUNL_SECRET}
      - SERVER_URL=${PRITUNL_SERVER_URL}
    volumes:
      - ./pritunl-data:/data
    network_mode: host

```

### Step 3: Start Pritunl Zero

Run the following command to start Pritunl Zero using Docker Compose:

```sh
docker-compose up -d
```

### Step 4: Access Pritunl Zero Web Interface

Once the containers are up and running, you can access the Pritunl Zero web interface using the URL specified in the `.env` file (`PRITUNL_SERVER_URL`). Open a web browser and navigate to this URL to access the Pritunl Zero web interface.

### Step 5: Configure Pritunl Zero

Follow the on-screen instructions to set up your Pritunl Zero instance. This may include creating an administrator account, configuring authentication methods, and setting up your Zero Trust network.

### Step 6: Connect Clients

Once Pritunl Zero is configured, users can download and install the Pritunl Client software on their devices. They can then use the client to connect to the Zero Trust network you've configured in Pritunl Zero.

That's it! You've now set up Pritunl Zero using Docker Compose, configured it, and can connect clients to the Zero Trust network for secure remote access.
