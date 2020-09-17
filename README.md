# cloudwatch2loggly
Sends logs from Cloudwatch logs to Loggly using Lamda function

## More information about AWS Lambda and Loggly
  * http://aws.amazon.com/lambda/
  * https://www.loggly.com/

## Getting started with AWS Lambda
Getting started documentation for AWS lambda can be found in [this 
article](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html). There is also [this blog post](http://alestic.com/2014/11/aws-lambda-cli) on how to set things up using the command line tools.

## Creating lambda function in AWS
1. Build lambda function code
   * Clone the Git repository:   
   `git clone https://github.com/loggly/cloudwatch2loggly.git`
   * Go to cloned folder:   
   `cd cloudwatch2loggly`
   * Install dependencies:   
   `npm install`
   * Create a .zip file for upload to AWS console later:   
   `zip -r cloudwatch2loggly.zip index.js node_modules`
1. Create Role
   * Sign in to your AWS account and open IAM console https://console.aws.amazon.com/iam/
   * In your IAM console create a new Role say, `cloudwatch-full-access`
   * Select Role Type as **AWS Lambda**
   * Apply policy **CloudWatchFullAccess** and save.
1. Create KMS Key
   * Create a KMS key - http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html
   * Encrypt the Loggly Customer Token using the AWS CLI   
   `aws kms encrypt --key-id alias/<your KMS key alias> --plaintext "<your loggly customer token>"`
   * Copy or keep `CiphertextBlob` attribute for furhter usage in the next step.
1. Create lambda function
   * Go to https://console.aws.amazon.com/lambda/home
   * Click **Create a Lambda function** button.
   * Select **Author from scratch** option
   * Set **Function name** for example to `cloudwatch2loggly`
   * Set **Runtime** to `Node.js 12.x`
   * Under **Permissions** click on **Choose or create an execution role**
   * Select **Use an existing role** and select **cloudwatch-full-access** role created above in step 1
   * Click on **Create function** button
   * Scroll to **Function code** section
   * Select **Upload a .zip file** in **Code entry type** dropdown
   * Upload lambda function (zip file `cloudwatch2loggly.zip` you made above)
   * Go to **Environment variables** section
   * Define new environment variable **kmsEncryptedCustomerToken** and set it to `CiphertextBlob` value from step 3 above (Create KMS Key)
   * Scroll to **Basic settings** section
   * Set **Memory (MB)** to **512 MB**
   * Set Timeout to **2** minutes
   * Scroll up to **Designer** section (expand if it's collapsed)
   * Click on **Add trigger** button
   * Select on **CloudWatch Logs**
   * Select appropriate **Log group**
   * Name the filter
   * Make sure the checkbox **Enable trigger** is checked
   * Click on **Add**
   * Click on **Save** to save the whole lambda function.
   * Wait for the events to occur in Loggly

**NOTE**: Always use latest version of **AWSCLI**. Some features like KMS may not work on older versions of AWSCLI. To upgrade, use the command given below

`pip install --upgrade awscli`











## Get the code and prepare it for the uploading to AWS
* Clone the git repo
```bash
git clone https://github.com/loggly/cloudwatch2loggly.git
cd cloudwatch2loggly
```
* Install required npm packages.
```
npm install
```

* zip up your code
```bash
zip -r cloudwatch2loggly.zip index.js node_modules
```

The resulting zip (cloudwatch2loggly.zip) is what you will upload to AWS.

## Setting up AWS
For all of the AWS setup, I used the AWS console following [this 
example](http://docs.aws.amazon.com/lambda/latest/dg/getting-started-amazons3-events.html).  Below, you will find a high-level 
description of how to do this.  I also found [this blog post](http://alestic.com/2014/11/aws-lambda-cli) on how to set things up 
using the command line tools.

### Create and upload the cloudwatch2loggly function in the AWS Console
1. Create Role
  1. Sign in to your AWS account and open IAM console https://console.aws.amazon.com/iam/
  2. In your IAM console create a new Role say, 'cloudwatch-full-access'
  3. Select Role Type as 'AWS Lambda'
  4. Apply policy 'CloudWatchFullAccess' and save.
2. Create KMS Key
  1. Create a KMS key - http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html
  2. Encrypt the Loggly Customer Token using the AWS CLI - **aws kms encrypt --key-id alias/&lt;your KMS key arn&gt; --plaintext "&lt;your loggly customer token&gt;"**
  3. Copy the base-64 encoded, encrypted token from step 2's CLI output (CiphertextBlob attribute) and replace it with the "your KMS encypted key" in the script at line no 22
3. Create lambda function
  1. https://console.aws.amazon.com/lambda/home
  2. Click "Create a Lambda function" button. *(Choose "Upload a .ZIP file")*
    * **Name:** *cloudwatch2loggly*
    * Upload lambda function (zip file you made above.)
    * **Handler*:** *index.handler*
    * Set Role : *cloudwatch-full-access*
    * Set Timeout to 2 minutes
  3. Go to your Lamda function and select the "Event sources" tab
    * Click on **Add Event Source**
    * Event Source Type : *CloudWatch Logs*
    * Log Group : Select your log group whose logs you want to send to Loggly.
    * Filter Name: Provide your filter name.
    * Filter Pattern: This is not a mandatory field. You can keep it empty.
    * Enable Event Source : *Enable Now*
 Now click on submit and wait for the events to occur in Loggly

**NOTE**: Always use latest version of **AWSCLI**. Some features like KMS may not work on older versions of AWSCLI. To upgrade, use the command given below

`pip install --upgrade awscli`


