# Batch-45 — Interview & Scenario Q&A
## AI + Cloud + DevOps — Day 4

> Short, crisp interview-ready answers based on the provided Day-4 transcript.  
> Focus: AI fundamentals, Generative AI, Agentic AI, AWS AI services, GitHub Copilot, Kubernetes, AIOps, Python for DevOps, and Terraform.

---

## Part 1 — 20 Interview Questions & Answers

### 1. What is Artificial Intelligence (AI)?
**Answer:** AI enables machines to perform tasks that normally require human intelligence. In the session, it was framed as **“automation of automation”** to accelerate existing DevOps automation.

### 2. What is Generative AI?
**Answer:** Generative AI creates content, answers, plans, or information based on a prompt and its training data.

### 3. What is Agentic AI?
**Answer:** Agentic AI goes beyond generating information and can take actions autonomously to complete a task.

### 4. What is the difference between Generative AI and Agentic AI?
**Answer:** Generative AI mainly **generates information**; Agentic AI can **take actions** based on the goal.

### 5. What is a prompt?
**Answer:** A prompt is the input or instruction given to an AI model to produce an output.

### 6. What is prompt engineering?
**Answer:** Prompt engineering is the practice of creating clear and effective instructions so an AI model produces useful results.

### 7. What is AI hallucination?
**Answer:** Hallucination is when an AI model produces incorrect or unreliable information as if it were correct.

### 8. What is RAG?
**Answer:** RAG stands for **Retrieval-Augmented Generation**. It combines retrieved information with AI generation to improve the relevance of responses.

### 9. What is Machine Learning?
**Answer:** Machine Learning is a subset of AI where systems learn patterns from data to perform tasks or make predictions.

### 10. What is Deep Learning?
**Answer:** Deep Learning is a subset of Machine Learning that uses deep neural-network-based approaches to learn complex patterns.

### 11. What is Computer Vision?
**Answer:** Computer Vision enables machines or systems to read and interpret visual data such as images.

### 12. What is Amazon Rekognition?
**Answer:** Amazon Rekognition is a managed AWS AI service used to analyze images and identify objects or visual information.

### 13. What is Amazon Textract?
**Answer:** Amazon Textract is a managed AWS AI service that extracts text and layout information from documents or images.

### 14. How can Rekognition and Textract work together?
**Answer:** Rekognition can analyze the image, while Textract can extract text from it. Both can be integrated into an application workflow through APIs.

### 15. What is AIOps?
**Answer:** AIOps applies AI to IT operations to improve incident detection, response, automation, and operational workflows.

### 16. What is GitHub Copilot?
**Answer:** GitHub Copilot is an AI coding assistant that can generate code, suggestions, commit messages, and other development assistance from prompts.

### 17. How do you activate Copilot in VS Code?
**Answer:** Use the latest VS Code, sign in with a GitHub account, and open Copilot Chat using **Ctrl + Shift + I**.

### 18. What is a Pod in Kubernetes?
**Answer:** A Pod is the smallest deployable unit in Kubernetes and normally contains one container, although multiple containers are supported.

### 19. What is a Kubernetes Namespace?
**Answer:** A Namespace provides an isolated logical environment inside a Kubernetes cluster, commonly used to separate environments or projects.

### 20. What is `terraform import`?
**Answer:** `terraform import` brings an existing infrastructure resource under Terraform state management.

---

# Part 2 — 20 Scenario-Based Questions & Answers

### 1. Scenario: You need AI to create a 5-day travel plan, but not book anything. What should you use?
**Answer:** Use **Generative AI** because the requirement is to generate information and a plan, not take actions.

### 2. Scenario: You want an AI system to search job portals, match jobs with a resume, and automatically apply. What concept fits?
**Answer:** **Agentic AI**, because it can perform actions autonomously as part of completing the goal.

### 3. Scenario: AI generated an incorrect answer. What is this called?
**Answer:** **AI hallucination**. Validate the output and provide correction or better context.

### 4. Scenario: Your company wants AI to answer questions using internal documents. What approach can help?
**Answer:** Use **RAG**, where relevant information is retrieved and then supplied to the AI for generation.

