
# React App Deployment on AWS with CloudFormation

This project uses **AWS CloudFormation** to provision a secure, scalable infrastructure that deploys a React application served from a private EC2 instance via a public Nginx reverse proxy.

---

## 📦 Components Created

- **VPC** with public and private subnets
- **NAT Gateway** for outbound internet access from private subnet
- **Internet Gateway** for public subnet
- **Public EC2 Instance**:
  - Runs **Nginx**
  - Acts as a **reverse proxy**
- **Private EC2 Instance**:
  - Runs **React app** using `serve`
- **Security Groups** to control access
- **UserData scripts** to automatically install and configure services

---

## 🔧 Parameters

| Parameter     | Description                    | Default     |
|--------------|--------------------------------|-------------|
| `KeyName`     | Name of your EC2 Key Pair for SSH access | *Required* |
| `InstanceType`| EC2 instance type              | `t3.micro`  |
| `AppPort`     | Port your React app will run on| `3000`      |

---

## 🚀 How to Deploy

### 1. Prerequisites

- An existing EC2 **Key Pair** in your AWS region
- AWS CLI configured (`aws configure`)
- Permissions to create VPC, EC2, EIP, NAT Gateway, etc.

### 2. Deploy Stack

```bash
aws cloudformation create-stack \
  --stack-name ReactAppStack \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=your-key-name \
  --capabilities CAPABILITY_NAMED_IAM
```

> Replace `your-key-name` with your EC2 Key Pair name.

### 3. Wait for Deployment to Complete

Monitor the stack via:
```bash
aws cloudformation describe-stacks --stack-name ReactAppStack
```

---

## 🌐 Access the React App

Once the stack is created, access your app via:

```bash
http://<PublicIP>
```

You can find the public IP in the **Outputs** tab of the CloudFormation stack, or use:

```bash
aws cloudformation describe-stacks \
  --stack-name ReactAppStack \
  --query "Stacks[0].Outputs[?OutputKey=='PublicIP'].OutputValue" \
  --output text
```

---

## 🛠 What Happens Internally

- The private EC2:
  - Creates a React app via `create-react-app`
  - Builds and serves it using `serve` on port `3000`
- The public EC2:
  - Configures Nginx to reverse proxy traffic to the private EC2’s private IP and port
  - Restarts Nginx automatically

---

## 🧼 Cleanup

To delete all resources:

```bash
aws cloudformation delete-stack --stack-name ReactAppStack
```

---

## 📁 File Structure

```
.
├── template.yaml   # CloudFormation template
└── README.md       # This file
```

---

## 🔐 Notes

- Make sure the NAT Gateway and EIP are not left running to avoid unexpected charges.
- React app is deployed with a basic example – customize as needed.
