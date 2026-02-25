# osTicket-Lab
Installing osTicket , creating a ticket, assigning a ticket, resolving, responding and closing out a ticket.

# 🎫 osTicket Installation & Operations Lab

## 📋 Objective

A comprehensive demonstration of installing, configuring, and operating osTicket—a professional open-source ticketing system—on Windows Server with IIS, PHP, and MySQL. This project showcases complete infrastructure deployment, database administration, application configuration, and real-world support ticket workflows from both customer and staff perspectives.

***

## 🛠️ Tools & Technologies Used
- Windows Server 2019+ (IIS - Internet Information Services)
- MySQL 8.0 Community Server with Command Line Client
- PHP 7.4+ with FastCGI Process Manager
- osTicket v1.18.3 (Open-Source Ticketing System)
- IIS URL Rewrite Module 2.1+
- MySQLi Extension for PHP
- cURL, JSON, and XML-DOM Extensions for PHP

## 🔧 Key Skills Demonstrated
- Windows Server administration and role-based deployment
- IIS web server configuration with FastCGI handler mapping
- Relational database design and MySQL user privilege management
- PHP runtime environment setup with dynamic module loading
- Multi-tier web application installation and troubleshooting
- Ticketing system operations and support workflows
- Customer communication and professional issue documentation
- Database-driven application deployment and configuration
- URL rewriting and web application routing
- Linux/Windows interoperability through PHP and MySQL

### 📊 Infrastructure & Capabilities Summary
| Category | Technical Specification | Engineering Purpose |
| :--- | :--- | :--- |
| **Web Server** | Windows IIS with CGI/FastCGI, URL Rewrite Module, Default Web Site (HTTP Port 80) | Serves PHP-based osTicket application; routes HTTP requests to FastCGI handler |
| **Application Runtime** | PHP 7.4+ with MySQLi, cURL, JSON, XML-DOM extensions via FastCGI | Executes osTicket backend business logic, database queries, and API calls |
| **Database Layer** | MySQL 8.0 with dedicated osticketuser account and osticket database | Persistent storage for tickets, users, departments, SLAs, and system configuration |
| **Handler Mapping** | FastCGI mapping: *.php → C:\php\php-cgi.exe | Bridges IIS HTTP processing to PHP interpreter for dynamic script execution |
| **Ticketing Capabilities** | Multi-department, multi-agent, SLA-based ticket management with status tracking | Tracks support requests from customer submission through staff resolution with audit trail |
| **Authentication** | osTicket admin user (techboul) with MySQL osticketuser credentials | Controls access to admin panel and protects database operations with principle of least privilege |

***

## 📊 Network Diagram

~~~text
┌──────────────────────────────────────────────────────────────────┐
│              OSTICKET LAB - INFRASTRUCTURE ARCHITECTURE           │
└──────────────────────────────────────────────────────────────────┘

                          ┌─────────────────────┐
                          │   Internet/Clients  │
                          │  (Support Users &   │
                          │  Admin Staff)       │
                          └──────────┬──────────┘
                                     │
                         HTTP/HTTPS (Port 80/443)
                                     │
                                     ▼
                    ┌─────────────────────────────────┐
                    │    Windows Server localhost     │
                    │                                 │
                    │  ┌───────────────────────────┐  │
                    │  │  IIS (Web Server)         │  │
                    │  │  ✓ CGI Role Installed     │  │
                    │  │  ✓ URL Rewrite Enabled   │  │
                    │  │  ✓ FastCGI Configured    │  │
                    │  │  ✓ Default Web Site      │  │
                    │  └───────────┬───────────────┘  │
                    │              │                  │
                    │  ┌───────────▼───────────────┐  │
                    │  │  PHP 7.4+ Runtime         │  │
                    │  │  ✓ FastCGI Handler       │  │
                    │  │  ✓ MySQLi Extension      │  │
                    │  │  ✓ cURL Extension       │  │
                    │  │  ✓ JSON Extension       │  │
                    │  │  ✓ XML-DOM Extension    │  │
                    │  └───────────┬───────────────┘  │
                    │              │                  │
                    │  ┌───────────▼───────────────┐  │
                    │  │  osTicket v1.18.3         │  │
                    │  │  ✓ Admin Panel (SCP)      │  │
                    │  │  ✓ Support Center (Web)   │  │
                    │  │  ✓ Ticket Queue           │  │
                    │  │  ✓ SLA Management         │  │
                    │  │  ✓ User Management        │  │
                    │  └───────────┬───────────────┘  │
                    │              │                  │
                    │  ┌───────────▼───────────────┐  │
                    │  │  MySQL 8.0 Database       │  │
                    │  │  ✓ osticket Database      │  │
                    │  │  ✓ osticketuser Account   │  │
                    │  │  ✓ Ticket Records         │  │
                    │  │  ✓ User & Agent Data      │  │
                    │  │  ✓ SLA & Department Cfg   │  │
                    │  └───────────────────────────┘  │
                    │                                 │
                    └─────────────────────────────────┘
