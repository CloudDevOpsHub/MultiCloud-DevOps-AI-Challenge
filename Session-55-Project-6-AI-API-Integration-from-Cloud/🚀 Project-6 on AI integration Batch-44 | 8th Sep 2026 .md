# Project 6: AI Integration with Python — Building an AI-Powered ATS Resume Scanner & Job Matcher on AWS EC2

[![Module: AI & Python Automation](https://img.shields.io/badge/Module-AI_%26_Python_Automation-8A2BE2?style=for-the-badge&logo=python&logoColor=white)](README.md)
[![Cloud: AWS EC2](https://img.shields.io/badge/Cloud-AWS_EC2_Ubuntu-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![AI: Google Gemini](https://img.shields.io/badge/AI_Model-Google_Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white)](README.md)
[![Frontend: Streamlit](https://img.shields.io/badge/Frontend-Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## Table of Contents

1. [Project Overview & Core Objective](#1-project-overview--core-objective)
2. [Technology Stack & System Requirements](#2-technology-stack--system-requirements)
3. [Project Directory & File Structure](#3-project-directory--file-structure)
4. [Step 1: AWS EC2 Ubuntu Instance Provisioning & Security Group Setup](#step-1-aws-ec2-ubuntu-instance-provisioning--security-group-setup)
5. [Step 2: System Packages, Python 3 & Poppler Installation](#step-2-system-packages-python-3--poppler-installation)
6. [Step 3: Repository Setup & Code Initialization](#step-3-repository-setup--code-initialization)
7. [Step 4: Python Virtual Environment (`venv`) Setup & Activation](#step-4-python-virtual-environment-venv-setup--activation)
8. [Step 5: Python Dependencies Installation (`requirements.txt`)](#step-5-python-dependencies-installation-requirementstxt)
9. [Step 6: Google Gemini API Key Generation (Google AI Studio)](#step-6-google-gemini-api-key-generation-google-ai-studio)
10. [Step 7: Secure Configuration with TOML (`.streamlit/secrets.toml`)](#step-7-secure-configuration-with-toml-streamlitsecretstoml)
11. [Step 8: Complete Application Code Implementation (`app.py`)](#step-8-complete-application-code-implementation-apppy)
12. [Step 9: Launching & Running the Streamlit Application](#step-9-launching--running-the-streamlit-application)
13. [Step 10: Live Application Testing & Verification Workflow](#step-10-live-application-testing--verification-workflow)
14. [Step 11: Real-World Troubleshooting & Error Resolution Matrix](#step-11-real-world-troubleshooting--error-resolution-matrix)
15. [Step 12: Infrastructure Cleanup & Cost Optimization](#step-12-infrastructure-cleanup--cost-optimization)
16. [Step 13: Professional Resume Points & Interview Highlights](#step-13-professional-resume-points--interview-highlights)

---

## 1. Project Overview & Core Objective

In modern hiring workflows, candidate resumes are filtered by automated **Application Tracking Systems (ATS)** before reaching human recruiters. Resumes that lack specific job-description (JD) keywords or fail alignment checks are discarded automatically.

This project implements an **enterprise AI Integration project** using **Python, Google Gemini AI, and Streamlit**, deployed on **AWS EC2 (Ubuntu 24.04 LTS)**. 

### Core Functionality:
* **PDF Resume Ingestion:** Accepts uploaded candidate resumes in PDF format and converts them into image representations for OCR and visual analysis.
* **Multimodal AI Analysis:** Integrates Google Gemini LLM using system prompts simulating an experienced Technical HR Manager and Senior SRE/DevOps Architect.
* **Automated ATS Scoring & Keyword Gap Analysis:**
  * Comprehensive candidate profile evaluation (Strengths, Weaknesses, Skill alignment).
  * Exact percentage match calculation against the targeted Job Description.
  * Identification of missing technical keywords (e.g., Kubernetes, Helm, Terraform, CI/CD).
* **Resume Tailoring Guidance:** Generates actionable instructions to optimize candidate resumes to exceed an 85–95% ATS match threshold.

---

## 2. Technology Stack & System Requirements

| Component | Technology / Tool | Purpose |
| :--- | :--- | :--- |
| **Cloud Host** | AWS EC2 (Ubuntu 24.04 LTS) | Compute host for web application execution |
| **Networking** | AWS Security Group | Open port `22` (SSH) and port `8501` (Streamlit HTTP) |
| **Programming Language** | Python 3.10+ | Core application logic and automation scripts |
| **Frontend Framework** | Streamlit | Rapid interactive web UI for document upload & output display |
| **AI / LLM Engine** | Google Gemini (`gemini-3.6-flash` / `gemini-1.5-flash`) | Large language model for semantic text evaluation |
| **Configuration Standard** | TOML (`secrets.toml`) | Secure credential storage for API keys |
| **PDF Processing** | `pdf2image` + `poppler-utils` | Linux system utility to convert PDF pages into image data |
| **Image Processing** | Pillow (`PIL`) | Handling image encoding and byte array conversion for Gemini |
| **Data Analytics** | Pandas, NumPy | Mathematical computation of match percentages and token parsing |

---

## 3. Project Directory & File Structure

```text
application-tracking-system/
├── app.py
├── requirements.txt
├── .streamlit/
│   └── secrets.toml
├── .gitignore
├── venv/
└── README.md
```

---

## Step 1: AWS EC2 Ubuntu Instance Provisioning & Security Group Setup

### 1.1 Launch the EC2 Instance
1. Log in to the **AWS Management Console** and navigate to **EC2** (`ap-south-1` Mumbai or your preferred region).
2. Click **Launch Instances** and configure:
   * **Name:** `ATS-Application`
   * **AMI:** `Ubuntu Server 24.04 LTS` (64-bit x86)
   * **Instance Type:** `t2.medium` or `t3.medium` (minimum 2 vCPU, 4 GiB RAM recommended for smooth image rendering; `t2.micro` can be used for basic testing).
   * **Key Pair:** Select an existing key pair or choose *Proceed without a key pair* if using EC2 Instance Connect.
   * **Storage:** `30 GiB` gp3 root volume.

### 1.2 Hardening Security Group (Port 8501)
1. Under **Network Settings**, click **Edit**.
2. Keep SSH rule: Port `22` (Source: My IP or Anywhere `0.0.0.0/0`).
3. Add a new **Inbound Security Rule**:
   * **Type:** `Custom TCP`
   * **Port Range:** `8501`
   * **Source:** `0.0.0.0/0` (Anywhere IPv4)
   * **Description:** `Streamlit Web Application UI`
4. Click **Launch Instance**.

### 1.3 Connect to the Instance
Connect using **EC2 Instance Connect** or your SSH client:
```bash
# Elevate to root privileges
sudo -i
```

---

## Step 2: System Packages, Python 3 & Poppler Installation

Streamlit and PDF image conversion require Python 3, pip, git, and the underlying Linux `poppler-utils` rendering library.

### 2.1 Update System Repositories and Install Python
```bash
apt update -y && apt upgrade -y
apt install -y python3 python3-pip git
```

### 2.2 Verify Python and Pip
```bash
python3 --version
pip3 --version
```

### 2.3 Install Poppler Utilities
`poppler-utils` is essential for `pdf2image` to convert uploaded PDF resumes into image byte data for LLM analysis:
```bash
apt install -y poppler-utils
```

---

## Step 3: Repository Setup & Code Initialization

Create a project directory or clone the application repository.

```bash
cd /root
mkdir -p application-tracking-system
cd application-tracking-system
```

---

## Step 4: Python Virtual Environment (`venv`) Setup & Activation

Always run Python applications inside an isolated virtual environment to prevent dependency conflicts with system packages.

### 4.1 Create the Virtual Environment
```bash
python3 -m venv venv
```

### 4.2 Activate the Virtual Environment
```bash
source venv/bin/activate
```

> The terminal prompt will now display `(venv) root@ip-...:~/application-tracking-system#`.

### 4.3 Upgrade Pip inside Virtual Environment
```bash
pip install --upgrade pip
```

---

## Step 5: Python Dependencies Installation (`requirements.txt`)

Create the `requirements.txt` file specifying all required application libraries.

### 5.1 Create `requirements.txt`
```bash
cat << 'EOF' > requirements.txt
streamlit
google-generativeai
python-dotenv
pdf2image
Pillow
pandas
numpy
EOF
```

### 5.2 Install Python Packages
```bash
pip install -r requirements.txt
```

---

## Step 6: Google Gemini API Key Generation (Google AI Studio)

1. Open a browser and navigate to **Google AI Studio**:  
   `https://aistudio.google.com/`
2. Sign in using your Google / Gmail account.
3. In the left navigation menu, click **Get API key** / **Create API Key**.
4. Select your Google Cloud Project (or choose the default project generated by AI Studio).
5. Click **Create API Key in new project**.
6. Copy the generated API key string and save it securely in a temporary text file.

---

## Step 7: Secure Configuration with TOML (`.streamlit/secrets.toml`)

Streamlit provides built-in secret management using **TOML (Tom's Obvious Minimal Language)** files located in `.streamlit/secrets.toml`. This prevents hardcoding sensitive API keys in application code.

### 7.1 Create the `.streamlit` Directory
```bash
mkdir -p .streamlit
```

### 7.2 Create `secrets.toml`
```bash
cat << 'EOF' > .streamlit/secrets.toml
GOOGLE_API_KEY = "PASTE_YOUR_GEMINI_API_KEY_HERE"
EOF
```

Replace `PASTE_YOUR_GEMINI_API_KEY_HERE` with your actual Google AI Studio API key.

### 7.3 Secure File Permissions
```bash
chmod 600 .streamlit/secrets.toml
```

---

## Step 8: Complete Application Code Implementation (`app.py`)

Create the main Python file `app.py` that orchestrates the Streamlit frontend, PDF parsing, and Google Gemini API communication.

```bash
cat << 'EOF' > app.py
import io
import os
import base64
import streamlit as st
import google.generativeai as genai
from PIL import Image
import pdf2image

# ---------------------------------------------------------
# Page Configuration
# ---------------------------------------------------------
st.set_page_config(
    page_title="AI ATS Resume Expert",
    page_icon="🤖",
    layout="wide"
)

# ---------------------------------------------------------
# Gemini API Key Configuration via Streamlit Secrets
# ---------------------------------------------------------
try:
    api_key = st.secrets["GOOGLE_API_KEY"]
    genai.configure(api_key=api_key)
except Exception as e:
    st.error("Missing Google Gemini API Key in .streamlit/secrets.toml. Please configure it.")
    st.stop()

# ---------------------------------------------------------
# AI Helper Function
# ---------------------------------------------------------
def get_gemini_response(input_prompt, pdf_content, prompt):
    """
    Sends the input prompt, image representation of the PDF,
    and system context to Google Gemini and returns the generated text.
    """
    # Use gemini-1.5-flash or gemini-3.6-flash depending on account access
    try:
        model = genai.GenerativeModel('gemini-1.5-flash')
        response = model.generate_content([input_prompt, pdf_content[0], prompt])
        return response.text
    except Exception:
        # Fallback to alternative model version
        model = genai.GenerativeModel('gemini-2.5-flash')
        response = model.generate_content([input_prompt, pdf_content[0], prompt])
        return response.text

# ---------------------------------------------------------
# PDF Processing Function
# ---------------------------------------------------------
def input_pdf_setup(uploaded_file):
    """
    Converts the uploaded PDF's first page into JPEG byte data for Gemini Vision.
    """
    if uploaded_file is not None:
        images = pdf2image.convert_from_bytes(uploaded_file.read())
        first_page = images[0]

        # Convert to byte array
        img_byte_arr = io.BytesIO()
        first_page.save(img_byte_arr, format='JPEG')
        img_byte_arr = img_byte_arr.getvalue()

        pdf_parts = [
            {
                "mime_type": "image/jpeg",
                "data": base64.b64encode(img_byte_arr).decode()
            }
        ]
        return pdf_parts
    else:
        raise FileNotFoundError("No resume PDF file uploaded.")

# ---------------------------------------------------------
# Streamlit Web UI
# ---------------------------------------------------------
st.title("🤖 AI-Powered ATS Resume Expert & Job Matcher")
st.subheader("Optimize your Cloud & DevOps resume using Google Gemini AI")

job_description = st.text_area(
    "📋 Paste Target Job Description (JD):", 
    height=220, 
    placeholder="Paste the complete job description including required technical skills, qualifications, and responsibilities..."
)

uploaded_file = st.file_uploader(
    "📄 Upload Your Resume (PDF format only):", 
    type=["pdf"]
)

if uploaded_file is not None:
    st.success("Resume PDF uploaded successfully!")

col1, col2, col3 = st.columns(3)

with col1:
    btn_eval = st.button("🔍 Comprehensive Profile Review")

with col2:
    btn_match = st.button("📊 Calculate ATS Match %")

with col3:
    btn_keywords = st.button("🔑 Identify Missing Keywords")

# ---------------------------------------------------------
# Specialized System Prompts
# ---------------------------------------------------------
prompt_eval = """
You are an experienced Technical HR Manager and Principal Cloud/DevOps Architect. 
Review the provided candidate resume against the given job description. 
Provide a detailed, professional evaluation covering:
1. Candidate Strengths aligned with the role
2. Clear Technical Weaknesses or Experience Gaps
3. Overall Assessment on whether the profile fits the job requirements
"""

prompt_match = """
You are an advanced Application Tracking System (ATS) scanner with deep understanding of Cloud & DevOps roles.
Evaluate the candidate resume against the job description.
Return the output in the following format:
1. Percentage Match: [Exact % between 0% and 100%]
2. Summary of Match Rationale
3. Top Recommendations to increase match to 90%+
"""

prompt_keywords = """
You are a specialized DevSecOps Talent Recruiter and ATS Keyword Analyzer.
Compare the uploaded resume against the job description.
Provide:
1. Critical Technical Keywords Present in the Resume
2. Missing High-Priority Keywords and Technologies Required by the JD
3. Specific bullet-point suggestions on how to incorporate the missing keywords authentically
"""

# ---------------------------------------------------------
# Execution Handlers
# ---------------------------------------------------------
if btn_eval:
    if uploaded_file is not None and job_description.strip() != "":
        with st.spinner("Analyzing resume with Gemini AI..."):
            pdf_data = input_pdf_setup(uploaded_file)
            response = get_gemini_response(prompt_eval, pdf_data, job_description)
            st.subheader("📋 Comprehensive Profile Review Output:")
            st.write(response)
    else:
        st.warning("Please upload a resume PDF and provide a valid Job Description.")

elif btn_match:
    if uploaded_file is not None and job_description.strip() != "":
        with st.spinner("Calculating ATS score..."):
            pdf_data = input_pdf_setup(uploaded_file)
            response = get_gemini_response(prompt_match, pdf_data, job_description)
            st.subheader("📊 ATS Match Percentage & Evaluation:")
            st.write(response)
    else:
        st.warning("Please upload a resume PDF and provide a valid Job Description.")

elif btn_keywords:
    if uploaded_file is not None and job_description.strip() != "":
        with st.spinner("Extracting missing keywords..."):
            pdf_data = input_pdf_setup(uploaded_file)
            response = get_gemini_response(prompt_keywords, pdf_data, job_description)
            st.subheader("🔑 Keyword Alignment & Gap Analysis:")
            st.write(response)
    else:
        st.warning("Please upload a resume PDF and provide a valid Job Description.")
EOF
```

---

## Step 9: Launching & Running the Streamlit Application

### 9.1 Start Streamlit on Port 8501
Run the application listening on all network interfaces (`0.0.0.0`):
```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```

### 9.2 Expected Terminal Output
```text
  You can now view your Streamlit app in your browser.

  Network URL: http://172.31.x.x:8501
  External URL: http://<AWS-EC2-PUBLIC-IP>:8501
```

### 9.3 Running Persistently in Background (Production / Daemon Mode)
To keep the application running after closing your SSH terminal, use `nohup`:
```bash
nohup streamlit run app.py --server.port 8501 --server.address 0.0.0.0 > streamlit.log 2>&1 &
```

To monitor runtime logs:
```bash
tail -f streamlit.log
```

---

## Step 10: Live Application Testing & Verification Workflow

1. Open your browser and navigate to:  
   `http://<AWS-EC2-PUBLIC-IP>:8501`
2. **Input Job Description:** Copy and paste an actual Cloud / DevOps Engineer JD from LinkedIn, Naukri, or Indeed.
3. **Upload Resume:** Select and upload a candidate resume in `.pdf` format.
4. **Trigger Tests:**
   * Click **"Comprehensive Profile Review"** → Confirms candidate strengths and architectural alignment.
   * Click **"Calculate ATS Match %"** → Returns calculated match percentage (e.g., `72%`) with explanation.
   * Click **"Identify Missing Keywords"** → Highlights missing technologies (e.g., `Terraform`, `Prometheus`, `Helm`, `ArgoCD`).
5. **Resume Iteration:** Add the missing keywords into the resume source document, re-export as PDF, re-upload, and verify that the match percentage reaches **85–95%**.

---

## Step 11: Real-World Troubleshooting & Error Resolution Matrix

| Symptom / Error | Root Cause | Exact Solution |
| :--- | :--- | :--- |
| **`404 Model Not Found` or `Gemini 2.5 Flash is no longer available to new users`** | Google deprecated older Flash endpoints for new Google AI Studio accounts. | In `app.py`, update model string to `gemini-1.5-flash` or `gemini-3.6-flash`. |
| **`429 ResourceExhausted / Rate Limit Exceeded`** | Exceeded Google free-tier token or request-per-minute quota. | Wait 60 seconds before re-trying, or generate a fresh API key from Google AI Studio. |
| **`streamlit: command not found`** | Virtual environment is not activated or packages installed globally without PATH export. | Run `source venv/bin/activate` inside `/root/application-tracking-system`. |
| **`This site can't be reached` on port 8501** | Inbound security rule for port `8501` missing in AWS Security Group. | Go to AWS EC2 Console → Instances → Security → Edit Inbound Rules → Add Custom TCP port `8501` from `0.0.0.0/0`. |
| **`PDFInfoNotInstalledError: Unable to get page count`** | Linux system package `poppler-utils` is missing on the host. | Run `sudo apt install -y poppler-utils`. |
| **`FileNotFoundError: .streamlit/secrets.toml`** | Secrets file missing or placed in the wrong folder. | Ensure `.streamlit/secrets.toml` exists in the same directory where `streamlit run app.py` is executed. |
| **`Streamlit Out of Memory / Process Killed`** | EC2 instance RAM (`t2.micro` 1GB) exhausted by poppler image rendering. | Upgrade EC2 instance type to `t2.medium` or `t3.medium` (4GB RAM) with 30GB disk. |

---

## Step 12: Infrastructure Cleanup & Cost Optimization

To avoid incurring cloud charges after completing the lab:

1. **Terminate EC2 Instance:**
   * Go to AWS EC2 Console → Instances.
   * Select `ATS-Application` → **Instance State** → **Terminate Instance**.
2. **Revoke Google Gemini API Key:**
   * Navigate to Google AI Studio (`https://aistudio.google.com/`).
   * Delete the generated API key.

---

## Step 13: Professional Resume Points & Interview Highlights

Add these bullet points to your resume to demonstrate hands-on AI integration experience:

* **AI-Driven DevOps Integration:** Built and deployed an automated, AI-powered Resume Intelligence & ATS Matching engine on AWS EC2 using Python, Streamlit, and Google Gemini Multimodal LLM APIs.
* **Computer Vision & Document Ingestion:** Implemented server-side PDF-to-image processing pipelines using `pdf2image` and `poppler-utils` to enable direct visual OCR document evaluation by LLM vision models.
* **Prompt Engineering & SRE Evaluation:** Designed structured, role-based system prompts for automated root-cause evaluation, keyword gap detection, and technical qualification scoring against enterprise job profiles.
* **Production Security & Config Hygiene:** Isolated sensitive credentials using Streamlit TOML secrets, Linux least-privilege permissions, and hardened AWS Security Group inbound traffic rules.

---
> [🏠 Back to Master Index](README.md)
