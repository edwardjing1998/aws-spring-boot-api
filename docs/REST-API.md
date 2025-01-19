1) This microservice provides a **RESTful API** for managing files in **AWS S3**. Users can:
- 📤 **Upload** files to S3
- 🗑 **Delete** files from S3
- 📄 **List** files stored in S3

This service is built with **Spring Boot** and integrates with **AWS S3**.


2) Upload a File Example Request (cURL) and Response:

Request:
curl -X POST "http://localhost:8080/storage/uploadFile" \
-H "Content-Type: multipart/form-data" \
-F "file=@sample.jpg" \
-F "folder=uploads"

Response:
"https://your-bucket-name.s3.amazonaws.com/uploads/sample.jpg"

3) Delete a File

Request:
curl -X DELETE "http://localhost:8080/storage/deleteFile" \
-H "Content-Type: application/json" \
-d '{"url": "https://your-bucket-name.s3.amazonaws.com/uploads/sample.jpg"}'

Response:
"Successfully deleted"

4) List Files

Request:
   curl -X GET "http://localhost:8080/storage/getFileList"

Response:
    [
    "uploads/sample.jpg",
    "uploads/document.pdf"
    ]