~~~

***

## 🚀 Implementation Steps

## Phase 1: Web Server & IIS Configuration
Establish the foundational Windows Server infrastructure with IIS role installation, CGI support enablement, URL Rewrite module deployment, and FastCGI handler mapping configuration to prepare the server for PHP-based application execution.

### Step :one:: Install IIS Role with CGI Support
**Action:** Add the Internet Information Services (IIS) role via Server Manager; select Application Development features with CGI enabled. This allows IIS to process FastCGI requests and execute PHP scripts through the FastCGI protocol rather than ISAPI, providing better performance and isolation for application processes.
*  ![IIS role CGI selected](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%201/1%20IIs%20role%20cgi%20selected.png)

### Step :two:: Verify IIS Default Web Site
**Action:** Launch IIS Manager from Administrative Tools; navigate to Sites → Default Web Site and verify the site is started and running on port 80. Browse to http://localhost to confirm HTTP connectivity and that IIS is successfully serving the default welcome page, establishing baseline web server functionality.
*  ![IIS default page localhost](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%201/2%20IIs%20default%20page%20localhost.png)

### Step :three:: Install URL Rewrite Module
**Action:** Download and install the IIS URL Rewrite Module from Microsoft's download center. This module enables clean URL routing and rewrite rules in IIS, allowing osTicket to use user-friendly URLs instead of query parameters. Configure at the Default Web Site level to apply globally to all applications hosted on this server.
*  ![URL Rewrite installed](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%201/3-url%20rewrite%20installed.png)

### Step :four:: Configure PHP Handler Mapping
**Action:** Create a FastCGI handler mapping in IIS that routes all *.php file requests to the PHP CGI executable (php-cgi.exe). This maps the request extension to the appropriate runtime interpreter, allowing IIS to execute PHP code server-side. Configure at the server level with request path pattern *.php and FastCGI Module.
*  ![PHP handler mapping](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%201/4-php%20handler%20mapping.png)

***

## Phase 2: Database & Application Installation
Deploy MySQL 8.0 database server with dedicated osTicket user account and database, then execute the osTicket web-based installer to create the application configuration and admin account. Verify all PHP extensions load correctly and that database connectivity is established.

### Step :one:: Create osTicket Database and User
**Action:** Connect to MySQL 8.0 Command Line Client using root credentials. Execute SQL commands to: (1) CREATE DATABASE osticket with UTF-8 character set and collation for international support, (2) CREATE USER 'osticketuser'@'localhost' with a strong password, (3) GRANT ALL PRIVILEGES on osticket.* to osticketuser, (4) FLUSH PRIVILEGES to apply changes. This establishes the database layer with principle-of-least-privilege access control for the application.
*  ![Database created, user granted](Phase%202/1%20database%20created%20user%20granted.png)

### Step :two:: Run osTicket Installer Prerequisites Check
**Action:** Navigate to http://localhost/osTicket/setup/install.php in a web browser to launch the osTicket installer. The installer performs an automated prerequisite check that validates: PHP version (7.4+), required extensions (MySQLi, cURL), file permissions on include/ost-config.php, and other system requirements. Review the results to identify any missing components that must be resolved before proceeding.
*  ![osTicket installer requirements](Phase%202/2%20osticket%20installer%20requirements.png)

