---
title: Deploy to AWS
sidebar_position: 1
slug: /deploying/aws
---

# Deploy to AWS

<a href="https://console.fleetbase.io/aws-marketplace" target="_aws_marketplace">
    <img src="https://flb-assets.s3.ap-southeast-1.amazonaws.com/images/fleetbase-aws.png" alt="Fleetbase on Aws" style={{width: "300px", marginBottom: ".5rem"}} />
</a>

Deploy your complete Fleetbase logistics platform on AWS with enterprise-grade security, scalability, and reliability. Our one-click deployment solution provisions a production-ready infrastructure in under 30 minutes with no DevOps expertise required.

## Why Choose AWS One-Click Deployment?

### 🚀 Lightning Fast Setup
Get your complete logistics platform running in **25 minutes or less**. Our automated CloudFormation template handles all the complexity of provisioning and configuring your AWS infrastructure.

### 🏢 Enterprise-Grade Infrastructure
Deploy with confidence using AWS best practices including:
- **High Availability**: Multi-AZ deployment across multiple availability zones
- **Auto-Scaling**: ECS Fargate services that scale automatically based on demand
- **Security**: VPC isolation, encrypted storage, and secrets management
- **Monitoring**: Comprehensive CloudWatch logging and monitoring
- **Backup**: Automated database backups with point-in-time recovery

### 💰 Cost-Optimized
- **Pay-as-you-use**: Only pay for the AWS resources you actually consume
- **Optimized Resource Allocation**: Right-sized instances and services
- **Spot Instances**: Optional spot instances for non-production environments
- **Estimated AWS costs**: $45-350/month depending on traffic and configuration

### 🔒 Production-Ready Security
- **VPC Isolation**: Private subnets with NAT gateways
- **Encrypted Storage**: All data encrypted at rest and in transit
- **Secrets Management**: AWS Secrets Manager for sensitive configuration
- **SSL/TLS**: Automatic SSL certificate provisioning and renewal
- **IAM Roles**: Least-privilege access controls

## What's Included

Your AWS deployment includes a complete, production-ready infrastructure stack:

### Compute & Containers
- **ECS Fargate Cluster**: Serverless container platform
- **Auto-scaling Services**: Automatic scaling based on CPU/memory usage
- **Application Load Balancer**: Distributes traffic across multiple containers
- **Container Health Checks**: Automatic container restart on failure

### Database & Cache
- **RDS MySQL 8.0**: Managed database with automated backups
- **ElastiCache Redis**: High-performance in-memory caching
- **Multi-AZ Support**: Database replication across availability zones
- **Automated Backups**: Daily backups with configurable retention

### Storage & CDN
- **S3 Object Storage**: Scalable file storage for uploads and assets
- **CloudFront CDN**: Global content delivery network
- **SSL Certificates**: Automatic SSL certificate management
- **Global Distribution**: Fast content delivery worldwide

### Networking & Security
- **VPC with Private Subnets**: Isolated network environment
- **NAT Gateways**: Secure outbound internet access
- **Security Groups**: Firewall rules for network access control
- **Secrets Manager**: Secure storage for sensitive configuration

### Monitoring & Logs
- **CloudWatch Logs**: Centralized application and system logging
- **Container Insights**: Detailed container performance metrics
- **Health Monitoring**: Automatic health checks and alerting
- **Performance Metrics**: Real-time performance monitoring

### Queue & Events
- **SQS Message Queues**: Reliable message queuing for background jobs
- **Event Processing**: Asynchronous task processing
- **Background Jobs**: Automated order processing and notifications
- **Real-time Updates**: Live tracking and status updates

## Pricing Options

### AGPL v3 Community Edition
**$2,500 one-time deployment fee**

Perfect for startups, developers, and open-source projects.

**✨ What's Included:**
- Complete Fleetbase platform with all features
- Production-ready AWS infrastructure
- Full source code access for modifications
- Community support via GitHub and Discord
- Auto-scaling and high availability
- Regular updates with new features

