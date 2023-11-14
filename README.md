# WordPress Deployment with Nginx, LEMP Stack, and GitHub Actions

## Step 1 – Installing the Nginx Web Server

To display web pages to site visitors, we'll use Nginx, a high-performance web server. Follow these steps:

1. **Update Server's Package Index:**
    ```bash
    sudo apt update
    ```

2. **Install Nginx:**
    ```bash
    sudo apt install nginx
    ```

3. **Allow Nginx Through UFW Firewall (if enabled):**
    ```bash
    sudo ufw allow 'Nginx HTTP'
    ```

4. **Verify UFW Status:**
    ```bash
    sudo ufw status
    ```

    Confirm that HTTP traffic is allowed.

5. **Test Nginx Installation:**
    Access your server's domain name or public IP address in your web browser:
    ```bash
    http://server_domain_or_IP
    ```

    If you see Nginx's default landing page, the installation is successful.

## Step 2 – Installing MySQL

1. **Install MySQL Server:**
    ```bash
    sudo apt install mysql-server
    ```

2. **Run MySQL Security Script:**
    ```bash
    sudo mysql_secure_installation
    ```

   Follow the script to configure MySQL security settings.

3. **Test MySQL Login:**
    ```bash
    sudo mysql
    ```

    Ensure you can log in to the MySQL console.

   To exit, type:
    ```bash
    exit
    ```

## Step 3 – Installing PHP

Install PHP and necessary modules:

```bash
sudo apt install php-fpm php-mysql
```

## Step 4 – Configuring Nginx to Use PHP Processor

1. **Create Nginx Server Block:**
    ```bash
    sudo nano /etc/nginx/sites-available/your_domain
    ```

2. **Paste Configuration:**
    ```nginx
    server {
        listen 80;
        server_name your_domain www.your_domain;
        root /var/www/your_domain;

        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
         }

        location ~ /\.ht {
            deny all;
        }
    }
    ```

3. **Activate Configuration:**
    ```bash
    sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
    sudo unlink /etc/nginx/sites-enabled/default
    ```

4. **Test Configuration Syntax:**
    ```bash
    sudo nginx -t
    ```

    If no errors, reload Nginx:
    ```bash
    sudo systemctl reload nginx
    ```

5. **Create Test Index.html:**
    ```bash
    nano /var/www/your_domain/index.html
    ```

    Include:
    ```html
    <html>
      <head>
        <title>your_domain website</title>
      </head>
      <body>
        <h1>Hello World!</h1>
        <p>This is the landing page of <strong>your_domain</strong>.</p>
      </body>
    </html>
    ```

6. **Access Your Website:**
    Visit http://server_domain_or_IP. If successful, your Nginx server block is working.

## Step 5 – Download and Configure WordPress

1. **Navigate to Project's Root:**
    ```bash
    cd /var/www/your_domain
    ```

2. **Download and Extract WordPress:**
    ```bash
    sudo wget https://wordpress.org/latest.tar.gz
    sudo tar -zxvf latest.tar.gz
    ```

3. **Move Files and Create Configuration:**
    ```bash
    sudo mv wordpress/* .
    sudo rmdir wordpress
    sudo cp wp-config-sample.php wp-config.php
    ```

4. **Edit wp-config.php:**
    ```bash
    sudo nano wp-config.php
    ```

    Replace database details with your MySQL credentials.

## Step 6 – Implement SSL/TLS using Let's Encrypt

1. **Install Certbot:**
    ```bash
    sudo apt update
    sudo apt install certbot python3-certbot-nginx
    ```

2. **Obtain SSL Certificate:**
    ```bash
    sudo certbot --nginx
    ```

    Follow prompts to configure SSL for your domain.

## Step 7 – Optimize Nginx Configuration

1. **Edit Nginx Configuration:**
    ```bash
    sudo nano /etc/nginx/sites-available/your_domain
    ```

2. **Add Caching and Gzip Compression:**
    ```nginx
    location / {
        try_files $uri $uri/ =404;
        expires 30d;
        add_header Cache-Control "public, max-age=2592000";
    }

    gzip on;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_comp_level 9;
    gzip_min_length 10;
    ```

## Step 8 – GitHub Actions Workflow

1. **Create Workflow File:**
    Create or update `.github/workflows/main.yml` with  deployment steps.
   

3. **Update GitHub Repository Secrets:**
    Update secrets with `SSH_HOST`, `SSH_USER`, and `SSH_KEY`.

## Step 9 – README.md

1. **Create or Update README.md:**
    Provide detailed instructions for setting up the environment and deploying the website using the GitHub Actions workflow. 

## Step 10 – Test and Submit

Test the deployment process locally and on your server to ensure it works as expected. Provide a link to your GitHub repository in your assignment submission, including all necessary code, configuration files, and the deployed website URL.