### Step :three:: Complete osTicket Installation & Admin Setup
**Action:** Fill in the osTicket installation form with: (1) Helpdesk name and URL, (2) Default email address for system notifications, (3) Admin user credentials (username: techboul, password), (4) MySQL connection details (host: localhost, database: osticket, user: osticketuser, password). Submit the installer and wait for database initialization. After completion, log into the Admin Panel (SCP) at http://localhost/osTicket/scp with admin credentials to verify successful installation.
*  ![osTicket admin dashboard](Phase%202/3%20osticket%20admin%20dashboard.png)

***

## Phase 3: Customer Ticket Submission (Public Interface)
Demonstrate the customer-facing Support Center workflow where end-users access the public ticketing interface, submit support requests with detailed issue information, and receive immediate confirmation of ticket creation. This shows the customer perspective of the ticketing system.

### Step :one:: Access Support Center Homepage
**Action:** Navigate to http://localhost/osTicket/ (the public support center) in a web browser. This page displays a welcome message explaining how the ticketing system works, provides ticket submission capability through "Open a New Ticket" button, and offers "Check Ticket Status" feature for customers to track their existing tickets. This is the entry point for all end-users seeking support.
*  ![Support center homepage](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%203/1%20%20support%20center%20homepage.png)

### Step :two:: Submit Ticket via Form
**Action:** Click "Open a New Ticket" and fill out the ticket submission form with realistic customer information: (1) Email Address: techboulnp@gmail.com, (2) Full Name: Mike Tyson, (3) Phone Number: 8675309, (4) Help Topic: "Report a Problem / Access Issue", (5) Issue Summary: "Cannot access email account on company laptop", (6) Issue Description: detailed multi-line explanation of the problem including symptoms and impact. This creates a complete support request with all necessary context for staff to resolve the issue.
*  ![Ticket creation form](Phase%203/2%20ticket%20creation%20form.png)

### Step :three:: Receive Submission Confirmation
**Action:** Submit the ticket form and verify successful creation with a green confirmation message ("Support ticket request created") addressed to the customer by name. The system confirms that the ticket has been created, a ticket number has been assigned, and a support representative will respond. This confirmation provides immediate feedback to the customer that their request was received successfully.
*  ![Ticket submitted confirmation](Phase%203/3%20ticket%20submitted%20confirmation.png)

***

## Phase 4: Staff Ticket Management (Admin Operations)
Demonstrate the staff-side administrative workflow where support team members access the Admin Panel (SCP), review incoming tickets in the open queue, assign tickets to appropriate staff members, compose professional responses, and resolve tickets. This shows the complete operational management of the ticketing system from staff perspective.

### Step :one:: View Ticket in Admin Queue
**Action:** Log into the Admin Panel (Staff Control Panel) at http://localhost/osTicket/scp using admin credentials (username: techboul, password). Navigate to Tickets → Open to view all unresolved tickets. The newly submitted customer ticket (Ticket #659759) appears in the queue with columns showing: Ticket Number, Last Updated timestamp, Subject line, From (customer name), Priority level, and Assigned To status. This queue is the primary interface for staff to manage incoming work.
*  ![Ticket in admin queue](Phase%204/1%20ticket%20in%20admin%20queue.png)

### Step :two:: Review Complete Ticket Details
**Action:** Click on ticket #659759 to open the full ticket detail view. This displays: (1) Complete ticket header with number and subject, (2) Status (Open), Priority (High), Department (Support), Create Date/Time, (3) Customer information: User (Mike Tyson), Email (techboulnp@gmail.com), (4) Ticket classification: Help Topic, Source (Web), (5) Full issue description provided by the customer, (6) SLA Plan and Due Date, (7) Current assignment status (Unassigned), (8) Ticket Thread showing the original customer message. This comprehensive view gives staff all context needed to resolve the issue.
*  ![Ticket details viewed](Phase%204/2%20ticket%20details%20viewed.png)

### Step :three:: Assign Ticket to Staff Member
**Action:** Click the "Assigned To" field in the ticket details view, which opens an assignment dialog. Select "John Doe" from the available staff members/agents list. Optionally add an assignment note explaining the rationale. Click "Assign" button to confirm. This transfers responsibility for the ticket to a specific team member and updates the ticket status to show who is handling it. The assignment is timestamped and logged for audit purposes.
*  ![Ticket assigned to staff](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%204/3%20Ticket%20assigned%20to%20staff.png)

