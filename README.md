# Project 1: Java Application Deployment with Reverse Proxy on AWS

## Project Overview

This project involves deploying a Java-based **Student Registration Web Application** on AWS using a secure reverse proxy architecture. The application is hosted on an EC2 instance using Apache Tomcat, while the student data is stored in an Amazon RDS MySQL database. A separate EC2 instance running Nginx acts as a reverse proxy, forwarding user requests to the backend server.

This setup improves security by ensuring the backend server is **not directly accessible** from the internet.

---

## Architecture

```
User Browser → Nginx (Proxy EC2) → Apache Tomcat (Backend EC2) → AWS RDS MySQL
```

---

## Traffic Flow (Step by Step)

1. User accesses the application using the **public IP** of the proxy EC2 instance.
2. Request reaches the **Nginx** reverse proxy server.
3. Nginx forwards the request to the **Tomcat backend** running on port `8080`.
4. Tomcat processes the request and queries the **RDS MySQL** database.
5. Database returns data to Tomcat.
6. Tomcat sends the response back to Nginx.
7. Nginx delivers the final response to the user's browser.

---

## Tools & Services Used

| Tool / Service     | Purpose                   |
|--------------------|---------------------------|
| AWS EC2            | Application & Proxy Server |
| Apache Tomcat      | Java Web Server (port 8080) |
| Nginx              | Reverse Proxy (port 80)    |
| AWS RDS MySQL      | Managed Database           |
| Ubuntu Linux       | Operating System           |
| MySQL Client       | Database Management        |

---

## Application Features

- ✅ Student Registration
- ✅ Store Student Data in MySQL Database
- ✅ View Students List
- ✅ Edit Student Details
- ✅ Delete Student Records

---

## Configuration Details

### Backend Server
- Apache Tomcat running on **port 8080**
- WAR file deployed at `/opt/tomcat/webapps/`

### Reverse Proxy (Nginx)
```nginx
server {
    listen 80;

    location /student {
        proxy_pass http://<backend-private-ip>:8080/student;
    }
}
```

### Security Configuration
- Backend EC2: **port 8080 is NOT open to the internet**
- Only the proxy server's security group can reach the backend on port 8080
- Proxy server exposes **port 80** to the internet

---

## Challenges & Solutions

### Issue 1: Tomcat Not Accessible
**Problem:** Tomcat was running but port 8080 was unreachable.  
**Solution:** Updated EC2 Security Group to allow port 8080 from the proxy server's security group.

### Issue 2: Column Name Mismatch
**Problem:** Application failed to insert records.  
**Error:** `Unknown column 'student_addr' in 'field list'`  
**Solution:** Updated the database schema to match the column names expected by the Java application.

### Issue 3: Reverse Proxy Not Forwarding
**Problem:** Nginx configuration was incorrect.  
**Solution:** Updated `proxy_pass` with the correct backend private IP and port, then restarted Nginx.

---

## Result

- Java web application successfully deployed on AWS
- Users access the app via the public proxy IP
- Student data stored securely in RDS MySQL
- Backend not publicly exposed

---

## Conclusion

This project implements a secure AWS architecture for a Java-based student registration application. A reverse proxy manages and secures incoming traffic while preventing direct access to the application server. The use of Apache Tomcat, Nginx, and Amazon RDS demonstrates a production-grade, scalable deployment pattern on AWS.
