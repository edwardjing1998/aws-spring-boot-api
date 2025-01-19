# AWS S3 File Management Service (AmazonService.java)

This microservice provides **file upload, deletion, and listing functionalities** using AWS S3. It enables seamless interaction with S3 storage for storing and managing files.

## 🚀 Features
- Upload files to an **S3 bucket** with a specified folder.
- Delete files from **S3** using a file URL.
- List all files stored in the **S3 bucket**.

---

## 🛠️ **Project Setup**

### 1️⃣ **Prerequisites**
Ensure you have the following:
- **Java 17+**
- **Spring Boot 3.x**
- **AWS Account** with S3 configured
- **AWS SDK for Java** (included in `pom.xml`)

### 2️⃣ **Configuration**
Set the required AWS credentials in `application.properties`:

```properties
# AWS S3 Configuration
amazon.s3.region=us-east-1
amazon.s3.bucket=your-bucket-name
amazon.aws.access-key-id=your-access-key
amazon.aws.access-key-secret=your-secret-key