### Step :four:: Confirm Assignment
**Action:** After clicking "Assign," the system closes the assignment dialog and displays a green success message at the top of the page: "✓ Ticket assigned to John Doe successfully". The ticket details refresh to show: (1) "Assigned To: John Doe" is now populated, (2) Assignment timestamp is recorded, (3) The ticket remains in Open status, ready for staff response. This confirmation ensures the assignment was processed and logged correctly.
*  ![Assignment confirmed](https://github.com/techboulnp-gif/osTicket-Lab/blob/e0fdb262719220fd6d4a47751887e46b8559c164/Phase%204/4%20Assignment%20confirmed.PNG)

### Step :five:: Compose Staff Response
**Action:** Scroll to the "Post Reply" section at the bottom of the ticket. In the reply text area, compose a professional response to the customer including: (1) Greeting to customer by name, (2) Acknowledgment of the reported issue, (3) Description of solution performed or troubleshooting steps taken, (4) Specific resolution details with any credentials or instructions, (5) Clear next steps for customer to verify the resolution, (6) Professional closing with staff name and department. Select "Resolved" in the Ticket Status dropdown to mark the ticket as complete upon sending. This demonstrates professional customer communication and issue resolution capability.
*  ![Staff response and resolution](Phase%204/5%20staff%20response%20and%20resolution.png)

### Step :six:: Post Response and Resolve Ticket
**Action:** Click the "Post Reply" button to submit the staff response. The system processes the action by: (1) Recording the response in the ticket thread with timestamp and staff member name, (2) Changing ticket status to "Resolved", (3) Sending the response to the customer email address on file, (4) Logging the action in the audit trail for compliance, (5) Removing the ticket from the Open queue. A green success message appears confirming the ticket resolution. The complete ticket lifecycle is now documented from initial customer submission through staff resolution.
*  ![Reply posted successfully](Phase%204/6%20reply%20posted%20successfully.png)

---

## 🚀 Outcomes & Results

- ✅ Successfully deployed a fully functional osTicket ticketing system on Windows Server with IIS and MySQL
- ✅ Configured PHP runtime environment with FastCGI handler mapping for dynamic application execution
- ✅ Created dedicated MySQL database and user account with appropriate privilege grants
- ✅ Demonstrated complete ticket lifecycle: customer submission → admin review → assignment → resolution
- ✅ Verified both public-facing Support Center and admin control panel functionality
- ✅ Implemented SLA management and ticket priority routing
- ✅ Established professional customer communication workflow with staff response tracking
- ✅ Documented end-to-end operational procedures for ticketing system management

***

## 🎓 Key Learnings & Skills Acquired

- **Web Server Administration:** Configured IIS roles, modules, handler mappings, and URL rewriting for PHP applications
- **Database Design & Security:** Created relational databases with dedicated user accounts and principle-of-least-privilege access control
- **Application Deployment:** Installed, configured, and troubleshot a multi-tier web application across Windows Server infrastructure
- **Ticketing System Operations:** Managed complete support ticket workflows from intake through resolution
- **PHP Environment Setup:** Enabled required extensions (MySQLi, cURL, JSON) and FastCGI process handling
- **IT Support Best Practices:** Demonstrated professional ticket assignment, customer communication, and issue documentation
- **Infrastructure Troubleshooting:** Identified and resolved configuration issues (permissions, extensions, handler mappings)
- **Cross-Platform Integration:** Integrated Windows Server (IIS), PHP runtime, and MySQL database into a cohesive application stack
- **Customer Service Excellence:** Composed professional responses and managed customer expectations throughout resolution process
- **Audit & Compliance:** Maintained complete audit trail of all ticket actions for regulatory compliance and quality assurance

***

## 🗺️ Project Roadmap

- Step 1: Complete osTicket installation and verify admin/customer interfaces ✅
- Step 2: Document ticket lifecycle with real customer scenario submission and resolution ✅
- Step 3: Expand with multi-department workflows, custom fields, knowledge base integration, and email automation 🟡

***

[⬅️ Back to Main Portfolio](https://github.com/techboulnp-gif)

**Created by:** Art Johnson | **Date:** 2026 | **Status:** 🟢 Complete
