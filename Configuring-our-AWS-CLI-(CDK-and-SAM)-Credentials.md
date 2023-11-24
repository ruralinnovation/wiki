The following has been extracted from AWS Docs (with added info for more clarity and context):
[CLI Configure profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)

# Prerequisite Steps

## New User

1. Create an IAM user with **Programmatic Access**

![image](https://user-images.githubusercontent.com/47429329/179747360-8ace1c90-c720-4dfb-9eeb-05b3208fbc7b.png)

2. Specify permissions and tags (keep in mind the permissions will control the CLI commands you can run)
3. Download the CSV file with the User Credentials and store it locally.

![image](https://user-images.githubusercontent.com/47429329/179747919-6acbd36a-b774-4bed-aa13-3055a861e217.png)

4.  You will use the Access Key ID and Secret Access Key in the following sections.

## Existing User

1. Open the AWS Console and navigate to the IAM service.
2. Click Users and select the existing User
3. On the User screen, click **Security credentials**

![image](https://user-images.githubusercontent.com/47429329/179748462-7c8b3fa6-7628-41bc-b8da-c968a62a406b.png)

4. Click **Create Access Key** button in the Access Keys section

![image](https://user-images.githubusercontent.com/47429329/179748622-b6e8e398-6a43-4504-8099-8119d01e4dea.png)

5. Download the CSV file with the User credentials and store it locally

![image](https://user-images.githubusercontent.com/47429329/179748802-6c9b6f74-9598-4103-a7da-6b24984fd0d5.png)

6. You will use the Access Key ID and Secret Access Key in the following sections.

# Named Profiles

A named profile is a collection of settings and credentials that you can apply to a AWS CLI command (also used for CDK and SAM, as they leverage CloudFormation). When you specify a profile to run a command, the settings and credentials are used to run that command. Multiple named profiles can be stored in the config and credentials files.

>You can specify one default profile that is used when no profile is explicitly referenced. Other profiles have names that you can specify as >a parameter on the command line for individual commands. Alternatively, you can specify a profile in the 
>[AWS_PROFILE](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)
> environment variable which overrides the default profile for >commands that run in that session.

# Creating Named Profiles

You can configure additional profiles by using [aws configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html#cli-configure-files-methods) with the --profile option, or by manually adding entries to the config and credentials files. For more information on the config and credentials files, see [Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html).

## Credentials Profile

The following example shows a credentials file with two profiles. The first [default] is used when you run a AWS CLI command with no profile. The second is used when you run a AWS CLI command with the --profile user1 parameter.

The credentials file uses a different naming format than the AWS CLI config file for named profiles. Do not use the word profile when creating an entry in the credentials file.

**`~/.aws/credentials`** (Linux & Mac) or **`%USERPROFILE%\.aws\credentials`** (Windows)

```text
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[user1]
aws_access_key_id=AKIAI44QH8DHBEXAMPLE
aws_secret_access_key=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
```

## Config Profile

Each profile can specify different credentials—perhaps from different IAM users—and can also specify different AWS Regions and output formats. When naming the profile in a config file, include the prefix word "profile".

The following example specifies Region and output information for the default and user1 profiles.

**`~/.aws/config`** (Linux & Mac) or **`%USERPROFILE%\.aws\config`** (Windows)

```text
[default]
region=us-west-2
output=json

[profile user1]
region=us-east-1
output=text
```