# Project5-AWS-S3-Demo-Projects

**Step-by-Step Guide: Managing S3 Bucket Access and Hosting a Static Website**
### **1. Managing S3 Bucket Access**

This section covers how to restrict access to an S3 bucket, allowing only specific users (like the bucket owner) to have access.

#### **Step 1: Grant S3 Full Access to an IAM User**

1. **Log into AWS Management Console**:
   - Use your credentials to log into the AWS Management Console.

2. **Navigate to IAM (Identity and Access Management)**:
   - From the AWS Management Console, search for and select **IAM**.

3. **Create an IAM User (if not already created)**:
   - Go to **Users** and click **Add user**.
   - Enter a username (e.g., `devops-user`).
   - For **Access type**, select **Programmatic access** and **AWS Management Console access**.
   - Attach the policy **AmazonS3FullAccess** to the user to grant full access to S3.
   - Complete the process by reviewing and creating the user.

4. **Verify S3 Access**:
   - Log in as the IAM user or switch roles to this user.
   - Navigate to **S3** in the AWS Management Console.
   - Ensure that the IAM user can see and access all S3 buckets.

#### **Step 2: Restrict Access Using Bucket Policies**

1. **Navigate to S3 and Select Your Bucket**:
   - Go to **S3** in the AWS Management Console.
   - Click on the bucket you want to manage.

2. **Go to the Permissions Tab**:
   - Once inside the bucket, click on the **Permissions** tab.
   - Scroll down to **Bucket Policy**.

3. **Edit the Bucket Policy**:
   - Click **Edit** under the Bucket Policy section.
   - To restrict access, add the following JSON policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:PrincipalArn": "arn:aws:iam::your-account-id:root"
        }
      }
    }
  ]
}
```

   - **Explanation**:
     - **Effect**: Denies the specified actions.
     - **Principal**: `*` means everyone.
     - **Action**: `s3:*` means all S3 actions.
     - **Resource**: Specifies the bucket and all objects within it.
     - **Condition**: `StringNotEquals` ensures the policy applies to everyone except the bucket owner.
     - Replace `"your-bucket-name"` with your actual S3 bucket name.
     - Replace `"your-account-id"` with your actual AWS account ID.

4. **Save the Policy**:
   - Click **Save changes** to apply the policy.

#### **Step 3: Validate Access Restrictions**

1. **Test Access as the IAM User**:
   - Log in as the IAM user in an incognito browser or a separate session.
   - Attempt to access the S3 bucket that was just restricted.

2. **Verify Access Denial**:
   - The IAM user should receive an "Insufficient Permissions" error when trying to list or download objects from the bucket.
   - This confirms that the policy is correctly restricting access.

---

### **2. Hosting a Static Website on S3**

This section will guide you through setting up an S3 bucket to host a static website.

#### **Step 1: Create and Configure the S3 Bucket**

1. **Create a New S3 Bucket**:
   - In the AWS Management Console, go to **S3**.
   - Click on **Create bucket**.
   - Provide a unique bucket name (e.g., `my-website-bucket`).
   - Choose the appropriate AWS region.

2. **Configure Bucket Settings**:
   - Under **Bucket settings**, leave all options as default or adjust them according to your requirements.
   - Ensure that **Block all public access** is checked initially (you'll change this later).

3. **Upload Your Website Files**:
   - Click on the bucket name to enter it.
   - Click **Upload** and upload your `index.html` (and any other necessary files).

#### **Step 2: Enable Static Website Hosting**

1. **Navigate to the Properties Tab**:
   - Inside your bucket, click on the **Properties** tab.

2. **Enable Static Website Hosting**:
   - Scroll down to **Static website hosting**.
   - Click **Edit** and then **Enable** static website hosting.
   - Select **Host a static website**.
   - Enter `index.html` as the **Index document**.
   - If you have an error page, specify the **Error document** (e.g., `error.html`).
   - Click **Save changes**.

3. **Public Access Configuration**:
   - By default, your bucket will still be private.

#### **Step 3: Adjust Bucket Permissions for Public Access**

1. **Unblock Public Access**:
   - Go to the **Permissions** tab.
   - Click **Edit** under **Block all public access**.
   - Uncheck **Block all public access** and save the changes.

2. **Confirm Public Access Changes**:
   - A confirmation dialog will appear; click **Confirm**.

#### **Step 4: Add a Bucket Policy for Public Read Access**

1. **Add a Public Read Bucket Policy**:
   - Go to **Bucket Policy** under the **Permissions** tab.
   - Add the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```
   - Replace `"your-bucket-name"` with your actual bucket name.
   - This policy allows public read access to all objects in your bucket.

2. **Save the Policy**:
   - Click **Save changes** to apply the policy.

#### **Step 5: Access the Static Website**

1. **Retrieve the Website URL**:
   - Go back to the **Properties** tab.
   - Scroll down to **Static website hosting**.
   - The **Bucket website endpoint** will provide a URL (e.g., `http://your-bucket-name.s3-website-region.amazonaws.com`).

2. **Test Your Website**:
   - Open the URL in your web browser to see your static website live.

