# PrestaShop Deployment on AWS (Two-Tier Architecture)

This project documents the deployment of PrestaShop on Amazon Web Services (AWS) using a two-tier architecture with separate application and database servers.

## Project Overview

This project demonstrates the deployment of an open-source eCommerce platform (PrestaShop) on Amazon Web Services (AWS) using a production-style architecture with:
- Separate EC2 instances for:
Application Server &
Database Server
- Secure SSH access
- Publicly accessible URL
- MySQL database configuration
- Required PHP extensions installation
- Basic security hardening

The goal was to simulate a real-world cloud deployment environment.

---

## Architecture

Two EC2 instances were created:

1. Application Server
   - Ubuntu 22.04
   - Apache
   - PHP
   - PrestaShop
   - Public IP address

2. Database Server
   - Ubuntu 22.04
   - MySQL
   - Private IP only

Flow:

Internet → Application Server (Public IP) → Private Network → Database Server (MySQL)

The database server is not publicly accessible. It only allows connections from the application server.

---

## Technologies Used

- Amazon Web Services (AWS)
- Amazon EC2
- Ubuntu 22.04
- Apache2
- PHP
- MySQL
- PrestaShop

