layout: true
<header>
  <p class="left"></p>
  <p class="right"></p>
</header>

---

class: title, middle, center
# Trials and Tribulation
## Deploying a Legacy Application to the Cloud & On-Premises
### Ryan Condron

---

class: middle, center
# Note:

## Some of the design choices were not mine

---

class: middle, center
# Application Overview

## The application in the presentation was a service & amusement collections application

---

# Application Components
- A Management Desktop Application
- Amusement Collection Application (legacy)
- Amusement Collections (modern)
- Ruby on Rails Web Application
- Customer Portal
- SQL Server Database
- Active Directory Server (Not Azure AD)
- File Share Server

---

# Desktop Environment Setup (Desktop Application)

- Used by general office users
  - Setup of a relational types
  - Used by office users, who prefer a desktop application
- Users create shortcut to application
- Application Hosted in a Windows File Share
- Application built using Powerbuilder
- Connects to SQL Server Database on same network
- Saves Attached Files to a subdirectory of the Windows share

---

# Amusement Collections Application (Desktop Application)

- Application is a subset of main desktop
  - Done by copying a certain set of libnrary files to laptop
  - Sync files using xcopy
  - Sync Data using Data Pipes
- Local Microsoft SQL Server installed on Laptop
- No internet access when roaming
- Sync Data and pulled new versions over a VPN connection

---

# Ruby on Rails Main Web Application

- Used by workers in the field to do the following:
  - Create, Update, & Complete Work Orders
  - Log sales calls and opportunities
  - Complete ATM Loads
- Ran on Linux on Premesis
- Connects to the SQL Server Database
- On Prem: Chef Omnibus
- Cloud: Docker Containers

---

# Customer Portal (Angular/C# API)

- Used for customers to create and view work orders
- Hosted Requirement
- C# API To Retrieve Data
- Angular for Client Experience

---

# Mobile Amusement Collections Application

- Used C# Blazor (New Web Framework)
- Offline Progressive Web Application

---
class: middle, center
# On Premises Only

---

# Rails Deployment Phase 1

- Manually Install all Dependencies
- Takes long time to update ever customer
- Not scalable

---

# Rails Deployment Phase 2

- Build Chef Omnibus Package
- Took setting up an instance from several hours to under an hour
- Everything was included that was needed to run the application
- Easier to deploy updates to a customer, via system update
- Becomes just a linux package etc. yum, deb

```
yum update
```

---

# What is Chef Omnibus

- A Ruby DSL to build a compile all libraries, services, languages,
  and the application so the application can run with no system
  dependencies
- Uses runit for service management
- Everything contained in 1 directory
- Centralized configuration file
- Pros:
  - Saves time in setup (1 thing to install vs many)
  - Saves time in deployment (just run system update )
  - Makes deployment easier
  - Every environment is the same
- Cons:
  - Long Build Time
  - Need to learn about compiling of all the neccesary dependencies
  - Need to host your own linux package repository to keep things secure

---

# Deploying the Desktop Application

- Install SQL Server on Windows
- Setup File Share
- Copy Application to File Share
- Setup Shortcuts

---

# Setup Collector Sync

- Verify with customer VPN details
- Create a Batch File to copy files from file share to laptop
  and send data
- Ensure Laptop could connect and pull files and send data

---

# Allowing Web Traffic to Web Application

- Create DNS rule
- Crate port forwarding rules in network firewall
- Ensure firewall rules are open
- Ensure Customer has a static IP Address

---

# Wrapping Up On-Premises Deployments

- Very involved process
- Wide range of Hardware, often with varying issues
- Setup took hours or days

---

class: middle, center
# Let's Go To The Cloud

---

# Chosing a Provider

- Cost
- Best Options for the need
-

---

# Initial Setup

- Setup Terminal Server
- Setup SQL Server on separate server
- Setup Active Directory Server
- Setup Linux instace to host the Rails Application
- Find a VPN to allow syncing

---

# Deploying the Desktop Application

- Code was located in a central directory for all clients
- Code location was added to system path
- Each client had their own folder to store client specific files
- Jailed by Active Directory & Group Policy

---

# Collector Laptop Deployment

- Setup the same way as on premises
- Syncing was more complicated due to VPN issues

---

# The VPN Solution for Syncing

- VPN Appliances too expenses
- Chose SSTP VPN a setup through Windows Server
- Pros:
  - Cheap, no per user cost
  - Integrates into Active Directory
- Cons:
  - Complicated setup (Every user needs a certificate)
  - Doesn't work every where (Blocked on Public Wifi)
  - Obscure error messages

---

# Deploying the Rails Application (First Attempt)

- git checkout the application for each customer and run on a different port
- connect to a different database number on redis server
- Pros:
  - It worked (kinda)
- Cons:
  - Fragile
  - Not scalable
  - Updates are slow

---

# Deploying the Rails Application (Second Attempt)

- Docker!
- Docker Compose to make deploying quick
- Need to ensure all settings are environment variables
- Each Customer:
  - Application Container
  - Sidekiq Container
  - Redis Container
- Centralized Nginx Container

---

# Problems Arise

- Application Stores DateTimes in local time.
  - Ran container in priviledged mode
  - Set the time zone in the container
  - originally was using a database trick
- Using Windows Shares with Docker was complicated
  - Tried mounting shares in the container
  - Also tried mounting externally

---

# Add Customer Portal & New Amusement Collections Application

- Container the pieces so it can be deployed to the customer
- Simply deploy the containers.

---

# Takeaways

- Deploying can be hard
- Development decisions can impact how an application is deployed