### 5. Scenario: A traffic camera captures a vehicle image and you need to analyze the vehicle. Which AWS service can help?
**Answer:** **Amazon Rekognition** can analyze the image and identify visual information.

### 6. Scenario: You have a vehicle number-plate image and need to extract the text from it. Which AWS service can help?
**Answer:** **Amazon Textract** can extract text from the image.

### 7. Scenario: After extracting a number plate, you need to notify the vehicle owner. Which AWS service from the session can send the notification?
**Answer:** **Amazon SNS** can send notifications through supported channels such as SMS or email.

### 8. Scenario: You want a complete flow for an automated traffic-challan system. What architecture can you use?
**Answer:** **Camera → Rekognition → Textract → Application/Database → SNS**.

### 9. Scenario: Your DevOps team wants AI to help reduce repetitive engineering work. What is the benefit?
**Answer:** AI acts as a **productivity multiplier**, helping engineers generate code, automate tasks, and accelerate workflows.

### 10. Scenario: You need to generate a Python stopwatch quickly in VS Code. Which tool from the session can help?
**Answer:** **GitHub Copilot**. Give it a clear prompt such as “Write a stopwatch Python code.”

### 11. Scenario: Copilot generated Kubernetes YAML. Should you blindly use it in an interview?
**Answer:** No. You should understand and explain the YAML because interviewers can ask what each field does.

### 12. Scenario: In VS Code, typing `kube.pod` generates a Kubernetes YAML template. Is this necessarily AI?
**Answer:** No. The transcript specifically notes that this comes from the **Kubernetes extension/template snippets**, not AI.

### 13. Scenario: Your team wants separate dev, QA, and production environments inside one Kubernetes cluster. What can you use?
**Answer:** Use **Kubernetes Namespaces** to provide logical isolation between environments.

### 14. Scenario: A Kubernetes Pod is running a single application container. What does Kubernetes use to wrap and orchestrate that container?
**Answer:** A **Pod**. Kubernetes manages containers through Pods.

### 15. Scenario: Prometheus monitoring data stops because the monitoring Pod goes down. What should you do?
**Answer:** Check the Pod logs and root cause, then use a resilient setup such as the **Prometheus Operator** or appropriate Helm/self-healing configuration.

### 16. Scenario: You are a DevOps engineer and have Python on your resume. What practical knowledge should you be ready to demonstrate?
**Answer:** Basic automation scripting: imports, `print`, `try/except`, `while`, `for` loops, and running Python files.

### 17. Scenario: You have a Python file called `script.py`. How do you run it?
**Answer:** Use:
```bash
python script.py
```
Python must be installed and the environment must be configured correctly.

### 18. Scenario: An AWS resource already exists manually, but you now want Terraform to manage it. What should you use?
**Answer:** Use **`terraform import`** to bring the existing resource into Terraform state management.

### 19. Scenario: A high-severity production incident occurs and needs escalation to on-call engineers. What AIOps-style workflow was discussed?
**Answer:** A tool such as **PagerDuty** can trigger alerts and escalate them through the defined on-call chain if the incident is not acknowledged.

### 20. Scenario: AI generated your complete Kubernetes YAML, but an interviewer asks you to explain it. What should you do?
**Answer:** Explain the important fields yourself, such as `apiVersion`, `kind`, `metadata`, `namespace`, and container configuration. AI can write code, but you must understand what it generated.

---

## Quick Interview Revision

Remember these key mappings:

| Requirement | Concept / Tool |
|---|---|
| Generate content or plans | Generative AI |
| AI that can take actions | Agentic AI |
| Retrieve information before generation | RAG |
| Analyze images | Amazon Rekognition |
| Extract text from documents/images | Amazon Textract |
| Send notifications | Amazon SNS |
| AI for operations | AIOps |
| AI coding assistant | GitHub Copilot |
| Smallest deployable Kubernetes unit | Pod |
| Kubernetes logical isolation | Namespace |
| Existing resource into Terraform state | `terraform import` |
| DevOps Python use case | Automation scripting |

## Interview Tip

Keep answers **crisp and direct**. If AI generates your code or YAML, do not memorize it blindly — understand the important parts and be ready to explain them in your own words.

*Source: Batch-45 Day-4 session transcript provided for this task.*
