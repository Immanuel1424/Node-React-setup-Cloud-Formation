# React + Node.js App Deployment on AWS with NAT Gateway (CloudFormation)

This project provisions an AWS infrastructure using CloudFormation to deploy a React.js (Node.js) app on a private EC2 instance. A public EC2 instance acts as a reverse proxy (Nginx) to securely expose the app to the internet.

## Architecture

* **VPC** with Public and Private Subnets
* **NAT Gateway** for internet access to private EC2
* **EC2 Public Instance** (Nginx Reverse Proxy)
* **EC2 Private Instance** (Node.js/React App)

## Features

* React app created via CLI (`create-react-app`)
* `npm start` runs app in private EC2
* Nginx reverse proxy forwards public traffic to private app
* Parameterized CloudFormation for dynamic VPCs and ports

---

## How to Deploy

### Prerequisites

* AWS account and IAM permissions to launch CloudFormation stacks
* EC2 KeyPair created in the desired region (e.g., `my-keypair`)

### Deployment Steps

1. **Clone this repository**

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
```

2. **Deploy CloudFormation Stack**

```bash
aws cloudformation create-stack \
  --stack-name react-app-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=my-keypair \
  --capabilities CAPABILITY_NAMED_IAM
```

3. **Wait until the stack completes.**

4. **Get the Public IP** from stack outputs:

```bash
aws cloudformation describe-stacks \
  --stack-name react-app-stack \
  --query "Stacks[0].Outputs"
```

5. **Access your App:**

```
http://<Public-IP>
```

---

## Nginx Configuration Notes

The reverse proxy on the public EC2 is configured to forward requests to the private instance. The configuration is written automatically to `/etc/nginx/sites-available/react-proxy` and symlinked to `sites-enabled/`.

You can manually edit and test the config by SSH-ing into the public EC2:

```bash
ssh -i my-keypair.pem ubuntu@<Public-IP>
sudo nano /etc/nginx/sites-available/react-proxy
sudo nginx -t && sudo systemctl restart nginx
```

---

## Future Enhancements

* Use `npm run build` + serve static files with Nginx
* Setup TLS (HTTPS) with Let's Encrypt
* CI/CD integration

---


