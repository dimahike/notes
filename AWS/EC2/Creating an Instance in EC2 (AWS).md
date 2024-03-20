
# Creating an Instance in EC2 (AWS)

This guide provides step-by-step instructions on how to create a new virtual server, or "instance", in Amazon EC2 (Elastic Compute Cloud).

## Prerequisites

- An AWS account
- AWS Management Console access

## Step 1: Sign In to AWS Management Console

1. Go to the [AWS Management Console](https://aws.amazon.com/console/).
2. Sign in with your AWS account credentials.

## Step 2: Access EC2 Dashboard

1. In the AWS Management Console, find and select "EC2" under the "Services" menu to go to the EC2 Dashboard.
2. You'll see an overview of your EC2 resources.

## Step 3: Launch Instance (ubuntu)

1. Click on the "Launch Instance" button to start the process of creating a new instance.
2. Choose an Amazon Machine Image (AMI) that serves as a template for your instance, offering a variety of operating systems and configurations.
3. Select an Instance Type. Each type provides different computing resources and performance. Choose one that fits your application needs.
4. Configure your instance details, including the number of instances, network, subnet, and more.
5. Add storage if needed. You can select the size and type of storage to attach to your instance.
6. Configure a Security Group, which acts as a virtual firewall to control the traffic to your instance.
7. Review your choices, and then click on the "Launch" button.

## Step 4: Key Pair for Secure Access

1. You'll be prompted to select an existing key pair or create a new one. This key pair is used for secure SSH access to your instance.
2. If creating a new one, give it a name, download it, and keep it safe. You'll need this key to access your instance.
- Recommended: Move your key to ~/.ssh/ and set the permissions to 600.
```bash
  # MAC or Linux
  mv ~/Downloads/my-key-pair.pem ~/.ssh/
  cd ~/.ssh
  chmod 400 ~/.ssh/my-key-pair.pem
  ssh -i ~/.ssh/my-key-pair.pem ubuntu@your-instance-public-dns.compute-1.amazonaws.com
  ```

## Step 5: Access Your Instance

1. Once the instance state is "running", you can connect to your instance using its public DNS or IP address and the private key from the key pair you created or selected.

## Extra Details
1. You can add access to different Ports and Protocols in the Security Group settings.
- Security tab
- Security Groups
- Edit inbound rules
- Add Rule (e.g. HTTP, HTTPS, PORT, SSH, etc.) You can add localhost:3000 for a Node.js app.
- Save rules
2. You can find the public DNS and IP address of your instance in the EC2 Dashboard under "Instances". Your app will be accessible at the ***Public IPv4 address***.

## Conclusion

You have successfully launched a new EC2 instance in AWS. You can now access and configure your server based on your requirements.
