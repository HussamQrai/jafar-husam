# Setup a Static Website Using Nginx 

This guide provides step-by-step instructions on setting up a Static Website Using Nginx on a local server

---

## Table of Contents
- [History of GNU/Linux (Linux)](#history-of-gnulinux-linux)
- [Linux File System](#linux-file-system)
- [Configuration](#configuration)
- [Testing](#testing)
- [SSL/TLS Configuration for Load Balanced Servers](#ssltls-configuration-for-load-balanced-servers)
  - [Obtain SSL Certificates](#obtain-ssl-certificates)
  - [Configure Nginx for SSL/TLS](#configure-nginx-for-ssltls)
  - [Test Nginx Configuration](#test-nginx-configuration)
  - [Update DNS](#update-dns)
  - [Test SSL/TLS](#test-ssltls)
- [Updating DNS Records for Nginx Load Balancer Servers](#updating-dns-records-for-nginx-load-balancer-servers)
  - [Login to Your Domain Registrar](#login-to-your-domain-registrar)
  - [Find DNS Settings or Domain Management](#find-dns-settings-or-domain-management)
  - [Locate DNS Records](#locate-dns-records)
  - [Update A Records or DNS Entries](#update-a-records-or-dns-entries)
  - [Save Changes](#save-changes)
  - [Verify the Update](#verify-the-update)
- [Additional Considerations](#additional-considerations)
- [License](#license)

---

# History of GNU/Linux(Linux)

## Introduction
To truly understand Linux, it's essential to appreciate the histories of UNIX and GNU. This README provides a brief overview of these systems and their evolution into what we commonly refer to as GNU/Linux.

## UNIX and its Limitations
In the 1960s, MIT and AT&T collaborated to develop the UNIX operating system. Despite its innovations, UNIX had limitations such as a terminal interface and high costs.

## The GNU Project
Richard Stallman initiated the GNU project to create a free and open-source operating system that surpassed UNIX's limitations. GNU introduced a user interface, supported various programs, and embraced the concept of sharing source code.

## The Missing Kernel
Despite its advancements, GNU lacked a kernel. In parallel, Linus Torvalds developed the Linux kernel in the early 1990s, providing the core interface between hardware and processes.

## The Birth of GNU/Linux
Richard Stallman and Linus Torvalds eventually combined forces, merging the GNU system with the Linux kernel. This union gave birth to GNU/Linux, commonly known as Linux.

## Open Source and Customization
GNU/Linux is renowned for its open-source nature, allowing anyone to modify and redistribute its codebase. Companies like Debian and Red Hat have developed their distributions (distros) based on GNU/Linux.

## Creating Your Distro
When creating a distribution (distro), key elements that can be customized include:
1. User interface
2. Shell
3. Package manager (installation, deletion, file system navigation)
4. Text editor
5. Programs
6. And more

## Categories of GNU/Linux Distributions
The GNU community categorizes distributions into:
- Beginner-friendly (e.g., Ubuntu, Linux Mint)
- Intermediate (e.g., Fedora, openSUSE)
- Professional (e.g., Arch Linux, Gentoo, Slackware)

## Choosing a Distro
Considerations for selecting a distro include:
1. Purpose of use
2. Personal preference
3. Hardware capabilities
4. And more

## Conclusion
Linux and GNU have profoundly shaped the landscape of operating systems, fostering a community-driven approach to software development and customization.

Feel free to explore and contribute to the vibrant world of GNU/Linux!


   
---


# Linux File System

### / (Root):
- The top-level directory of the filesystem hierarchy. All other directories and files are contained within this directory.

### /home:
- Contains user home directories. Each user has a subdirectory under /home named after their username. This is where user-specific files and settings are stored.

### /etc:
- Contains system-wide configuration files and shell scripts used to boot and initialize the system.

### /opt:
- Used for optional or add-on software packages. It can contain software and add-ons that are not part of the default installation.

### /boot:
- Contains files needed to boot the system, such as the kernel and bootloader configuration files.

### /bin:
- Contains essential command binaries that are needed for system operation and repair, usable by all users.

### /mnt:
- Temporary mount point for mounting filesystems. System administrators mount storage devices here temporarily.

### /tmp:
- Temporary files are stored here. This directory is often cleared upon reboot.

### /usr:
- Contains user-related programs and data. This includes libraries, documentation, and binaries for user applications.
  - **/usr/local**: Contains locally installed software and files that are not part of the standard distribution.
  - **/usr/sbin**: Non-essential system binaries for superuser (root).
  - **/usr/include**: Standard include files for development, primarily C/C++ header files.

### /root:
- The home directory for the root user (superuser).

### /lib:
- Contains essential shared libraries and kernel modules needed to boot the system and run commands in the root filesystem.

### /sbin:
- Contains essential system binaries for system administration, mostly used by the root user.

### /var:
- Variable data files. This includes logs, databases, websites, and temporary email files.
  - **/var/tmp**: Temporary files that need to be preserved between system reboots.
  - **/var/log**: Log files from various services and applications.
  - **/var/cache**: Application cache data.
  - **/var/spool**: Spool files for tasks waiting to be processed, such as print jobs and mail queues.

### /media:
- Mount point for removable media such as CDs, DVDs, and USB drives.


---

## Configuration
2. Configure your static website:
    - Place your static website content in a directory on each server (e.g., `/var/www/html`).

3. Configure Nginx for Load Balancing:
    - Edit the Nginx configuration file on each server (usually `/etc/nginx/nginx.conf`).
    - Add the following block within the `http` block:
      
        ```nginx
        http {
            upstream mywebsite {
                server <server1_ip>;
                server <server2_ip>;
                # Add more servers as needed
            }

            server {
                listen 80;
                server_name <your_domain.com>;

                location / {
                    proxy_pass http://mywebsite;
                }
            }
        }
        ```
        Replace `<server1_ip>` and `<server2_ip>` with the actual IP addresses of your servers.

4. Test Nginx Configuration:
    - Run the following command to check for syntax errors:
      
        ```bash
        sudo nginx -t
        ```
    - If successful, you should see `nginx: configuration file /etc/nginx/nginx.conf test is successful`.

5. Restart Nginx:
    - Apply the changes by restarting Nginx:
      
        ```bash
        sudo service nginx restart
        ```

---

## Testing
6. Update DNS:
    - Point your domain's DNS records to the IP addresses of your Nginx load balancer servers.

7. Test Load Balancing:
    - Visit your website using the domain name, and Nginx should distribute traffic evenly between your servers.

---

## SSL/TLS Configuration for Load Balanced Servers
If your static website uses HTTPS and you have multiple servers set up for load balancing, you need to configure SSL/TLS certificates on each server. The following steps guide you through obtaining and configuring SSL certificates for Nginx with load balancing.

### Obtain SSL Certificates

1. **Option 1: Let's Encrypt (Recommended)**
    - Install Certbot, the Let's Encrypt client:
      
        ```bash
        sudo apt install certbot
        ```
    - Obtain SSL certificates for your domain:
      
        ```bash
        sudo certbot certonly --nginx -d <your_domain.com>
        ```
    - Follow the prompts to complete the certificate generation process.

2. **Option 2: Use Existing Certificates**
    - If you have SSL certificates from a certificate authority, make sure you have the certificate and private key files.

### Configure Nginx for SSL/TLS

3. Open the Nginx configuration file for editing:
   
    ```bash
    sudo nano /etc/nginx/nginx.conf
    ```

5. Add the SSL server block within the existing server block, including all servers in the `upstream` block:
   
    ```nginx
    http {
        upstream mywebsite {
            server server1_ip;
            server server2_ip;
            # Add more servers as needed
        }

        server {
            listen 80;
            server_name <your_domain.com>;

            location / {
                proxy_pass http://mywebsite;
            }
        }

        server {
            listen 443 ssl;
            server_name <your_domain.com>;

            ssl_certificate /etc/letsencrypt/live/<your_domain.com>/fullchain.pem; # Update path accordingly
            ssl_certificate_key /etc/letsencrypt/live/<your_domain.com>/privkey.pem;  # Update path accordingly

            ssl_protocols TLSv1.2 TLSv1.3;
            ssl_prefer_server_ciphers off;
            ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384';

            # Other SSL/TLS settings (optional)
            ssl_session_timeout 1d;
            ssl_session_cache shared:MozSSL:10m;
            ssl_session_tickets off;

            location / {
                proxy_pass http://mywebsite;
            }
        }
    }
    ```
    - Update `<your_domain.com>`, `server1_ip`, and `server2_ip` with your actual domain and server IP addresses.
    - If you used Let's Encrypt, replace `/etc/letsencrypt/live/<your_domain.com>` with the correct path.

6. Save the configuration file.

### Test Nginx Configuration

6. Run the following command to check for syntax errors:
    ```bash
    sudo nginx -t
    ```

7. If successful, restart Nginx to apply the changes:
    ```bash
    sudo service nginx restart
    ```

### Update DNS

8. Point your domain's DNS records to the IP addresses of your Nginx load balancer servers.

### Test SSL/TLS

9. Visit your website using `https://<your_domain.com>`. Your browser should show a secure connection.

---

## Updating DNS Records for Nginx Load Balancer Servers

To ensure that your domain correctly points to the IP addresses of your Nginx load balancer servers, you need to update the DNS records at your domain registrar. Follow the steps below to achieve this.

### Login to Your Domain Registrar

1. Visit the website of the company where you registered your domain (e.g., GoDaddy, Namecheap).
2. Log in to your account.

### Find DNS Settings or Domain Management

3. Navigate to the domain management or DNS settings section of your account. Look for options like "DNS Management" or "Domain Settings."

### Locate DNS Records

4. Look for a section that displays your domain's DNS records. This might be labeled as "DNS Records" or "Name Servers."

### Update A Records or DNS Entries

5. Locate the A records or DNS entries associated with your domain. These records specify the IP addresses where your domain points.
6. Update the IP addresses to the ones of your Nginx load balancer servers. You will typically find fields for the hostname (usually '@' or your domain name) and IP address.

### Save Changes

7. After updating the A records, save your changes. This process may take some time to propagate throughout the internet.

### Verify the Update

8. To confirm the update, you can use online DNS lookup tools or wait for the changes to propagate and then visit your website using your domain name in a web browser.

### Example (Simplified):

- Before:
  - A Record: `@` points to `Old_IP_Address`
- After:
  - A Record: `@` points to `New_Load_Balancer_IP`

Remember that DNS changes might take some time to propagate, and during this period, some users might still be directed to the old IP address. It's generally a good practice to make DNS changes during periods of low traffic or with a low TTL (Time To Live) setting for faster propagation.

If you are unsure about any step, it's recommended to check the documentation of your specific domain registrar or contact their support for assistance.

---

## Additional Considerations

- **Monitoring:** Implement monitoring tools to keep an eye on server health and performance.
- **Automation:** Consider using configuration management tools like Ansible, Puppet, or Chef for automating the SSL/TLS setup process across multiple servers.

---

## License

This project is licensed under the MIT License. See the [LICENSE](https://github.com/iftekharmickey/Load-Balancing-for-Static-Website-using-Nginx/blob/main/LICENSE) file for details.
