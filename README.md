# node-lambda

![node-lambda](../master/node-lambda.png?raw=true)

Command line tool to locally run and deploy your node.js application to [Amazon Lambda](http://aws.amazon.com/lambda/).

[![BuildStatus](https://travis-ci.org/motdotla/node-lambda.svg?branch=master)](https://travis-ci.org/motdotla/node-lambda)
[![NPM version](https://badge.fury.io/js/node-lambda.svg)](http://badge.fury.io/js/node-lambda)

```
$ node-lambda run
```

## Installation

```
$ npm install -g node-lambda
```

## Example App

The [node-lambda-template](https://github.com/RebelMail/node-lambda-template) example app makes it easy to get up and running.

## Usage

There are 4 available commands.

```
$ node-lambda setup
$ node-lambda run
$ node-lambda package
$ node-lambda deploy
```

### Commands

#### setup

Initializes the `event.json`, `context.json`, `.env`, `deploy.env` files, and `event_sources.json` files. `event.json` is where you mock your event. `context.json` is where you can add additional mock data to the context passed to your lambda function. `.env` is where you place your deployment configuration. `deploy.env` has the same format as `.env`, but is used for holding any environment/config variables that you need to be deployed with your code to Lambda but you don't want in version control (e.g. DB connection info). `event_sources.json` is used to set the event source of the Lambda function (Not all event sources available in Lambda are supported).

```
$ node-lambda setup --help

  Usage: setup [options]

  Options:

    -h, --help                     output usage information
```

After running setup, it's a good idea to gitignore the generated `event.json` and `.env` files, as well as `.lambda`.

```
$ echo -e ".env\ndeploy.env\nevent.json\n.lambda" >> .gitignore
```

##### Deploy env variables

```
AWS_ENVIRONMENT            // (default: '')
CONFIG_FILE                // (default: '')
EVENT_SOURCE_FILE          // (default: '')
EXCLUDE_GLOBS              // (default: '')
AWS_ACCESS_KEY_ID          // (default: not set!)
AWS_SECRET_ACCESS_KEY      // (default: not set!)
AWS_PROFILE =              // (default: '')
AWS_SESSION_TOKEN =        // (default: '')
AWS_REGION =               // (default: 'us-east-1,us-west-2,eu-west-1')
AWS_FUNCTION_NAME          // (default: package.json.name or 'UnnamedFunction')
AWS_HANDLER                // (default: 'index.handler')
AWS_ROLE_ARN || AWS_ROLE   // (default: 'missing')
AWS_MEMORY_SIZE            // (default: 128)
AWS_TIMEOUT                // (default: 60)
AWS_RUN_TIMEOUT            // (default: 3)
AWS_DESCRIPTION            // (default: package.json.description or '')
AWS_RUNTIME                // (default: 'nodejs6.10')
AWS_PUBLISH                // (default: false)
AWS_FUNCTION_VERSION       // (default: '')
AWS_VPC_SUBNETS            // (default: '')
AWS_VPC_SECURITY_GROUPS    // (default: '')
AWS_TRACING_CONFIG         // (default: '')
EVENT_FILE                 // (default: 'event.json')
PACKAGE_DIRECTORY          // (default: not set)
CONTEXT_FILE               // (default: 'context.json')
PREBUILT_DIRECTORY         // (default: '')
SRC_DIRECTORY              // (default: '')
DEPLOY_TIMEOUT             // (default: '120000')
DOCKER_IMAGE               // (default: '')
DEPLOY_ZIPFILE             // (default: '')
AWS_DLQ_TARGET_ARN         // (default: not set)
```

#### run

Runs your Amazon Lambda index.js file locally. Passes `event.json` data to the Amazon Lambda event object.

```
$ node-lambda run --help

  Usage: run [options]

  Options:

    -h, --help                          Output usage information
    -H, --handler [index.handler]       Lambda Handler {index.handler}
    -j, --eventFile [event.json]        Event JSON File
    -f, --configFile []                 Path to file holding secret environment variables (e.g. "deploy.env")
    -u, --runtime [nodejs6.10]          Lambda Runtime {nodejs6.10, nodejs4.3}
    -t, --timeout [3]                   Lambda Timeout in seconds (max of 300)
    -x, --contextFile [context.json]    Context JSON file
```

#### package

Bundles your application into a local zip file.

```
$ node-lambda package --help

  Usage: package [options]

  Options:

    -h, --help                          output usage information
    -A, --packageDirectory [build]      Local Package Directory
    -I, --dockerImage []                Docker image for npm install
    -n, --functionName [node-lambda]    Lambda FunctionName
    -H, --handler [index.handler]       Lambda Handler {index.handler}
    -e, --environment [staging]         Choose environment {development, staging, production}
    -x, --excludeGlobs []               Add a space separated list of file(type)s to ignore (e.g. "*.json .env")
    -D, --prebuiltDirectory []          Prebuilt directory

```

#### deploy

Bundles and deploys your application up to Amazon Lambda.

```
$ node-lambda deploy --help

  Usage: deploy [options]

  Options:

    -h, --help                                 output usage information
    -e, --environment [staging]                Choose environment {development, staging, production}
    -a, --accessKey [your_key]                 AWS Access Key
    -s, --secretKey [your_secret]              AWS Secret Key
    -P, --profile [your_profile]               AWS Profile
    -k, --sessionToken [your_token]            AWS Session Token
    -r, --region [us-east-1]                   AWS Region(s)
    -n, --functionName [node-lambda]           Lambda FunctionName
    -H, --handler [index.handler]              Lambda Handler {index.handler}
    -o, --role [your_role]                     Amazon Role ARN
    -m, --memorySize [128]                     Lambda Memory Size
    -t, --timeout [3]                          Lambda Timeout
    -d, --description [missing]                Lambda Description
    -u, --runtime [nodejs6.10]                 Lambda Runtime {nodejs6.10, nodejs4.3}
    -p, --publish [false]                      This boolean parameter can be used to request AWS Lambda to create the Lambda function and publish a version as an atomic operation
    -L, --lambdaVersion [custom-version]       Lambda Version
    -f, --configFile []                        Path to file holding secret environment variables (e.g. "deploy.env")
    -b, --vpcSubnets []                        VPC Subnet ID(s, comma separated list) for your Lambda Function, when using this, the below param is also required
    -g, --vpcSecurityGroups []                 VPC Security Group ID(s, comma separated list) for your Lambda Function, when using this, the above param is also required
    -Q, --deadLetterConfigTargetArn []         Lambda DLQ resource
    -T, --tracingConfig []                     Lambda tracing settings
    -A, --packageDirectory []                  Local package directory
    -I, --dockerImage []                       Docker image for npm install
    -S, --eventSourceFile [event_sources.json] Path to file holding event source mapping variables (e.g. "event_sources.json")
    -x, --excludeGlobs []                      Add a space separated list of file(type)s to ignore (e.g. "*.json .env")
    -D, --prebuiltDirectory []                 Prebuilt directory
    -z, --deployZipfile []                     Deploy zipfile
    -T, --deployTimeout [120000]               Deploy Timeout
    -G, --sourceDirectory [.]                  Path to lambda source Directory (e.g. "./some-lambda")
```

## Custom Environment Variables

AWS Lambda will let you set environment variables for your function. Use the sample `deploy.env` file in combination with the `--configFile` flag to set values which will be added to the lambda configuration upon deploy.  Environment variables will also be set when running locally using the same flag

## Node.js Runtime Configuration

AWS Lambda now supports Node.js 6.10 and Node.js 4.3. Please also check the [Programming Model (Node.js)](http://docs.aws.amazon.com/lambda/latest/dg/programming-model.html) page.

## Post install script
When running `node-lambda deploy` if you need to do some action after `npm install --production` and before deploying to AWS Lambda (e.g. replace some modules with precompiled ones or download some libraries, replace some config file depending on environment) you can create `post_install.sh` script. If the file exists the script will be executed (and output shown after execution) if not it is skipped. Environment string is passed to script as first parameter so you can use it if needed. Make sure that the script is executable.

Example `post_install.sh`:
```
printf "\n\n######  Post install script  ###### \n"
ENV="production";
if [ ! -z $1 ]
  then
    ENV=$1;
fi
cp -v "config_$ENV.js" "config.js" \
&& printf "######  DONE!  ###### \n\n"
```

## Post invoke script (example)
If you wish to invoke your deployed AWS Lambda function, you can add the following as a `script` to your `package.json`:

```
"invoke:remote": "aws lambda invoke --function-name myLambdaFnName --payload fileb://fixtures/hi.json invoked.json --log-type Tail | jq -r '.LogResult' | base64 --decode && rm invoked.json"
```

## Prebuilt packages
The `--prebuiltDirectory` flag is useful for working with Webpack for example. It skips `npm install --production` and `post_install.sh` and simply packages the specified directory.

## Handling `npm link` and Dependencies With Local Paths
Perhaps the easiest way to handle these cases is to bundle the code using Webpack and use the `--prebuiltDirectory` flag to package the output for deployment.

## ScheduleEvents
#### Optional Parameter
When using the eventSourceFile flag (-S or --eventSourceFile) to set a ScheduleEvent trigger, you can pass an optional _ScheduleDescription_ key into the ScheduleEvent object with a custom description for the CloudWatch event rule you are defining. By default, node-lambda generates a _ScheduleDescription_ for you based on the ScheduleName and ScheduleExpression of the rule.

#### Note on ScheduleState for ScheduleEvents
When setting ScheduleState to `ENABLED` or `DISABLED` for ScheduleEvents, it is useful to note that this sets the state of the CloudWatch Event rule but _DOES NOT_ set the state of the trigger for the Lambda function you are deploying; ScheduleEvent triggers are enabled by default in the Lambda console when added using the eventSourceFile flag.

#### Known Issue
###### Duplicate ScheduleEvent Triggers
If you are adding a trigger via the `eventSourceFile` for the first time, remove preexisting triggers from the Lambda console before deploying. Deploying a Lambda with the `--eventSourceFile` flag will *NOT* overwrite the same triggers created from the AWS console and may result in a duplicate triggers for the same rule.

## Other AWS Lambda Tools Projects

+ [lambdaws](https://github.com/mentum/lambdaws)
+ [lambdaws-instant-api](https://github.com/mentum/lambdaws-instant-api)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

### Coding style
This project uses [JavaScript Standard Style](https://standardjs.com/).

[![JavaScript Style Guide](https://cdn.rawgit.com/feross/standard/master/badge.svg)](https://github.com/feross/standard)

## Running tests

```
$ npm install
$ npm test
```
