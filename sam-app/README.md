# sam-app

* == source code + supporting files / serverless application
  - hello_world
    - == Code for the application's Lambda function.
  - events
    - == Invocation events / -- can be used to -- invoke the function.
  - tests
    - Unit tests -- for the -- application code. 
  - template.yaml
    - == template / defines the application's AWS resources
      - Lambda functions & API Gateway API
      - check in ".Resources"

* AWS Toolkit
  * open source plug-in | popular IDEs /
    * uses SAM CLI 
    * has a simplified step-through debugging experience -- for -- Lambda function code
    * supported IDEs
      * [CLion](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [GoLand](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [IntelliJ](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [WebStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [Rider](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [PhpStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [PyCharm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [RubyMine](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [DataGrip](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
      * [VS Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/welcome.html)
      * [Visual Studio](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/welcome.html)

## Deploy the sample application

* required
  * SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
  * [Python 3 installed](https://www.python.org/downloads/)
  * Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

* 
    ```bash
    # package & deploy | AWS 
    sam deploy --guided
    ```

    * requested prompts | deploying the application
      * **Stack Name**
        * -- to deploy to -- CloudFormation
        * requirements
          * unique to your
            * AWS account
            * AWS region
        * recommended
          * match your project name.
      * **AWS Region**
        * | deploy your app to
      * **Confirm changes before deploy**
        * if set to yes -> change sets are shown | BEFORE execution
        * if set to no -> AWS SAM CLI -- will automatically deploy -- application changes.
      * **Allow SAM CLI IAM role creation**
        * create AWS IAM roles / minimum required permissions
          * Reason: 🧠AWS Lambda function(s) -- can access to -- AWS services
        * provide `capabilities=CAPABILITY_IAM`
          * Reason: 🧠 deploy an AWS CloudFormation stack / creates or modifies IAM roles 🧠
          * if it isn't provided through this prompt -> you MUST explicitly pass `--capabilities CAPABILITY_IAM` | `sam deploy` command
      * **Save arguments to samconfig.toml**
        * if set to yes -> your choices will be saved | project's configuration file
          * -> in the future you can just re-run `sam deploy` WITHOUT parameters
    * output
      * API Gateway Endpoint URL
* `sam list endpoints`
  * check the listed endpoints

## Use the SAM CLI to build and test locally

*

    ```bash
    sam-app$ sam build --use-container
    ```

  * installs dependencies / defined in `hello_world/requirements.txt`
  * creates a deployment package,
  * saves it in `.aws-sam/build` folder.
  * Problems
    * Problem1: "Error: Docker is unreachable. Docker needs to be running to build inside a container."
      * Attempt1: Change architecture
      * Solution: TODO:
*

    ```bash
    sam-app$ sam local invoke HelloWorldFunction --event events/event.json
    ```

  * invoke the function directly -- via a -- test event
  * Problems:
    * Problem1: "Error: Running AWS SAM projects locally requires Docker. Have you got it installed and running?"
      * Solution: TODO:
* 

    ```bash
    sam-app$ sam local start-api
    sam-app$ curl http://localhost:3000/
    ```

  * emulate your application's API | port 3000 
  * Problems:
    * Problem1: "Error: Running AWS SAM projects locally requires Docker. Have you got it installed and running?"
        * Solution: TODO:

## Add a resource to your application

* if you want to add resources / NOT included | [the SAM specification](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md) -> use standard [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html) resource types.

## Fetch, tail, and filter Lambda function logs

* `sam logs`
  * 👀works for ALL AWS Lambda functions
    * NOT just ones / deployed -- via -- SAM 👀
* 

    ```bash
    sam-app$ sam logs -n HelloWorldFunction --stack-name "sam-app" --tail
    ```
* see [SAM CLI Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-logging.html).

## Tests

```bash
sam-app$ pip install -r tests/requirements.txt --user
# unit test
sam-app$ python -m pytest tests/unit -v
# integration test, requiring deploying the stack first.
# Create the env variable AWS_SAM_STACK_NAME with the name of the stack we are testing
sam-app$ AWS_SAM_STACK_NAME="sam-app" python -m pytest tests/integration -v
```

## Cleanup

* 
    ```bash
    # delete -- based on -- stackName
    sam delete --stack-name "sam-app"
    ```
  * delete the sample application / you created

## Resources

* see 
  * [AWS SAM developer guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
  * [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/) 
