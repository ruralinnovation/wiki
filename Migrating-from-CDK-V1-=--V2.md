## Main Changes
 The main changes from AWS CDK v1 to CDK v2 are as follows.
 - AWS CDK v2 consolidates the stable parts of the AWS Construct Library, including the core library, into a single package, `aws-cdk-lib`. Developers no longer need to install additional packages for the individual AWS services they use. This single-package approach also means that you don't have to synchronize the versions of the various CDK library packages.
     L1 (CfnXXXX) constructs, which represent the exact resources available in AWS CloudFormation, are always considered stable and so are included in `aws-cdk-lib`.
 - Experimental modules, where we're still working with the community to develop new [L2 or L3 constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html#constructs_lib), are not included in `aws-cdk-lib`. Instead, they're distributed as individual packages. Experimental packages are named with an `alpha` suffix and a semantic version number. The semantic version number matches the first version of the AWS Construct Library that they're compatible with, also with an `alpha` suffix. Constructs move into `aws-cdk-lib` after being designated stable, permitting the main Construct Library to adhere to strict semantic versioning.
     Stability is specified at the service level. For example, if we begin creating one or more [L2 constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html#constructs_lib) for Amazon AppFlow, which at this writing has only L1 constructs, they first appear in a module named `@aws-cdk/aws-appflow-alpha`. Then, they move to `aws-cdk-lib` when we feel that the new constructs meet the fundamental needs of customers.
     Once a module has been designated stable and incorporated into `aws-cdk-lib`, new APIs are added using the "BetaN" convention described in the next bullet.
     A new version of each experimental module is released with every release of the AWS CDK. For the most part, however, they needn't be kept in sync. You can upgrade `aws-cdk-lib` or the experimental module whenever you want. The exception is that when two or more related experimental modules depend on each other, they must be the same version.
 - For stable modules to which new functionality is being added, new APIs (whether entirely new constructs or new methods or properties on an existing construct) receive a `Beta1` suffix while work is in progress. (Followed by `Beta2`, `Beta3`, and so on when breaking changes are needed.) A version of the API without the suffix is added when the API is designated stable. All methods except the latest (whether beta or final) are then deprecated.
     For example, if we add a new method `grantPower()` to a construct, it initially appears as `grantPowerBeta1()`. If breaking changes are needed (for example, a new required parameter or property), the next version of the method would be named `grantPowerBeta2()`, and so on. When work is complete and the API is finalized, the method `grantPower()` (with no suffix) is added, and the BetaN methods are deprecated.
     All the beta APIs remain in the Construct Library until the next major version (3.0) release, and their signatures will not change. You'll see deprecation warnings if you use them, so you should move to the final version of the API at your earliest convenience. However, no future AWS CDK 2.x releases will break your application.
 - The `Construct` class has been extracted from the AWS CDK into a separate library, along with related types. This is done to support efforts to apply the Construct Programming Model to other domains. If you are writing your own constructs or using related APIs, you must declare the `constructs` module as a dependency and make minor changes to your imports. If you are using advanced features, such as hooking into the CDK app lifecycle, more changes may be needed. For full details, [see the RFC](https://github.com/aws/aws-cdk-rfcs/blob/master/text/0192-remove-constructs-compat.md#release-notes).
 - Deprecated properties, methods, and types in AWS CDK v1.x and its Construct Library have been removed completely from the CDK v2 API. In most supported languages, these APIs produce warnings under v1.x, so you may have already migrated to the replacement APIs. A complete [list of deprecated APIs](https://github.com/aws/aws-cdk/blob/master/DEPRECATED_APIs.md) in CDK v1.x is available on GitHub.
 - Behavior that was gated by feature flags in AWS CDK v1.x is enabled by default in CDK v2. The earlier feature flags are no longer needed, and in most cases they're not supported. A few are still available to let you revert to CDK v1 behavior in very specific circumstances. For more information, see [Updating feature flags](https://docs.aws.amazon.com/cdk/v2/guide/migrating-v2.html#migrating-v2-v1-upgrade-cdk-json).
 - With CDK v2, the environments you deploy into must be bootstrapped using the modern bootstrap stack. The legacy bootstrap stack (the default under v1) is no longer supported. CDK v2 furthermore requires a new version of the modern stack. To upgrade your existing environments, re-bootstrap them. It is no longer necessary to set any feature flags or environment variables to use the modern bootstrap stack.


 ## Migration Steps

 ### Update feature flags in `cdk.json` tp `false`
 - `@aws-cdk/core:enableStackNameDuplicates`
 - `aws-cdk:enableDiffNoFail`
 - `@aws-cdk/aws-ecr-assets:dockerIgnoreSupport`
 - `@aws-cdk/aws-secretsmanager:parseOwnedSecretName`
 - `@aws-cdk/aws-kms:defaultKeyPolicies`
 - `@aws-cdk/aws-s3:grantWriteWithoutAcl`
 - `@aws-cdk/aws-efs:defaultEncryptionAtRest` 

 **Before**
 ```
 {
   "app": "npx ts-node --prefer-ts-exts bin/aws-helper.ts",
   "context": {
     "@aws-cdk/core:newStyleStackSynthesis": false,
     "@aws-cdk/core:stackRelativeExports": false,
     "@aws-cdk/aws-rds:lowercaseDbIdentifier": false,
     "@aws-cdk/aws-apigateway:usagePlanKeyOrderInsensitiveId": false,
     "@aws-cdk/aws-lambda:recognizeVersionProps": false,
     "@aws-cdk/aws-cloudfront:defaultSecurityPolicyTLSv1.2_2021": false
   }
 }
 ```
 **After**
 ```
 {
   "app": "npx ts-node --prefer-ts-exts bin/aws-helper.ts",
   "context": {
     "@aws-cdk/core:newStyleStackSynthesis": false,
     "@aws-cdk/core:stackRelativeExports": false,
     "@aws-cdk/aws-rds:lowercaseDbIdentifier": false,
     "@aws-cdk/aws-apigateway:usagePlanKeyOrderInsensitiveId": false,
     "@aws-cdk/aws-lambda:recognizeVersionProps": false,
     "@aws-cdk/aws-cloudfront:defaultSecurityPolicyTLSv1.2_2021": false
   }
 }
 ```
### Bump to latest version of V1 as a precaution
`npm i @aws-cdk@1.204.0 --save`

 ###  Update your app's dependencies and imports as necessary for the programming language that it's written in.
 Should look something like the following

> NOTE: Some libs could be experimental in CDK V2, which will mean we have to import those alpha packages in separately, like V1.
 ```
 "dependencies": {
   "@aws-cdk/aws-lambda-python-alpha": "^2.0.0-alpha.11",
   "aws-cdk-lib": "^2.0.0",
   "constructs": "^10.0.0"
 }
 ```
 ###  Bump CDK Toolkit
 ````
 npm install -g aws-cdk@latest
 ````
 ### Bootstrap all accounts again
 cdk bootstrap aws://ACCOUNT-NUMBER-1/REGION-1 --profile dev,uat,prod

### Upgrade Python

Window OS
Simply download and install the Python 3.9 installer from the Python download [page](https://www.python.org/downloads/release/python-390/). And you are not sure which version is for you. I would recommend going for Windows x86–64 executable installer version or you can simply click this [link](https://www.python.org/ftp/python/3.9.0/python-3.9.0-amd64.exe).


Recommended Python Installer (Screenshot from the Python Download page)
You may check on the “Add Python to Path” option while installing to register Python3.9 as the main python on your system

`$ python --version`
Python 3.9.0

