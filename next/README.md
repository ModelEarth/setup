# Next.js AWS Amplify

## GitHub Copilot

Start by getting the OpenAI driven [GitHub Copilot](https://copilot.github.com/) activated in WebStorm or VS Code.

Check that your [copilot access](https://github.com/features/copilot/signup) is enabled. If not, join the waitlist.

In Webstorm, click Preferences > Plugins and turn on Github Copilot.  

In VS Code, click the Extensions icon on the left and [install](https://github.com/github/copilot-docs) GitHub Copilot. Sign-in to your GitHub account within VS Code using your profile icon in the lower left. Click the notification bell icon in the lower right and agree to the Copilot terms.

Paste the following in a .js page to test that Copilot autocompletes the function:

`function findHighestNumber(array) {`


## Setup the AWS Amplify Framework with NextJS

Select Next.JS (a React framework) when choosing the frontend when [Getting Started with AWS Amplify](https://docs.amplify.aws/start/).  

The [Next.js AWS Amplify setup steps](https://docs.amplify.aws/start/q/integration/next/) will guide you through the following. View each step for additional commands:

Step 1. Run `amplify configure` to create a new IAM user. Enter a new name like "amplify-siteX-user".  Use the same name when prompted a second time.  (Matching the names is not essential, but it's easier when the frontend and backend names match.)

Step 2: `amplify init` and command to install Amplify libraries.

Step 3: Run `amplify add api` to create a backend API and then "amplify push" to deploy everything
To restrict access to portions of the API, configure PRODUCTION-READY [authorization rules](https://docs.amplify.aws/cli/graphql/authorization-rules) on who can create, read, update and delete.


`amplify push` will build all your local backend resources and provision it in the cloud
`amplify status`
`amplify publish` will build all your local backend and frontend resources (if you have hosting category added) and provision it in the cloud (Maybe this is new, didn't see in the AWS setup pages)


Amplyfy CLI Setup - Go to the AWS RDS console and click "Create database".

[Data Modeling](https://docs.amplify.aws/cli/graphql/data-modeling/) -  Amplify automatically creates Amazon DynamoDB database tables for GraphQL



## Uploading Files to S3 Buckets from a Browser

[Event Handlers for Browser Uploads](https://docs.amplify.aws/lib/storage/upload/q/platform/js/#event-handlers)


Allowing external users to securely and directly upload files to Amazon S3 [Details](https://aws.amazon.com/blogs/storage/allowing-external-users-to-securely-and-directly-upload-files-to-amazon-s3/)


[StackOverflow example of securely uploading from Next JS to Amazon S3](https://stackoverflow.com/questions/63525876/how-to-securely-upload-images-to-amazon-s3-from-a-next-js-application)


[Viewing Photos in an Amazon S3 Bucket from a Browser](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/s3-example-photos-view.html)


<!--
## nextjs

The content in the modelearth "nextjs" repository is built from these 
[setup steps](https://vercel.com/guides/nextjs-prisma-postgres) using Node.js, Next.js, 
Prisma, and PostgreSQL (or MySQL) with TypeScript.

## AWS Toolkit - Work with S3 Buckets

[Connect to an AWS Account](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/key-tasks.html#key-tasks-s3)

## Batch upload files to S3 using command

Using AWS Command Line Interface (CLI) to [access Amazon S3](https://aws.amazon.com/getting-started/hands-on/backup-to-s3-cli/)


Goals:

1. Display the content of public folders (from GitHub, AWS S3, Google Drive, Dropbox, OneDrive, etc.)
2. Add display processes that are automatically driven by the file types (image rotation, video display, etc.).

-->