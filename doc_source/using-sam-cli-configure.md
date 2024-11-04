# Configuring the AWS SAM CLI<a name="using-sam-cli-configure"></a>

* goal
  * configure, for the AWS SAM CLI
    * credentials,
    * basic settings,
    * project settings

**Topics**
+ [Configure credentials and basic settings](#using-sam-cli-configure-basic)
+ [Configure project settings](#using-sam-cli-configure-project)
+ [Configure and use different environments](#using-sam-cli-configure-project-env)
+ [Configure and use parameter values](#using-sam-cli-configure-project-parameter)
+ [Using the AWS SAM CLI with configuration files](#using-sam-cli-configure-cli)

## Configure credentials and basic settings<a name="using-sam-cli-configure-basic"></a>

* basic settings
  * -- is configured via -- AWS CLI
  * _Example:_ AWS credentials, default region name, default output format
  * used by
    * AWS CLI
    * AWS SAM CLI
  * see
    + [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)
    + [Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)
    + [Named profiles for the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)
    + [Using an IAM Identity Center enabled named profile](https://docs.aws.amazon.com/cli/latest/userguide/sso-using-profile.html)
    + [Step 5: Use the AWS CLI to configure AWS credentials](prerequisites.md#prerequisites-configure-credentials)

## Configure project settings<a name="using-sam-cli-configure-project"></a>

* `.toml` file format
  * see [https://toml.io/en/](https://toml.io/en/)
  * `samconfig.toml`
    * default configuration file | your project’s root directory (== location of AWS SAM template file)
  * _Example:_ 

    ```
    ...
    version = 0.1
    
    [default]
    [default.global]
    [default.global.parameters]
    stack_name = "sam-app"
    
    [default.build.parameters]
    cached = true
    parallel = true
    
    [default.deploy.parameters]
    capabilities = "CAPABILITY_IAM"
    confirm_changeset = true
    resolve_s3 = true
    
    [default.sync.parameters]
    watch = true
    
    [default.local_start_api.parameters]
    warm_containers = "EAGER"
    
    [prod]
    [prod.sync]
    [prod.sync.parameters]
    watch = false
    ```

## Configure and use different environments<a name="using-sam-cli-configure-project-env"></a>

* **environment**
  * == named identifier / 👀specified | `samconfig.toml` 👀
  * uses
    * 👀specify a collection of project settings 👀
  * `[default]`
    * default environment
  * if you want to create new environments -> specify a new table header
    * _Example:_ `[prod]`
  + environment name -- should always be the -- first entry | table header
  + `--config-env`
    + option -- to specify the -- environment 
    + _Example:_

      ```
      $ sam build --config-env "prod"
      ```

## Configure and use parameter values<a name="using-sam-cli-configure-project-parameter"></a>

* parameter values / EACH AWS SAM CLI command -- can be -- specified
  + 2@ entry | table header -- identifies the -- AWS SAM CLI command
    + _Example1:_ `[default.build]` -- specifies -- default environment values for the `sam build`
    + _Example2:_ check previous "samconfig.tml"
    + if AWS SAM CLI command includes a subcommand -> use the `_`
      + _Example:_ `sam local invoke`, `invoke` is the subcommand -> `[environment.local_invoke]`
    + if an AWS SAM CLI command or subcommand contains a `-` -> replace it with `_`
      + _Example:_ `sam local start-api`, `start-api` is the subcommand, becomes -> `[environment.local_start_api]`
    + if you want to specify parameter values / AWS SAM CLI -> add the parameter entry | TOML table header
      + _Example:_ `[environment.command.parameters]`\.
    + if you want to specify parameter values / ALL commands -> use `global`
      + _Example:_ `[environment.global.parameters]`
  * configuration entry == TOML key-value pair
    + _Example:_ `watch = false` configuration entry
      + `watch` is the key
      + `false` is the value
    + key
      + == long-form option name -- of the -- AWS SAM CLI command
        + see [AWS SAM CLI command reference list](serverless-sam-cli-command-reference.md)
        + _Example:_ `sam sync --template-file project.yaml` can be specified as 

          ```
          [default.sync.parameters]
          template_file = "project.yaml"
          ```
    + value
      + allowed
        + Boolean values -- `true` or `false`
          + _Example:_ `confirm_changeset = true`
        + 1 parameter as string -> use `" "`
          + _Example:_ `region = "us-west-2"`
        + list of parameter values -> use `"value1 value2 value3 ..."`
          + _Example:_ `capabilities = "CAPABILITY_IAM CAPABILITY_NAMED_IAM"`
        + if value is a list of key-value pairs -> pairs are space-delimited & values are surrounded by encoded `" "` 
          + _Example:_ `tags = "project=\"my-application\" stage=\"production\""`\.
        + if you can specify multiple times -> value is an array of arguments
          + _Example:_ `image_repositories = ["my-function-1=image-repo-1", "my-function-2=image-repo-2"]`\.

* priority of configured values
  + CL 
    + _Example:_ `--parameter-overrides`
  + specific command entries | [configuration project file](#configure-project-settingsa-nameusing-sam-cli-configure-projecta)
  + global entries | [configuration project file](#configure-project-settingsa-nameusing-sam-cli-configure-projecta) 
    + _Example:_ 

      ```
      [default.global.parameters]
      stack_name = "common-stack"
  
      [default.deploy.parameters]
      stack_name = "my-app-stack"
      ```

      `sam deploy` command -- will use the -- stack name `my-app-stack`
  + `Parameters` section | template file

## Using the AWS SAM CLI with configuration files<a name="using-sam-cli-configure-cli"></a>

* scenario
  * commands / provide an interactive flow -- to set -- configuration values
  * _Example:_ `sam deploy --guided`

    ```
    sam-app $ sam deploy --guided
    
    Configuring SAM deploy
    ======================
    
        Looking for config file [samconfig.toml] :  Found
        Reading default arguments  :  Success
    
        Setting default arguments for 'sam deploy'
        =========================================
        Stack Name [sam-app]: ENTER
        AWS Region [us-west-2]: ENTER
        #Shows you resources changes to be deployed and require a 'Y' to initiate deploy
        Confirm changes before deploy [Y/n]: n
        #SAM needs permission to be able to create roles to connect to the resources in your template
        Allow SAM CLI IAM role creation [Y/n]: ENTER
        #Preserves the state of previously provisioned resources when an operation fails
        Disable rollback [y/N]: ENTER
        HelloWorldFunction may not have authorization defined, Is this okay? [y/N]: y
        Save arguments to configuration file [Y/n]: ENTER
        SAM configuration file [samconfig.toml]: ENTER
        SAM configuration environment [default]: ENTER
    ```

* values / 
  * ALREADY exist | ANY of your configuration files -> will be displayed | `[ ]`
  * you provide -- during the -- interactive flow -> 👀AWS SAM CLI writes those values | your configuration files 👀
    * _Example:_ previous example write | your configuration file

        ```
            [default.deploy]
            [default.deploy.parameters]
            capabilities = "CAPABILITY_IAM"
            confirm_changeset = true
            resolve_s3 = true
            s3_bucket = "aws-sam-cli-managed-default-samclisourcebucket-1a4x26zabcde"
            s3_prefix = "sam-app"
            region = "us-west-2"
            ```

    * 👀if the parameter value entered exists | global command table OR specific command table -> AWS SAM does NOT write the value | specific command table 👀
    * 👀if the parameter value entered exists | global command table & specific command table -> AWS SAM deletes the key-value pair | specific command table👀