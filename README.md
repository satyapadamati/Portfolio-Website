# Deploying a Web Application on AWS EC2 with Nginx, Custom Domain, and SSL (Route 53)

## **Overview**
This project involves deploying a web application on an AWS EC2 instance using Nginx as the web server, configuring a custom domain using AWS Route 53, and securing the application with SSL certificates provided by Let's Encrypt.

---

## **Project Features**
1. Deployment of a web application on an AWS EC2 instance.
2. Use of Nginx as a web server to serve the application.
3. Configuration of a custom domain (e.g., `luxuryhotelmenu.com` and `www.luxuryhotelmenu.com`).
4. SSL certificate setup using Certbot for HTTPS.
5. DNS management using AWS Route 53.

---

## **Technologies Used**
- **AWS EC2:** Hosting the application.
- **Nginx:** Web server to serve static files and reverse proxy if required.
- **AWS Route 53:** Domain registration and DNS management.
- **Let's Encrypt:** Free SSL certificate provider.

---

## **Steps to Reproduce**

### **1. Launch an EC2 Instance**
1. Log in to your AWS Management Console.
2. Navigate to the **EC2 Dashboard**.
3. Launch a new EC2 instance with the following settings:
   - AMI: Ubuntu 20.04 or later.
   - Instance Type: t2.micro (Free Tier eligible).
   - Security Group:
     - Allow **port 80** (HTTP).
     - Allow **port 443** (HTTPS).
   - Add an Elastic IP for a static public IP address.

4. SSH into your EC2 instance:
   ```bash
   ssh -i your-key.pem ubuntu@your-ec2-public-ip
   ```

### **2. Install Nginx**
1. Update the system packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Install Nginx:
   ```bash
   sudo apt install nginx -y
   ```
3. Start and enable the Nginx service:
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```
4. Test by visiting your EC2 public IP address in a browser (`http://your-ec2-public-ip`).

### **3. Deploy Your Application**
1. Upload your application files to the `/var/www/html` directory:
   ```bash
   sudo rm -rf /var/www/html/*
   sudo cp -r /path/to/your/application/* /var/www/html/
   ```
2. Ensure the correct permissions:
   ```bash
   sudo chown -R www-data:www-data /var/www/html
   sudo chmod -R 755 /var/www/html
   ```
3. Test by visiting your EC2 public IP in a browser to see your application.

### **4. Configure Custom Domain with AWS Route 53**
1. Purchase or use an existing domain in AWS Route 53.
2. Create a **Hosted Zone** for your domain:
   - Go to Route 53, select **Hosted Zones**, and click **Create Hosted Zone**.
   - Add records:
     - **A Record:**
       - Name: `@` (or leave blank).
       - Type: `A`.
       - Value: Public IP of your EC2 instance.
       - TTL: 300 seconds.
     - **CNAME Record:**
       - Name: `www`.
       - Value: `luxuryhotelmenu.com`.
3. Update your domain’s nameservers to Route 53’s nameservers (found in your Hosted Zone).

### **5. Configure Nginx for Custom Domain**
1. Open the Nginx configuration file:
   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```
2. Update the configuration for your domain:
   ```nginx
   server {
       listen 80;
       server_name luxuryhotelmenu.com www.luxuryhotelmenu.com;

       root /var/www/html;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```
3. Test the configuration and restart Nginx:
   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```
4. Test your custom domain by visiting `http://luxuryhotelmenu.com` or `http://www.luxuryhotelmenu.com`.

### **6. Set Up SSL with Certbot**
1. Install Certbot and the Nginx plugin:
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   ```
2. Obtain an SSL certificate:
   ```bash
   sudo certbot --nginx -d luxuryhotelmenu.com -d www.luxuryhotelmenu.com
   ```
3. Follow the prompts to:
   - Agree to the terms of service.
   - Redirect HTTP to HTTPS (choose Yes).
4. Verify HTTPS by visiting:
   - `https://luxuryhotelmenu.com`
   - `https://www.luxuryhotelmenu.com`

### **7. Test Automatic Certificate Renewal**
Run a dry run to ensure automatic renewal is working:
```bash
sudo certbot renew --dry-run
```

---

## **Project Structure**
```
/var/www/html
|-- index.html  # Main entry point
|-- css/
|-- js/
|-- images/
```

---

## **Commands Summary**
### Nginx Commands
- Restart Nginx:
  ```bash
  sudo systemctl restart nginx
  ```
- Test Nginx Configuration:
  ```bash
  sudo nginx -t
  ```

### Certbot Commands
- Install Certbot:
  ```bash
  sudo apt install certbot python3-certbot-nginx -y
  ```
- Obtain SSL Certificate:
  ```bash
  sudo certbot --nginx -d luxuryhotelmenu.com -d www.luxuryhotelmenu.com
  ```
- Test SSL Renewal:
  ```bash
  sudo certbot renew --dry-run
  ```

---

## **Final Notes**
- This deployment provides a secure and scalable web application setup.
- Ensure your application files are updated as needed in `/var/www/html`.
- Monitor your SSL certificates to avoid expiration.
- Use AWS CloudWatch for monitoring server health if required.

Feel free to clone this project, adapt it to your needs, and contribute!

