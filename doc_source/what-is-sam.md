# What is the AWS Serverless Application Model \(AWS SAM\)?<a name="what-is-sam"></a>

* := toolkit / about serverless applications | AWS, for
  * building
  * running
* == **AWS SAM template specification** + **AWS SAM CLI** 
  1. **AWS SAM template specification**
     * open-source framework / allows
       * defining your serverless application infrastructure | AWS
  2. **AWS SAM CLI**
     * == CL tool / allows
       * build
       * run 
     * uses
       * \+ AWS SAM templates
       * \+ TP integrations
         * _Example:_ Terraform

* see
  * [main concepts](what-is-concepts.md)
  * [Serverless concepts](what-is-concepts.md)

## What is the AWS SAM template specification?<a name="what-is-sam-template"></a>

* := open-source framework /
  * allows
    * about serverless application infrastructure code
      * define
      * manage
  + 👀**Built | AWS CloudFormation** 👀 ->
    + AWS CloudFormation syntax
    + extensive support of
        + resource
        + property configurations
    + 👀AWS CloudFormation will finally deploy the infrastructure 👀
  + **extension of AWS CloudFormation**
    + own unique syntax / -- focuses on -- serverless development
      + BOTH syntaxes are valid
  + **Transformational**
    + 👀== your SAM template -- is transformed, through AWS CloudFormation, to the -- AWS CloudFormation syntax 👀
      + _Example:_ 23 lines of SAM template -- can be transformed to -- 200 lines of AWS Cloudformation