**📋 AGPL v3 Requirements:**
- Must share source code modifications
- Must clearly state any significant changes to software
- Network use triggers copyleft obligations
- Ideal for internal use or open-source projects

### FCL Commercial License
**$25,000 per year (includes deployment service)**

For businesses requiring commercial flexibility and enterprise support.

**🚀 Everything in AGPL v3 Plus:**
- **Commercial Use Rights**: No copyleft obligations or source sharing requirements
- **Private Modifications**: Keep your customizations and integrations private
- **White-Label Solutions**: Rebrand and resell as your own product
- **Priority Support**: Direct access to Fleetbase engineering team
- **99.9% Uptime SLA**: Commercial support with guaranteed uptime
- **Custom Development**: Paid custom feature development available
- **Fleetbase Navigator App**: Included driver mobile application

**💼 Commercial Benefits:**
- No open-source license obligations
- Proprietary integrations and customizations
- Commercial redistribution rights
- Enterprise-grade support and SLA

:::note AWS Infrastructure Costs
AWS infrastructure costs are separate and billed directly by AWS based on your usage. Estimated costs range from $45-350/month depending on traffic and configuration.
:::

## Deployment Process

### Step 1: Configure Parameters
Choose your environment type, instance sizes, and custom domain settings. Our smart defaults work great out of the box.

### Step 2: Launch CloudFormation
Click deploy and watch as AWS CloudFormation automatically provisions your entire infrastructure stack in the background.

### Step 3: Start Managing Logistics
Access your Fleetbase console and start managing orders, tracking vehicles, and optimizing your logistics operations immediately.

## Technical Specifications

### Application Stack
- **Fleetbase**: v0.7.8 with extension architecture
- **Real-time Engine**: SocketCluster v17.4.0
- **Container Runtime**: FrankenPHP + Octane for high performance
- **Queue System**: Laravel Queues + SQS for reliable job processing

### AWS Services
- **Compute**: ECS Fargate for serverless containers
- **Database**: RDS MySQL 8.0 with Multi-AZ
- **Cache**: ElastiCache Redis for performance
- **Storage**: S3 + CloudFront for global distribution
- **Load Balancer**: Application Load Balancer with SSL
- **Monitoring**: CloudWatch + X-Ray for observability

## Getting Started

Ready to deploy Fleetbase on AWS? Choose your licensing option and get started:

[**🚀 Deploy with AGPL v3 License**](https://aws.amazon.com/marketplace/pp/prodview-6ehco3zrjqsj6)
[**🏢 Deploy with Commercial License**](https://aws.amazon.com/marketplace/pp/prodview-6ehco3zrjqsj6)
[**💬 Talk to Sales**](https://cal.com/shivthakker/enquiry)

## Frequently Asked Questions

### How long does deployment take?
The CloudFormation stack typically takes 15-20 minutes to deploy completely. Most of this time is spent provisioning the database and setting up networking components.

### Can I use my own domain name?
Yes! You can specify your own domain name and the template will automatically create SSL certificates and configure DNS records through Route53.

### What about data backups?
Automated backups are enabled by default with configurable retention periods. Your RDS database is automatically backed up daily, and you can restore to any point in time.

### How do I scale the infrastructure?
The infrastructure auto-scales based on demand. You can also manually adjust instance types and counts through the CloudFormation parameters or ECS console.

### Is this suitable for production use?
Absolutely! The template follows AWS best practices for production deployments including Multi-AZ databases, auto-scaling, monitoring, and security configurations.

### What support is available?
- **AGPL v3**: Community support via GitHub issues and Discord
- **FCL Commercial**: Priority support with direct access to engineering team and 99.9% uptime SLA

### Can I customize the deployment?
Yes! Both licensing options include full source code access. The FCL Commercial license allows you to keep modifications private, while AGPL v3 requires sharing modifications.

---

**Ready to get started?** [Deploy to AWS now](https://console.fleetbase.io/aws-marketplace) or [contact our sales team](https://cal.com/shivthakker/enquiry) for enterprise inquiries.

