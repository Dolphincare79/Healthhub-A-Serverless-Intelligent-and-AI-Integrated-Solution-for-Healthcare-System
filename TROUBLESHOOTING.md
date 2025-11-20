# HealthHub Troubleshooting Guide

## 1. Serverless Deployment Fails
**Problem:** `npm run deploy` fails or hangs during CloudFormation stack creation.

**Likely Causes:**
- Missing IAM permissions.
- Incorrect AWS CLI configuration (region/profile).
- Timeout due to network issues.

**Solutions:**
- Verify IAM role has `AdministratorAccess`.
- Run:
```bash
aws configure list
```
- Check CloudFormation console for error details.
- Redeploy individual services:
```bash
serverless deploy --stage dev --service SERVICE-NAME
```

---

## 2. API Gateway URL Not Found
**Problem:** Frontend `.env` file does not have the correct API URL.

**Likely Causes:**
- API Gateway deployment incomplete.
- Wrong AWS CLI query in script.

**Solutions:**
- Run:
```bash
aws apigateway get-rest-apis --query 'items[*].[name,id]' --output table
```
- Copy the correct API ID and update `.env`:
```
VITE_API_BASE_URL=https://<API_ID>.execute-api.<region>.amazonaws.com/dev
```

---

## 3. Frontend Fails to Start
**Problem:** `npm run dev` throws errors or does not start.

**Likely Causes:**
- Missing dependencies.
- Incorrect Node.js version.

**Solutions:**
- Ensure Node.js LTS is installed:
```bash
node -v
```
- Reinstall dependencies:
```bash
rm -rf node_modules && npm install
```

---

## 4. Cognito Authentication Issues
**Problem:** Users cannot log in or sign up.

**Likely Causes:**
- Cognito User Pool misconfigured.
- Missing environment variables.

**Solutions:**
- Check Cognito console for user pool settings.
- Confirm `VITE_API_BASE_URL` and Cognito details in `.env`.

---

## 5. Lambda Timeout or Errors
**Problem:** Backend services return 500 errors.

**Likely Causes:**
- Lambda function timeout.
- Missing permissions for DynamoDB or S3.

**Solutions:**
- Increase Lambda timeout in `serverless.yml`.
- Attach required IAM policies for DynamoDB and S3.

---

## 6. CloudWatch Logs Show Errors
**Problem:** Deployment succeeds but app misbehaves.

**Solutions:**
- Check logs:
```bash
aws logs describe-log-groups
aws logs tail /aws/lambda/<function-name> --follow
```

---

## 7. Port Access Issues
**Problem:** Cannot access frontend via `http://<EC2_PUBLIC_IP>:5173`.

**Likely Causes:**
- Security Group missing inbound rule for port 5173.

**Solutions:**
- Add inbound rule for TCP port 5173 in EC2 Security Group.

---

## Quick AWS CLI Debugging Commands
- List EC2 instances:
```bash
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,PublicIpAddress]' --output table
```
- Check CloudFormation stack status:
```bash
aws cloudformation describe-stacks --query 'Stacks[*].[StackName,StackStatus]' --output table
```
- View API Gateway endpoints:
```bash
aws apigateway get-rest-apis --query 'items[*].[name,id]' --output table
```
