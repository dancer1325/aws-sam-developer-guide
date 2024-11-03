# Tutorial: Deploying a Hello World application<a name="serverless-getting-started-hello-world"></a>

* goal
  * via AWS SAM CLI, to
    + Initialize, build, and deploy a sample **Hello World** application\.
    + Make local changes and sync -- to -- AWS CloudFormation\.
    + Test your application | AWS Cloud\.
    + Optionally, perform local testing | your development host\.
    + Delete the sample application from the AWS Cloud\.

* **Hello World** application
  * == **Amazon API Gateway** + **AWS Lambda** + **AWS IAM role**  
    * API endpoint / -- used to invoke -- your function\.
    + Function / 
      + processes the HTTP API GET request
      + returns a `hello world` message\.
    + permissions for the services -- to securely -- interact\.

    ![\[A diagram of a Lambda function that's invoked when you send a GET request to the API Gateway endpoint.\]](http://docs.aws.amazon.com/serverless-application-model/latest/developerguide/images/gs-01.png)

**Topics**
+ [Prerequisites](#serverless-getting-started-hello-world-prerequisites)
+ [Step 1: Initialize the sample Hello World application](#serverless-getting-started-hello-world-init)
+ [Step 2: Build your application](#serverless-getting-started-hello-world-build)
+ [Step 3: Deploy your application to the AWS Cloud](#serverless-getting-started-hello-world-deploy)
+ [Step 4: Run your application](#serverless-getting-started-hello-world-run)
+ [Step 5: Modify and sync your application to the AWS Cloud](#serverless-getting-started-hello-world-sync)
+ [Step 6: \(Optional\) Test your application locally](#serverless-getting-started-hello-world-test)
+ [Step 7: Delete your application from the AWS Cloud](#serverless-getting-started-hello-world-delete)
+ [Troubleshooting](#serverless-getting-started-hello-world-troubleshoot)
+ [Learn more](#serverless-getting-started-hello-world-learn)

## Prerequisites<a name="serverless-getting-started-hello-world-prerequisites"></a>

+ [AWS SAM prerequisites](prerequisites.md)
+ [Installing the AWS SAM CLI](install-sam-cli.md)

## Step 1: Initialize the sample Hello World application<a name="serverless-getting-started-hello-world-init"></a>

1. `sam init`
2. AWS SAM CLI -- will guide you through -- initializing a new application
   1. Select **AWS Quick Start Templates**
   2. Choose the **Hello World Example** template and download it\.
   3. Use the Python runtime and `zip` package type\.
   4. opt OUT of [AWS X-Ray tracing](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)
   5. opt OUT of monitoring -- via -- [Amazon CloudWatch Application Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-application-insights.html)
   6. opt IN [structured logging](https://docs.aws.amazon.com/lambda/latest/operatorguide/parse-logs.html)
   7. Name your application as **sam\-app**\.

3. AWS SAM CLI 
   1. downloads your starting template
   2. creates the application project directory structure | your local machine
      * example of the AWS SAM CLI output:

         ```
         Cloning from https://github.com/aws/aws-sam-cli-app-templates (process may take a moment)
   
             -----------------------
             Generating application:
             -----------------------
             Name: sam-app
             Runtime: python3.9
             Architectures: x86_64
             Dependency Manager: pip
             Application Template: hello-world
             Output Directory: .
             Configuration file: sam-app/samconfig.toml
   
             Next steps can be found in the README file at sam-app/README.md
   
   
         Commands you can use next
         =========================
         [*] Create pipeline: cd sam-app && sam pipeline init --bootstrap
         [*] Validate SAM template: cd sam-app && sam validate
         [*] Test Function in the Cloud: cd sam-app && sam sync --stack-name {stack-name} --watch
         ```

4. Check the structure of the project created 

      ```
      $ cd sam-app
   
      $ tree
   
      ├── README.md
      ├── __init__.py
      ├── events
      │   └── event.json
      ├── hello_world
      │   ├── __init__.py
      │   ├── app.py
      │   └── requirements.txt
      ├── samconfig.toml
      ├── template.yaml
      └── tests
          ├── __init__.py
          ├── integration
          │   ├── __init__.py
          │   └── test_api_gateway.py
          ├── requirements.txt
          └── unit
              ├── __init__.py
              └── test_handler.py
           
      6 directories, 14 files
      ```

  * important files
    * `hello_world/app.py`
      * == your Lambda function code
    * `hello_world/requirements.txt`
      * == Python dependencies / required by your Lambda function
    * `samconfig.toml`
      * == Configuration file -- for your -- application /
        * stores default parameters -- used by the -- AWS SAM CLI
    * `template.yaml`
      * == AWS SAM template / contains your application infrastructure code\.

## Step 2: Build your application<a name="serverless-getting-started-hello-world-build"></a>

* goal
  * build your application
    * AWS SAM CLI 
      * creates a `.aws-sam/`
      * organizes your
        * function dependencies,
        * project code,
        * project files
  * prepare for deployment

*
  ```
  sam-app$ sam build
  ```

  * if you do NOT have Python | your local machine -> use `sam build -use-container`
    * create a Docker container / includes your function's
      * runtime
      * dependencies
    * requirements
      * Docker | your local machine
  * example of the AWS SAM CLI output

      ```
        $ sam build
        Starting Build use cache
        Manifest file is changed (new hash: 3298f1304...d4d421) or dependency folder (.aws-sam/deps/4d3dfad6-a267-47a6-a6cd-e07d6fae318c) is missing for (HelloWorldFunction), downloading dependencies and copying/building source
        Building codeuri: /Users/.../Demo/sam-tutorial1/sam-app/hello_world runtime: python3.9 metadata: {} architecture: x86_64 functions: HelloWorldFunction
        Running PythonPipBuilder:CleanUp
        Running PythonPipBuilder:ResolveDependencies
        Running PythonPipBuilder:CopySource
        Running PythonPipBuilder:CopySource
        
        Build Succeeded
        
        Built Artifacts  : .aws-sam/build
        Built Template   : .aws-sam/build/template.yaml
        
        Commands you can use next
        =========================
        [*] Validate SAM template: sam validate
        [*] Invoke Function: sam local invoke
        [*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
        [*] Deploy: sam deploy --guided
        ```

  * shortened example of `.aws-sam/`

    ```
      .aws-sam
      ├── build
      │   ├── HelloWorldFunction
      │   │   ├── __init__.py
      │   │   ├── app.py
      │   │   └── requirements.txt
      │   └── template.yaml
      └── build.toml
      ```

    + `build/HelloWorldFunction`
      + your Lambda function code & dependencies
        + specific directory / EACH function in your application\.
    + `build/template.yaml`
      + copy of your AWS SAM template / -- is referenced by -- AWS CloudFormation | deployment\.
    + `build.toml`
      + stores default parameter values / -- referenced by the -- AWS SAM CLI |
        + building your application
        + deploying your application\.

## Step 3: Deploy your application to the AWS Cloud<a name="serverless-getting-started-hello-world-deploy"></a>

* goal
  * deploy -- via AWS SAM CLI -- your application | AWS Cloud
    * steps
      * Guide you -- through -- configuring your application settings for deployment\.
      + Upload your application files | Amazon S3
      + AWS SAM template -- is transformed into an -- AWS CloudFormation template
      + uploads your template | AWS CloudFormation service -- to provision -- your AWS resources\.

* see [sampleApp](../sam-app/README.md#deploy-the-sample-application)

## Step 4: Run your application<a name="serverless-getting-started-hello-world-run"></a>

* goal
  * send a GET request -- to -- your API endpoint

* see [sampleApp](../sam-app/README.md#run-your-application)

## Step 5: Modify and sync your application to the AWS Cloud<a name="serverless-getting-started-hello-world-sync"></a>

* TODO:
In this step, you use the AWS SAM CLI sam sync \-\-watch command to sync local changes to the AWS Cloud\.

**To use sam sync**

1. In your command line, from the `sam-app` project directory, run the following:

   ```
   $ sam sync --watch
   ```

1. The AWS SAM CLI prompts you to confirm that you are syncing a development stack\. Since the sam sync \-\-watch command automatically deploys local changes to the AWS Cloud in real time, we recommend it for development environments only\.

   The AWS SAM CLI performs an initial deployment before it begins monitoring for local changes\. The following is an example output:

   ```
   $ sam sync --watch
   The SAM CLI will use the AWS Lambda, Amazon API Gateway, and AWS StepFunctions APIs to upload your code without
   performing a CloudFormation deployment. This will cause drift in your CloudFormation stack.
   **The sync command should only be used against a development stack**.
   
   Confirm that you are synchronizing a development stack.
   
   Enter Y to proceed with the command, or enter N to cancel:
    [Y/n]: y
   Queued infra sync. Waiting for in progress code syncs to complete...
   Starting infra sync.
   Manifest is not changed for (HelloWorldFunction), running incremental build
   Building codeuri: /Users/.../Demo/sam-tutorial1/sam-app/hello_world runtime: python3.9 metadata: {} architecture: x86_64 functions: HelloWorldFunction
   Running PythonPipBuilder:CopySource
   
   Build Succeeded
   
   Successfully packaged artifacts and wrote output template to file /var/folders/45/5ct135bx3fn2551_ptl5g6_80000gr/T/tmpq3x9vh63.
   Execute the following command to deploy the packaged template
   sam deploy --template-file /var/folders/45/5ct135bx3fn2551_ptl5g6_80000gr/T/tmpq3x9vh63 --stack-name <YOUR STACK NAME>
   
   
       Deploying with following values
       ===============================
       Stack name                   : sam-app
       Region                       : us-west-2
       Disable rollback             : False
       Deployment s3 bucket         : aws-sam-cli-managed-default-samclisourcebucket-1a4x26zbcdkqr
       Capabilities                 : ["CAPABILITY_NAMED_IAM", "CAPABILITY_AUTO_EXPAND"]
       Parameter overrides          : {}
       Signing Profiles             : null
   
   Initiating deployment
   =====================
   
   
   2023-03-15 13:10:05 - Waiting for stack create/update to complete
   
   CloudFormation events from stack operations (refresh every 0.5 seconds)
   ---------------------------------------------------------------------------------------------------------------------------------------------
   ResourceStatus                      ResourceType                        LogicalResourceId                   ResourceStatusReason
   ---------------------------------------------------------------------------------------------------------------------------------------------
   UPDATE_IN_PROGRESS                  AWS::CloudFormation::Stack          sam-app                             Transformation succeeded
   CREATE_IN_PROGRESS                  AWS::CloudFormation::Stack          AwsSamAutoDependencyLayerNestedSt   -
                                                                           ack
   CREATE_IN_PROGRESS                  AWS::CloudFormation::Stack          AwsSamAutoDependencyLayerNestedSt   Resource creation Initiated
                                                                           ack
   CREATE_COMPLETE                     AWS::CloudFormation::Stack          AwsSamAutoDependencyLayerNestedSt   -
                                                                           ack
   UPDATE_IN_PROGRESS                  AWS::Lambda::Function               HelloWorldFunction                  -
   UPDATE_COMPLETE                     AWS::Lambda::Function               HelloWorldFunction                  -
   UPDATE_COMPLETE_CLEANUP_IN_PROGRE   AWS::CloudFormation::Stack          sam-app                             -
   SS
   UPDATE_COMPLETE                     AWS::CloudFormation::Stack          sam-app                             -
   ---------------------------------------------------------------------------------------------------------------------------------------------
   
   CloudFormation outputs from deployed stack
   ----------------------------------------------------------------------------------------------------------------------------------------------
   Outputs
   ----------------------------------------------------------------------------------------------------------------------------------------------
   Key                 HelloWorldFunctionIamRole
   Description         Implicit IAM Role created for Hello World function
   Value               arn:aws:iam::513423067560:role/sam-app-HelloWorldFunctionRole-15GLOUR9LMT1W
   
   Key                 HelloWorldApi
   Description         API Gateway endpoint URL for Prod stage for Hello World function
   Value               https://ets1gv8lxi.execute-api.us-west-2.amazonaws.com/Prod/hello/
   
   Key                 HelloWorldFunction
   Description         Hello World Lambda Function ARN
   Value               arn:aws:lambda:us-west-2:513423067560:function:sam-app-HelloWorldFunction-yQDNe17r9maD
   ----------------------------------------------------------------------------------------------------------------------------------------------
   
   
   Stack update succeeded. Sync infra completed.
   
   Infra sync completed.
   CodeTrigger not created as CodeUri or DefinitionUri is missing for ServerlessRestApi.
   ```

Next, you will modify your Lambda function code\. The AWS SAM CLI will automatically detect this change and sync your application to the AWS Cloud\.

**To modify and sync your application**

1. In your IDE of choice, open the `sam-app/hello_world/app.py` file\.

1. Change the `message` and save your file\. The following is an example:

   ```
   import json
   ...
   def lambda_handler(event, context):
       ...
       return {
           "statusCode": 200,
           "body": json.dumps({
               "message": "hello everyone!",
               ...
           }),
       }
   ```

1. The AWS SAM CLI detects your change and syncs your application to the AWS Cloud\. The following is an example output:

   ```
   Syncing Lambda Function HelloWorldFunction...
   Manifest is not changed for (HelloWorldFunction), running incremental build
   Building codeuri: /Users/.../Demo/sam-tutorial1/sam-app/hello_world runtime: python3.9 metadata: {} architecture: x86_64 functions: HelloWorldFunction
   Running PythonPipBuilder:CopySource
   Finished syncing Lambda Function HelloWorldFunction.
   ```

1. To verify your change, send a GET request to your API endpoint again\.

   ```
   $ curl https://ets1gv8lxi.execute-api.us-west-2.amazonaws.com/Prod/hello/
   {"message": "hello everyone!"}
   ```

## Step 6: \(Optional\) Test your application locally<a name="serverless-getting-started-hello-world-test"></a>

* requirements
  * Docker | your local machine
    * see [Installing Docker](install-docker.md)
* `sam local`
  * allows
    * testing your application locally
  * how does it work?
    * 👀AWS SAM CLI -- creates, via Docker -- a local environment👀
      * local environment == cloud-based execution environment -- of -- your Lambda function
  * uses
    * call that HTTP API endpoint locally -- to invoke -- your Lambda function
  * `sam local invoke` | `sam-app` project directory
    * example output

      ```
        $ sam local invoke
        Invoking app.lambda_handler (python3.9)
        Local image was not found.
        Removing rapid images for repo public.ecr.aws/sam/emulation-python3.9
        Building image.....................
        Using local image: public.ecr.aws/lambda/python:3.9-rapid-x86_64.
        
        Mounting /Users/.../Demo/sam-tutorial1/sam-app/.aws-sam/build/HelloWorldFunction as /var/task:ro,delegated inside runtime container
        START RequestId: b046db01-2a00-415d-af97-35f3a02e9eb6 Version: $LATEST
        END RequestId: b046db01-2a00-415d-af97-35f3a02e9eb6
        REPORT RequestId: b046db01-2a00-415d-af97-35f3a02e9eb6    Init Duration: 1.01 ms    Duration: 633.45 ms    Billed Duration: 634 ms    Memory Size: 128 MB    Max Memory Used: 128 MB
        {"statusCode": 200, "body": "{\"message\": \"hello world\"}"}
        ```
      
    * Problems:
      * Problem1: "Error: Running AWS SAM projects locally requires Docker. Have you got it installed and running?"
        * Attempt1: Switch to "9p" volumes
        * Attempt2: https://github.com/aws/aws-sam-cli/issues/3595#issuecomment-1575553210
        * Solution: `export DOCKER_HOST="unix://$HOME/.rd/docker.sock"` 
          * https://github.com/aws/aws-toolkit-vscode/issues/3413#issuecomment-1549117147
  * `sam local start-api` | `sam-app` project directory
    * 👀SAM CLI creates 👀
      * local Docker container -- for -- your Lambda function
      * local HTTP server / simulate your API endpoint
    * Problems:
      * Problem1: "Error: Running AWS SAM projects locally requires Docker. Have you got it installed and running?"
        * Attempt1: Switch to "9p" volumes
        * Attempt2: https://github.com/aws/aws-sam-cli/issues/3595#issuecomment-1575553210
        * Solution: `export DOCKER_HOST="unix://$HOME/.rd/docker.sock"`
          * https://github.com/aws/aws-toolkit-vscode/issues/3413#issuecomment-1549117147
      * example output

        ```
          $ sam local start-api
          Initializing the lambda functions containers.
          Local image is up-to-date
          Using local image: public.ecr.aws/lambda/python:3.9-rapid-x86_64.
          
          Mounting /Users/.../Demo/sam-tutorial1/sam-app/.aws-sam/build/HelloWorldFunction as /var/task:ro,delegated inside runtime container
          Containers Initialization is done.
          Mounting HelloWorldFunction at http://127.0.0.1:3000/hello [GET]
          You can now browse to the above endpoints to invoke your functions. You do not need to restart/reload SAM CLI while working on your functions, changes will be reflected instantly/automatically. If you used sam build before running local commands, you will need to re-run sam build for the changes to be picked up. You only need to restart SAM CLI if you update your AWS SAM template
          2023-03-15 14:25:21 WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
            * Running on http://127.0.0.1:3000
          2023-03-15 14:25:21 Press CTRL+C to quit
          ```
    * hit local API endpoint

      ```
      $ curl http://127.0.0.1:3000/hello
      {"message": "hello world"}
      ```

## Step 7: Delete your application from the AWS Cloud<a name="serverless-getting-started-hello-world-delete"></a>

* TODO:
In this step, you use the AWS SAM CLI sam delete command to delete your application from the AWS Cloud\.

**To delete your application from the AWS Cloud**

1. In your command line, from the `sam-app` project directory, run the following:

   ```
   $ sam delete
   ```

1. The AWS SAM CLI will ask you to confirm\. Then, it will delete your application’s Amazon S3 bucket and AWS CloudFormation stack\. The following is an example output:

   ```
   $ sam delete
       Are you sure you want to delete the stack sam-app in the region us-west-2 ? [y/N]: y
       Are you sure you want to delete the folder sam-app in S3 which contains the artifacts? [y/N]: y
       - Deleting S3 object with key c6ce8fa8b5a97dd022ecd006536eb5a4
       - Deleting S3 object with key 5d513a459d062d644f3b7dd0c8b56a2a.template
       - Deleting S3 object with key sam-app/2bebf67c79f6a743cc5312f6dfc1efee.template
       - Deleting S3 object with key sam-app/6b208d0e42ad15d1cee77d967834784b.template
       - Deleting S3 object with key sam-app/da3c598813f1c2151579b73ad788cac8
       - Deleting S3 object with key sam-app/f798cdd93cee188a71d120f14a035b11
       - Deleting Cloudformation stack sam-app
   
   Deleted successfully
   ```

## Troubleshooting<a name="serverless-getting-started-hello-world-troubleshoot"></a>

To troubleshoot the AWS SAM CLI, see [AWS SAM CLI troubleshooting](sam-cli-troubleshooting.md)\.

## Learn more<a name="serverless-getting-started-hello-world-learn"></a>

To continue learning about AWS SAM, see the following resources:
+ **[The Complete AWS SAM Workshop](https://s12d.com/sam-ws-en-intro)** – A workshop designed to teach you many of the major features that AWS SAM provides\.
+ **[ Sessions with SAM](https://www.youtube.com/playlist?list=PLJo-rJlep0ED198FJnTzhIB5Aut_1vDAd)** – Video series created by our AWS Serverless Developer Advocate team on using AWS SAM\.
+ **[Serverless Land](https://serverlessland.com/)** – Site that brings together the latest information, blogs, videos, code, and learning resources for AWS serverless\.