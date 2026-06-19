# 1. IAM Role for Lambda: An IAM role that grants the Lambda function permissions to stop RDS instances.

# 2. Lambda Function: A Python-based Lambda function that uses the AWS SDK (boto3) to stop the specified RDS instance(s).

# 3. EventBridge Rule (CloudWatch Event Rule): A scheduled EventBridge rule that triggers the Lambda function every 7 days using a cron expression.

# 4. To deploy:
- Save the Terraform code in .tf files and the Python code as lambda_function.py.
- Zip the Python file into lambda_function.zip.
- Initialize Terraform: terraform init
- Plan the deployment: terraform plan
- Apply the changes: terraform apply