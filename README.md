# AWS LLM Context Query

## Overview
A minimal serverless project demonstrating how to query an LLM hosted on AWS SageMaker using a Lambda function.  
The function retrieves context data from an S3 bucket, merges it with an input query, and returns a context-aware model response.

This project is designed for AWS users who want a simple working example of end-to-end LLM orchestration across Lambda, S3, and SageMaker.

---

## ⚠️ Cost Warning
Running LLMs on SageMaker can be **costly** very quickly depending on the instance type and runtime duration.  
Before deploying, configure:

- **AWS Budgets** (set alerts for monthly spend)
- **Cost Anomaly Detection**
- **Auto-termination policies** for endpoints when idle  


---
``
## 🧩 Project Flow
````

Query Input → Lambda (Python)
→ Retrieves context.json from S3
→ Sends query + context to SageMaker LLM Endpoint
→ Returns formatted model response



---

## 🧠 Components
- **Lambda Function:** Handles incoming queries and manages S3/SageMaker interactions.  
- **S3 Bucket:** Stores JSON files containing static or dynamic context data.  
- **SageMaker Endpoint:** Hosts or serves the language model (LLM) for inference.

---

## ⚙️ Usage

### 1. Configure Variables
Edit the following variables inside `lambda_function.py` before deployment:

```python
SAGEMAKER_ENDPOINT = "your-sagemaker-endpoint-name"
BUCKET_NAME = "your-s3-bucket-name"
CONTEXT_FILE = "context.json"
AWS_ACCOUNT_ID = "your-aws-account-id"

If your context file is stored in a subfolder within S3, modify:

```python
CONTEXT_FILE = "path/to/context.json"
```

---

### 2. Deploy Lambda

**Zip the code:**

```bash
zip function.zip lambda_function.py
```

**Deploy:**

```bash
aws lambda create-function \
  --function-name llm-context-query \
  --runtime python3.9 \
  --role arn:aws:iam::<AWS_ACCOUNT_ID>:role/<YourLambdaRole> \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://function.zip
```

**Update existing function:**

```bash
aws lambda update-function-code \
  --function-name llm-context-query \
  --zip-file fileb://function.zip
```

---

### 3. Example Event Payload

Send a test event from the AWS Lambda console or CLI:

```json
{
  "query": "Tell me what you know about the Gameboy?"
}
```

---

### 4. Example Output

```json
{
  "response": "The Game Boy is a small handheld gaming device made by Nintendo. It first came out in Japan on April 21, 1989, and then later in North America and other places. It was different from earlier Nintendo handhelds because it could play different games using cartridges."
}
```

---
````
## 📄 Notes

* SageMaker endpoints can remain active and accumulate costs even when idle.
  **Always delete or stop your endpoint after testing.**
* Ensure your IAM role attached to Lambda has the following permissions:

  * `s3:GetObject`
  * `sagemaker:InvokeEndpoint`
  * `logs:CreateLogStream`
  * `logs:PutLogEvents`

```
```
