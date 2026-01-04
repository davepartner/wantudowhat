# Wan2.2-Animate: Image-to-Video Animation with Character Consistency

This repository provides a user-friendly web interface for **Wan2.2-Animate**, a powerful AI model that transforms a still image and a driving video into a high-quality animation with remarkable character consistency. This project is a fork of the original [initchu/Wan2.2-Animate](https://github.com/initchu/Wan2.2-Animate) repository and is designed to be easily deployed on a local machine or a Virtual Private Server (VPS).

## Introduction

**Wan2.2-Animate** is a unified character animation and replacement model developed by Tongyi Lab at Alibaba. It leverages the **Wan2.2-Animate-14B** model to generate animations that accurately replicate the movements and expressions from a source video onto a reference image. This technology opens up exciting possibilities for content creators, animators, and developers who want to bring static images to life.

This repository simplifies the process of using this advanced AI model by providing a Gradio-based web interface. You can easily install and run the application on your own machine or deploy it to a VPS for public access.

## Features

*   **High-Quality Animation:** Generate smooth and realistic animations from a single image and a driving video.
*   **Character Consistency:** Maintain the identity and appearance of the character from the reference image throughout the animation.
*   **User-Friendly Interface:** A simple and intuitive web interface built with Gradio for easy interaction.
*   **Local and VPS Deployment:** Run the application on your local machine for personal use or deploy it on a VPS to share with others.
*   **Open Source:** The code is open source and available under the Apache 2.0 license.

## Prerequisites

Before you begin, you will need the following:

*   **Python 3.8 or higher:** You can download Python from the official [website](https://www.python.org/downloads/).
*   **Git:** You will need Git to clone the repository. You can download it from the [Git website](https://git-scm.com/downloads).
*   **DashScope API Key:** This application uses the Alibaba Cloud DashScope API. You will need to obtain an API key from the [DashScope website](https://dashscope.console.aliyun.com/).

## Local Installation and Usage

Follow these steps to run the application on your local machine:

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/davepartner/wantudowhat.git
    cd wantudowhat
    ```

2.  **Create and activate a virtual environment:**

    *   **On macOS and Linux:**

        ```bash
        python3 -m venv env
        source env/bin/activate
        ```

    *   **On Windows:**

        ```bash
        python -m venv env
        .\env\Scripts\activate
        ```

3.  **Install the required dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

4.  **Set the DashScope API Key:**

    *   **On macOS and Linux:**

        ```bash
        export DASHSCOPE_API_KEY="YOUR_API_KEY"
        ```

    *   **On Windows (Command Prompt):**

        ```bash
        set DASHSCOPE_API_KEY="YOUR_API_KEY"
        ```

    *   **On Windows (PowerShell):**

        ```powershell
        $env:DASHSCOPE_API_KEY="YOUR_API_KEY"
        ```

    Replace `"YOUR_API_KEY"` with your actual DashScope API key.

5.  **Run the application:**

    ```bash
    python app.py
    ```

6.  **Access the web interface:**

    Open your web browser and navigate to `http://127.0.0.1:7860`.

## VPS Deployment Guide

This guide will walk you through deploying the Wan2.2-Animate application on a VPS running Ubuntu 22.04. This setup uses Nginx as a reverse proxy and Systemd to manage the application as a service.

### Prerequisites

*   A VPS running Ubuntu 22.04.
*   A domain name pointed to your VPS's IP address.
*   SSH access to your VPS as a non-root user with sudo privileges.
*   Nginx installed on your VPS.

### 1. Server Setup

1.  **Update your server:**

    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2.  **Install Python and other dependencies:**

    ```bash
    sudo apt install python3-pip python3-venv -y
    ```

### 2. Application Setup

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/davepartner/wantudowhat.git /opt/wantudowhat
    ```

2.  **Create and activate a virtual environment:**

    ```bash
    sudo python3 -m venv /opt/wantudowhat/env
    source /opt/wantudowhat/env/bin/activate
    ```

3.  **Install the required dependencies:**

    ```bash
    pip install -r /opt/wantudowhat/requirements.txt
    ```

### 3. Systemd Service

Create a systemd service to run the application in the background and automatically restart it on failure.

1.  **Create a systemd service file:**

    ```bash
    sudo nano /etc/systemd/system/wantudowhat.service
    ```

2.  **Add the following content to the file:**

    Replace `your_user` with your actual username.

    ```ini
    [Unit]
    Description=Wan2.2-Animate Gradio Web Application
    After=network.target

    [Service]
    User=your_user
    Group=www-data
    WorkingDirectory=/opt/wantudowhat
    Environment="PATH=/opt/wantudowhat/env/bin"
    Environment="DASHSCOPE_API_KEY=YOUR_API_KEY"
    ExecStart=/opt/wantudowhat/env/bin/python app.py
    Restart=always

    [Install]
    WantedBy=multi-user.target
    ```

3.  **Reload the systemd daemon, enable and start the service:**

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wantudowhat
    sudo systemctl start wantudowhat
    ```

4.  **Check the status of the service:**

    ```bash
    sudo systemctl status wantudowhat
    ```

### 4. Nginx Reverse Proxy

Configure Nginx to act as a reverse proxy, forwarding requests to the Gradio application.

1.  **Create an Nginx configuration file:**

    ```bash
    sudo nano /etc/nginx/sites-available/wantudowhat
    ```

2.  **Add the following content to the file:**

    Replace `your_domain.com` with your actual domain name.

    ```nginx
    server {
        listen 80;
        server_name your_domain.com www.your_domain.com;

        location / {
            proxy_pass http://127.0.0.1:7860/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_buffering off;
            proxy_redirect off;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }
    ```

3.  **Enable the site and restart Nginx:**

    ```bash
    sudo ln -s /etc/nginx/sites-available/wantudowhat /etc/nginx/sites-enabled/
    sudo nginx -t
    sudo systemctl restart nginx
    ```

### 5. SSL Certificate (Recommended)

It is highly recommended to secure your application with an SSL certificate. You can use Let's Encrypt to get a free SSL certificate.

1.  **Install Certbot:**

    ```bash
    sudo apt install certbot python3-certbot-nginx -y
    ```

2.  **Obtain and install the SSL certificate:**

    ```bash
    sudo certbot --nginx -d your_domain.com -d www.your_domain.com
    ```

    Follow the on-screen instructions to complete the process.

## Project Structure

```
.
├── README.assets
├── examples
├── .gitattributes
├── README.md
├── app.py
└── requirements.txt
```

*   `README.assets/`: Contains images and other assets for the README file.
*   `examples/`: Contains example images and videos.
*   `.gitattributes`: A Git configuration file.
*   `README.md`: This file.
*   `app.py`: The main application file containing the Gradio web interface and the logic for interacting with the DashScope API.
*   `requirements.txt`: A list of the Python dependencies required to run the application.

## Contributing

Contributions are welcome! If you have any suggestions, bug reports, or feature requests, please open an issue or submit a pull request.

## License

This project is licensed under the [Apache 2.0 License](LICENSE).

## Acknowledgments

This project is a fork of the [initchu/Wan2.2-Animate](https://github.com/initchu/Wan2.2-Animate) repository and is made possible by the work of the developers of the **Wan2.2-Animate** model at Tongyi Lab, Alibaba.

## References

1.  [Gradio Documentation](https://www.gradio.app/docs/)
2.  [Running Gradio On Your Web Server With Nginx](https://www.gradio.app/guides/running-gradio-on-your-web-server-with-nginx)
3.  [How to Deploy Gradio on Ubuntu 22.04 - Complete Guide | Vultr Docs](https://docs.vultr.com/how-to-deploy-gradio-on-ubuntu-22.04)
4.  [Wan2.2 Animate - a Hugging Face Space by Wan-AI](https://huggingface.co/spaces/Wan-AI/Wan2.2-Animate)
