# Accessing Amazon Web Services (AWS)

You're likely here because your coach has given you a set of credentials to access AWS and you want to find out how to use them to get authenticated with AWS. 
You're in the right place!

They will have given you:

- An AWS Access Key ID
- An AWS Secret Access Key

And/or:

- A sign in link, a username and temporary password

Before going any further though, read this important note on working with these credentials safely.

> ⚠️ **IMPORTANT**: **Never** share these credentials with anyone else. **Never** place these credentials directly in your code or upload them to GitHub as part of your codebase. Stolen credentials can lead to costly scenarios and attackers getting access to data they shouldn't have. To mitigate these risks, always make your GitHub repositories **private**. 
>
> **If you ever do suspect that your credentials may have become public or have been uploaded to GitHub (whether to a public or private repo)** delete them from there and let a coach know immediately. 
> Your coach will be able to invalidate your credentials so potential attackers can't use them and issue you with new ones.
> It's important you notify a coach about this even if you have deleted the credentials from the repository already.

Bear the above in mind whenever you work with any credentials or secrets for resources in the Cloud.

## AWS credentials: overview

There are two ways of accessing AWS: 

- via the command line using the AWS CLI (`aws`)
- via the browser using the AWS Console (the UI)

To access AWS via the command line, you will need:

- An AWS Access Key ID
- An AWS Secret Access Key

[If those are the credentials you have, read on here.](#set-up-the-aws-cli)
Once you've done that and if you were also given additional credentials, read on below.

To access AWS via the Console, you will need:

- A sign-in link
- A username
- A temporary password

If those are the credentials you have, head to the sign-in link given to you by your coach and sign-in using your username and temporary password.
The sign-in link will look something like this, where `XXXXXXXXXXX` is a long number:

```
https://XXXXXXXXXXX.signin.aws.amazon.com/console
```

You will be prompted to reset your password.
Do this. 
Store your new password in a safe place, preferably a password manager.
You'll need to use the same sign-in link every time you want to log in to AWS so it's a good idea to bookmark it.


## Set up the AWS CLI

The AWS CLI is a command line tool that allows you to interact with AWS using the terminal.

Run the command below to install it using Homebrew. This will take a while to install (expect somewhere around 15 to 20 min) so it's a good opportunity to take a break and perhaps get a hot beverage 🍵

```shell
brew install awscli
```

Next, configure your credentials using the `aws configure` command:

```shell
aws configure 
```

It will prompt you for

- an AWS Access Key ID: use the value provided by your coach,
- a Secret Access Key: use the value provided by your coach,
- a default region name: use `eu-west-2`,
- a default output format: press enter to leave blank.

Once completed, you should see something like the following in the terminal:

```shell
AWS Access Key ID [None]: ANOTREALACCESSKEYID
AWS Secret Access Key [None]: ANOTREALSECRETACCESSKEY
Default region name [None]: eu-west-2
Default output format [None]: 
```

Press enter to save these changes. 
Now, run the following command to check whether the configuration is correct. 

```
aws sts get-caller-identity
```

If your details are valid, you will see a valid response which includes your username in the payload and looks something like this (the account ID below is an example):

```
{
    "UserId": "MY-USER-ID",
    "Account": "1234567890",
    "Arn": "arn:aws:iam::1234567890:user/yourname"
}
```

This confirms that your AWS CLI has now been set up correctly.

<details>
<summary>Help, I don't get a valid response!</summary>

If something is incorrect with your details, you will see an error such as the one below:

```
An error occurred (InvalidClientTokenId) when calling the GetCallerIdentity operation: The security token included in the request is invalid.
```

Run `aws configure` again and make sure you enter the credentials exactly as they were given to you.
If you still run into trouble or see a different error message, contact your coach.
</details>

When you ran `aws configure`, the command set up a hidden file under `$HOME/.aws/credentials` with your credentials in it (you can open it if you're curious). 
Now, whenever you run `aws` commands from the terminal, it will look up your credentials in this file to authenticate with AWS.

Aside from the AWS CLI, many other tools that you might encounter in your course and that work with AWS behind the scenes can also read credentials from this file.

[For more details, you can visit the official AWS guide on how to install and configure the AWS CLI here.](https://aws.amazon.com/getting-started/guides/setup-environment/module-three/)


<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=pills%2Faccessing_aws.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=pills%2Faccessing_aws.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=pills%2Faccessing_aws.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=pills%2Faccessing_aws.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=pills%2Faccessing_aws.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
