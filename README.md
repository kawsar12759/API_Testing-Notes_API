# Notes API Testing with Postman & Newman

This project demonstrates comprehensive API testing for the Notes API using Postman and Newman. It includes full test coverage of user authentication and note management endpoints, with automated testing, validation scripts, and dynamic data handling.

---

## 📚 Table of Contents

- [Features](#features)
- [API Documentation](#api-documentation)
- [Technology Used](#technology-used)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Run Commands](#run-commands)
- [Test Summary](#test-summary)
- [Example Test Cases](#example-test-cases)
- [Newman HTML Report](#newman-html-report)


---

## ✅ Features

- 🔐 **User Authentication**: Register, Login, Profile, Logout
- 📝 **Note Management**: Create, Read, Update, Delete notes
- 🧪 **Automated Testing**: Covers positive & negative scenarios
- 📊 **Assertion Validations**: Status codes, messages, data fields
- 🔁 **Dynamic Variables**: Randomized input for isolated tests
- 📁 **HTML Report Generation** with `newman-reporter-htmlextra`

---

## 📄 API Documentation

📎 [https://practice.expandtesting.com/notes/api/api-docs/](https://practice.expandtesting.com/notes/api/api-docs/)

---

## 🛠️ Technology Used

- Postman
- Newman
- newman-reporter-htmlextra
- JavaScript (for test scripts)

---

## ⚙️ Prerequisites

- [Node.js](https://nodejs.org/)
- [Postman](https://www.postman.com/downloads/)

Install globally:
```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```
---

## 📥 Installation
```bash
git clone https://github.com/your-username/notes-api-testing.git
cd notes-api-testing
```

1. Open Postman and import:
- Notes_API.postman_collection.json
- Notes_API.postman_environment.json

---

## ▶️ Usage

1. Select the Notes_API environment in Postman.
2. Run tests using the Collection Runner or Newman CLI.
3. View test results in CLI or the generated HTML report.

---

## 🧪 Run Commands

**Run via CLI**
```bash
newman run Notes_API.postman_collection.json -e Notes_API.postman_environment.json
```

**Run with HTML report**
```bash
newman run Notes_API.postman_collection.json -e Notes_API.postman_environment.json -r cli,htmlextra
```

---

## 📊 Test Summary
| Metric               | Value |
| -------------------- | ----- |
| Requests             | 16    |
| Assertions           | 87    |
| Failed Assertions    | 1     |
| Skipped Tests        | 0     |
| Duration             | 8.7s  |
| Avg. Resp. Time      | 465ms |

---

## 🧾 Example Test Cases

### 1️⃣ Register User

**🔗 Request URL**
```bash
POST https://practice.expandtesting.com/users/register
```

**📦 Request Body**
```bash 
{
        "name": "{{userName}}",
        "email": "{{userEmail}}",
        "password": "{{password}}"
}
```

**🧪 Pre-Request Script**
```bash
var userName = pm.variables.replaceIn("{{$randomFullName}}")
pm.environment.set("userName", userName)

var userEmail = pm.variables.replaceIn("{{$randomEmail}}").toLowerCase()
pm.environment.set("userEmail", userEmail)

var password = pm.variables.replaceIn("{{$randomPassword}}")
pm.environment.set("password", password)
```

**🧪 Test Script**
```bash 
var statusCode = pm.response.code

if(statusCode===201){
    var jsonData = pm.response.json()
    pm.environment.set("id", jsonData.data.id)
    pm.test("Status Code:201 Validaion", function(){
        pm.expect(jsonData.status).to.eql(201)
    })
    pm.test("Message: Registration Validaion", function(){
        pm.expect(jsonData.message).to.eql("User account created successfully")
    })
    pm.test("Name Field Validation", function(){
        pm.expect(jsonData.data.name).to.eql(pm.environment.get("userName"))
    })
     pm.test("Email Field Validation", function(){
        pm.expect(jsonData.data.email).to.eql(pm.environment.get("userEmail"))
    })
}

else if(statusCode===400){
    var jsonData = pm.response.json()
    pm.test("Status Code:400 Validaion", function(){
        pm.expect(jsonData.status).to.eql(400)
    })
    if(jsonData.message==="Password must be between 6 and 30 characters"){
        pm.test("Message: Password Length Validation")
    }
    else if(jsonData.message==="A valid email address is required"){
        pm.test("Message: Email Format Validation")
    }
    else if(jsonData.message==="User name must be between 4 and 30 characters"){
        pm.test("Message: Username Length Validation")
    }

    else if(jsonData.message==="Invalid input data"){
        pm.test("Message: Invalid Input Data Validation")
    }

}

if(statusCode===409){
    var jsonData = pm.response.json()
    pm.test("Status Code:409 Validaion", function(){
        pm.expect(jsonData.status).to.eql(409)
    })
    if(jsonData.message==="An account already exists with the same email address"){
        pm.test("Message: Already Registered Validation")
    }
}
```
**✅ Sample Response**
```bash
{
    "success": true,
    "status": 201,
    "message": "User account created successfully",
    "data": {
        "id": "681c4e856a3ce60286a7865b",
        "name": "Mr. Ian Ruecker",
        "email": "dillon.larson@yahoo.com"
    }
}
```
---

### 2️⃣ Login User

**🔗 Request URL**
```bash
POST https://practice.expandtesting.com/users/login
```

**📦 Request Body**
```bash 
{
        "email": "{{userEmail}}",
        "password": "{{password}}"
}
```
**🧪 Test Script**
```bash 
var statusCode = pm.response.code

if(statusCode===200){
    var jsonData = pm.response.json()
    pm.environment.set("token",jsonData.data.token)
    pm.environment.set("id", jsonData.data.id)
    pm.test("Status Code:200 Validaion", function(){
        pm.expect(jsonData.status).to.eql(200)
    })
    pm.test("Message: Login Validaion", function(){
        pm.expect(jsonData.message).to.eql("Login successful")
    })
    pm.test("Name Field Validation", function(){
        pm.expect(jsonData.data.name).to.eql(pm.environment.get("userName"))
    })
     pm.test("Email Field Validation", function(){
        pm.expect(jsonData.data.email).to.eql(pm.environment.get("userEmail"))
    })
    if(jsonData.data.token!==null)
    {
        pm.test("Token Not Null Validation")
    }
}

else if(statusCode===400){
    var jsonData = pm.response.json()
    pm.test("Status Code:400 Validaion", function(){
        pm.expect(jsonData.status).to.eql(400)
    })
    pm.test("Message: Bad Request Validation", function(){
        pm.expect(jsonData.message).to.eql("Bad Request")
    })

}

else if(statusCode===401){
    var jsonData = pm.response.json()
    pm.test("Status Code:401 Validaion", function(){
        pm.expect(jsonData.status).to.eql(401)
    })
    pm.test("Message: Unauthorized Access Validation", function(){
        pm.expect(jsonData.message).to.eql("Incorrect email address or password")
    })

}

else if(statusCode===500){
    var jsonData = pm.response.json()
    pm.test("Status Code:500 Validaion", function(){
        pm.expect(jsonData.status).to.eql(500)
    })
    pm.test("Message: Internal Server Error Validation", function(){
        pm.expect(jsonData.message).to.eql("Internal Error Server")
    })
}

```
**✅ Sample Response**
```bash
{
    "success": true,
    "status": 200,
    "message": "Login successful",
    "data": {
        "id": "681c4e856a3ce60286a7865b",
        "name": "Mr. Ian Ruecker",
        "email": "dillon.larson@yahoo.com",
        "token": "05f3be8c338b4955ad6bc8559590b9cefbc6aed4f39f4bd0862677681cb2125d"
    }
}
```

---

## 📊 Newman HTML Report

🖥️ The HTML report includes:

- Request/response logs
- Header and body info
- Visual pass/fail summaries
- Assertion details

📂 Open **Notes_API-2025-05-07-07-22-42-991-0.html** in your browser.

### 📊 Newman HTML Report Sample
![Summary](https://drive.google.com/uc?id=1EmjqWENRpWxtDE4-CUBqw-CpHWHyCIHt)
![Total Request](https://drive.google.com/uc?id=1msdaqfQQcAYqJ_TPGTVbqh6Yj-BDGzHc)
![Failed Tests](https://drive.google.com/uc?id=1iu0qzgv8YsaOqa2Off1a65uL_gH-Cl5_)
![Skipped Tests](https://drive.google.com/uc?id=1H7f14J6YVPG7hqTFFzRsahpTS-SnuRio)