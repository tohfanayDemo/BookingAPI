# 🧪 Booking API Test Automation Framework

This repository contains a robust and CI-ready **API Test Automation Framework** for the **Booking API**, built using **Postman**, **Newman**, and **Jenkins**. It supports environment-based testing, data-driven requests, reporting, pass-rate evaluation, and automated email notifications. <br>



### 📁 Project Structure
BookingAPI/  
├── Postman Collections/ # All chained and modular API requests  
├── Booking.postman_environment.json # Postman environment configuration  
├── bookingAPI_testData.json # Extensive data for positive & negative test cases  
├── Jenkinsfile # Jenkins CI/CD pipeline  
├── package.json # Newman, reporting, and email dependencies  <br><br>


### ⚙️ Features

- ✅ **Chained API Testing** via Postman Collections
- ✅ **Environment-specific execution** (QA, DEV, STAGE)
- ✅ **Secure credentials** using Jenkins credentials binding
- ✅ **Data-driven testing** with external JSON
- ✅ **Reports**: Newman CLI, JSON, JUnit, HTML Extra, Allure
- ✅ **Pass Rate Validation** using configurable threshold (default: 80%)
- ✅ **Email Notifications** with HTML reports attached
- ✅ **Jenkins CI Integration** with archiving and publishing <br><br>



### 📦 Tech Stack

| Tool                    | Purpose                             |
|-------------------------|-------------------------------------|
| Postman                 | Create & structure chained API tests |
| Newman                  | Run Postman tests from CLI          |
| newman-reporter-htmlextra | Generate rich HTML reports        |
| Allure                  | Advanced test reporting             |
| Jenkins                 | CI/CD pipeline execution            |
| nodemailer              | Send result summary via email       |



### 🚀 How to Run Tests Locally

### 1. 📦 Install Dependencies
```bash
npm install
```
### 2. ▶️ Run the Tests
```bash
newman run "Postman Collections/Booking.postman_collection.json" \
    -e Booking.postman_environment.json \
    -d bookingAPI_testData.json \
    -r cli,json,junit,html,htmlextra
```
### 3. 📊 View Reports
Reports will be saved in the ./reports/ folder. Open htmlextra_report.html for a detailed view. <br><br>

### 🛠️ Jenkins Pipeline
This repo includes a Jenkinsfile that automates the entire test execution and reporting process.

| Stage                     | Purpose                                        |
| ------------------------- | ---------------------------------------------- |
| `Checkout`                | Clones this repository                         |
| `Install Dependencies`    | Installs `newman`, `nodemailer`, etc.          |
| `Set Credentials`         | Dynamically loads Jenkins credentials          |
| `Run Newman Tests`        | Executes Postman collections with reports      |
| `Evaluate Pass Threshold` | Fails build if pass rate < 80% (configurable)  |
| `Post Actions`            | Archives reports, publishes HTML, sends emails | 

### 🎯 Triggeringwith Parameters
When triggering the pipeline, choose the environment:  
- QA  
- DEV  
- STAGE <br><br>

### 🧠 Credentials Handling
Based on selected TEST_ENV, Jenkins securely injects environment-specific credentials:

| TEST\_ENV | Jenkins Credential ID |
| --------- | --------------------- |
| QA        | `qa-creds`            |
| DEV       | `dev-creds`           |
| STAGE     | `stage-creds`         | 
<br>

### 👤 Author  
Tohfatul Nayeem  
📧 tohfa.nay@gmail.com  
💻 GitHub: [https://github.com/tohfanayDemo/BookingAPI]

