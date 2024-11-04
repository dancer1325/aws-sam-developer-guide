# Using the AWS SAM CLI<a name="using-sam-cli"></a>

* goal
  * use AWS SAM CLI + AWS SAM templates + supported TP integrations -- for your serverless applications, to --
    * build
    * run

**Topics**
+ [How AWS SAM CLI commands are documented](#using-sam-cli-documentation)
+ [Configuring the AWS SAM CLI](using-sam-cli-configure.md)
+ [Using sam build](using-sam-cli-build.md)
+ [Using sam deploy](using-sam-cli-deploy.md)
+ [Using sam init](using-sam-cli-init.md)
+ [Using sam local](using-sam-cli-local.md)
+ [Using sam sync](using-sam-cli-sync.md)

## How AWS SAM CLI commands are documented<a name="using-sam-cli-documentation"></a>

* syntax | here, to write running AWS SAM CLI commands
  + **Prompt**
    + by default, Linux prompt
      + `$ `
    + if commands / Windows specific
      + `> `
  + **Directory**
    + commands / MUST be executed | specific directory
      + `directoryName $ sam ...`
  + **User input**
  + **Replaceable text**
    + == Variable text / required by commands
  + **Output**
    + == Output / -- returned as a -- response to the command

  * _Example:_

    ```
    $ sam deploy --guided --template template.yaml
    
    Configuring SAM deploy
    ======================
    
        Looking for config file [samconfig.toml] :  Found
        Reading default arguments  :  Success
    
        Setting default arguments for 'sam deploy'
        =========================================
        Stack Name [sam-app]: ENTER
        AWS Region [us-west-2]: ENTER
        #Shows you resources changes to be deployed and require a 'Y' to initiate deploy
        Confirm changes before deploy [y/N]: ENTER
        #SAM needs permission to be able to create roles to connect to the resources in your template
        Allow SAM CLI IAM role creation [Y/n]: ENTER
        #Preserves the state of previously provisioned resources when an operation fails
        Disable rollback [y/N]: ENTER
        HelloWorldFunction may not have authorization defined, Is this okay? [y/N]: y
        Save arguments to configuration file [Y/n]: ENTER
        SAM configuration file [samconfig.toml]: ENTER
        SAM configuration environment [default]: ENTER
    ```