* _Example:_ basic serverless application /
  * processes requests -- to get -- ALL items from a database
  * parts
    * Lambda function
    * HTTP API / communicate between the client & application
    * BBDD to store items\.
    * Permissions for the application -- to -- run securely\.

    ![\[Application architecture of simple serverless application.\]](http://docs.aws.amazon.com/serverless-application-model/latest/developerguide/images/what-is-sam-06.png)

  * sample AWS SAM template

    ```
    AWSTemplateFormatVersion: 2010-09-09
    Transform: AWS::Serverless-2016-10-31
    Resources:
      getAllItemsFunction:
        Type: AWS::Serverless::Function
        Properties:
          Handler: src/get-all-items.getAllItemsHandler
          Runtime: nodejs12.x
          Events:
            Api:
              Type: HttpApi
              Properties:
                Path: /
                Method: GET
        Connectors:
          MyConn:
            Properties:
            Destination:
              Id: SampleTable
              Permissions:
                - Read
      SampleTable:
        Type: AWS::Serverless::SimpleTable
    ```

    * transformed to AWS Cloudformation

    ```
    {
      "AWSTemplateFormatVersion": "2010-09-09",
      "Resources": {
        "getAllItemsFunction": {
          "Type": "AWS::Lambda::Function",
          "Metadata": {
            "SamResourceId": "getAllItemsFunction"
          },
          "Properties": {
            "Code": {
              "S3Bucket": "aws-sam-cli-managed-default-samclisourcebucket-1a4x26zbcdkqr",
              "S3Key": "what-is-app/a6f856abf1b2c4f7488c09b367540b5b"
            },
            "Handler": "src/get-all-items.getAllItemsHandler",
            "Role": {
              "Fn::GetAtt": [
                "getAllItemsFunctionRole",
                "Arn"
              ]
            },
            "Runtime": "nodejs12.x",
            "Tags": [
              {
                "Key": "lambda:createdBy",
                "Value": "SAM"
              }
            ]
          }
        },
        "getAllItemsFunctionRole": {
          "Type": "AWS::IAM::Role",
          "Properties": {
            "AssumeRolePolicyDocument": {
              "Version": "2012-10-17",
              "Statement": [
                {
                  "Action": [
                    "sts:AssumeRole"
                  ],
                  "Effect": "Allow",
                  "Principal": {
                    "Service": [
                      "lambda.amazonaws.com"
                    ]
                  }
                }
              ]
            },
            "ManagedPolicyArns": [
              "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
            ],
            "Tags": [
              {
                "Key": "lambda:createdBy",
                "Value": "SAM"
              }
            ]
          }
        },
        "getAllItemsFunctionApiPermission": {
          "Type": "AWS::Lambda::Permission",
          "Properties": {
            "Action": "lambda:InvokeFunction",
            "FunctionName": {
              "Ref": "getAllItemsFunction"
            },
            "Principal": "apigateway.amazonaws.com",
            "SourceArn": {
              "Fn::Sub": [
                "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${__ApiId__}/${__Stage__}/GET/",
                {
                  "__ApiId__": {
                    "Ref": "ServerlessHttpApi"
                  },
                  "__Stage__": "*"
                }
              ]
            }
          }
        },
        "ServerlessHttpApi": {
          "Type": "AWS::ApiGatewayV2::Api",
          "Properties": {
            "Body": {
              "info": {
                "version": "1.0",
                "title": {
                  "Ref": "AWS::StackName"
                }
              },
              "paths": {
                "/": {
                  "get": {
                    "x-amazon-apigateway-integration": {
                      "httpMethod": "POST",
                      "type": "aws_proxy",
                      "uri": {
                        "Fn::Sub": "arn:${AWS::Partition}:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${getAllItemsFunction.Arn}/invocations"
                      },
                      "payloadFormatVersion": "2.0"
                    },
                    "responses": {}
                  }
                }
              },
              "openapi": "3.0.1",
              "tags": [
                {
                  "name": "httpapi:createdBy",
                  "x-amazon-apigateway-tag-value": "SAM"
                }
              ]
            }
          }
        },
        "ServerlessHttpApiApiGatewayDefaultStage": {
          "Type": "AWS::ApiGatewayV2::Stage",
          "Properties": {
            "ApiId": {
              "Ref": "ServerlessHttpApi"
            },
            "StageName": "$default",
            "Tags": {
              "httpapi:createdBy": "SAM"
            },
            "AutoDeploy": true
          }
        },
        "SampleTable": {
          "Type": "AWS::DynamoDB::Table",
          "Metadata": {
            "SamResourceId": "SampleTable"
          },
          "Properties": {
            "AttributeDefinitions": [
              {
                "AttributeName": "id",
                "AttributeType": "S"
              }
            ],
            "KeySchema": [
              {
                "AttributeName": "id",
                "KeyType": "HASH"
              }
            ],
            "BillingMode": "PAY_PER_REQUEST"
          }
        },
        "getAllItemsFunctionMyConnPolicy": {
          "Type": "AWS::IAM::ManagedPolicy",
          "Metadata": {
            "aws:sam:connectors": {
              "getAllItemsFunctionMyConn": {
                "Source": {
                  "Type": "AWS::Serverless::Function"
                },
                "Destination": {
                  "Type": "AWS::Serverless::SimpleTable"
                }
              }
            }
          },
          "Properties": {
            "PolicyDocument": {
              "Version": "2012-10-17",
              "Statement": [
                {
                  "Effect": "Allow",
                  "Action": [
                    "dynamodb:GetItem",
                    "dynamodb:Query",
                    "dynamodb:Scan",
                    "dynamodb:BatchGetItem",
                    "dynamodb:ConditionCheckItem",
                    "dynamodb:PartiQLSelect"
                  ],
                  "Resource": [
                    {
                      "Fn::GetAtt": [
                        "SampleTable",
                        "Arn"
                      ]
                    },
                    {
                      "Fn::Sub": [
                        "${DestinationArn}/index/*",
                        {
                          "DestinationArn": {
                            "Fn::GetAtt": [
                              "SampleTable",
                              "Arn"
                            ]
                          }
                        }
                      ]
                    }
                  ]
                }
              ]
            },
            "Roles": [
              {
                "Ref": "getAllItemsFunctionRole"
              }
            ]
          }
        }
      }
    }
    ```

## What is the AWS SAM CLI?<a name="what-is-sam-cli"></a>

* == CL tool / allows
  * build
  * run
* uses
  + Quickly initialize a new application project\.
  + Build your application for deployment\.
  + Perform local debugging and testing\.
  + Deploy your application\.
  + Configure CI/CD deployment pipelines\.
  + Monitor and troubleshoot your application | cloud\.
  + Sync local changes -- to the -- cloud | you develop\.

### Initialize a new project<a name="what-is-sam-cli-init"></a>

* `sam init`
  * choose a template -- to begin a -- new project

### Build your application for deployment<a name="what-is-sam-cli-build"></a>

* `sam build`
  * allows
    * 👀preparing the application for deployment 👀
      * package your function dependencies &
        * creating the `.aws-sam`directory
      * organize your project code & folder structure

### Perform local debugging and testing<a name="what-is-sam-cli-local"></a>

* `sam local`
  * allows
    * simulate events,
    * test APIs,
    * invoke functions ->
      * creates a local container,
      * builds our function,
      * invokes it,
      * outputs the results\.

### Deploy your application<a name="what-is-sam-cli-deploy"></a>

* `sam deploy`
  * allows
    * deploy our application -- through an -- 👀interactive flow 👀 /
      * steps
        * choose application's deployment settings
        * AWS SAM teplate -- is transformed to -- AWS CloudFormation
        * finally, deploy to AWS Cloudformation

### Configure CI/CD deployment pipelines<a name="what-is-sam-cli-cicd"></a>

* `sam pipeline init`
  * allows
    * Create secure CI/CD
      * -- via a -- supported CI/CD system

### Monitor and troubleshoot your application in the cloud<a name="what-is-sam-cli-monitor"></a>

* `sam list`
  * allows
    * viewing important information -- about your -- deployed resources
    * gather logs,
    * utilize built-in monitoring tools (_Example:_ AWS X-Ray)
* `sam logs`
  * allows
    * view our function's logs

### Sync local changes to the cloud as you develop<a name="what-is-sam-cli-sync"></a>

* `sam sync`
  * allows
    * watching for local changes
    * detects the change
    * deploys our updates | cloud

## Sum up about benefits -- of using -- AWS SAM<a name="what-is-sam-benefits"></a>

**Define your application infrastructure code quickly, using less code**  
* define your serverless application infrastructure code -- via -- AWS SAM templates
* deploy your templates directly to AWS CloudFormation to provision your resources\.

**Manage your serverless applications -- through -- their entire development lifecycle**  
* entire development lifecycle
  * authoring,
  * building,
  * deploying,
  * testing,
  * monitoring
* see [Using the AWS SAM CLI](using-sam-cli.md)

**Quickly provision permissions between resources -- via -- AWS SAM connectors**  
* AWS SAM connectors | your AWS SAM templates
  * allows
    * defining IAM permissions between your AWS resources 
* see [Managing resource permissions with AWS SAM connectors](managing-permissions-connectors.md)\.

**Continuously sync local changes | cloud as you develop**  
* `sam sync` 
  * sync automatically sync local changes -- to the -- cloud
* see [Using sam sync](using-sam-cli-sync.md)\.

**Manage your Terraform serverless applications**  
* AWS SAM CLI
  * 👀== extension of the AWS CLI 👀
  * allows
    * perform local debugging
    * testing your Lambda
      * functions
      * layers
    * emulating your 
      * application's build environment
      * API 
  * how does it work?
    * run your functions | Amazon Linux environment / matches Lambda
      * -- via -- Docker  
* see [AWS SAM CLI Terraform support](terraform-support.md)\.

## Learn more<a name="what-is-sam-learn"></a>

* see
  + **[Complete AWS SAM Workshop](https://s12d.com/sam-ws-en-intro)**
  + **[ Sessions with SAM](https://www.youtube.com/playlist?list=PLJo-rJlep0ED198FJnTzhIB5Aut_1vDAd)**
  + **[Serverless Land](https://serverlessland.com/)**
