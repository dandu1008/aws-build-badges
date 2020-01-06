# aws-build-badges
Create status-badges and commit-id badges according to CodeBuild/CodePipeline states.<br>
The badges are uploaded to S3 bucket, and then you can link to those badges (SVG files) in your README.md<br>
AWS has some weird limitation where it's not possible to have a build badge when the source is CodePipeline, [it simply doesn't work](https://forums.aws.amazon.com/message.jspa?messageID=867736#867736).

Use the files [README.md.api.html](README.md.api.html) and [README.md.ui.html](README.md.ui.html) to add the proper HTML code to your README.md<br>
*Note*: In case you wonder what's `?dummy=null`, it helps to avoid from letting GitHub cache your images (badges).

![Example](./assets/aws-build-badges-example.png)

## Technology stack
1. [NodeJS 10.x](https://aws.amazon.com/about-aws/whats-new/2019/05/aws_lambda_adds_support_for_node_js_v10/) - AWS Lambda supports this version of NodeJS
1. [AWS SDK for JavaScript](https://aws.amazon.com/sdk-for-node-js/) - Upload status badges to S3 bucket, and get commit-id from pipeline
1. [yarn](https://yarnpkg.com/lang/en/) - package manager (instead of npm)
1. [TypeScript 3.7](https://www.typescriptlang.org/) - targeting ES5
1. [ESLint](https://eslint.org/) - Linting TypeScript (src) and JavaScript (dist) files
1. [Webpack v3.3](https://webpack.js.org/) - Packing and minifying code - `yarn build`
1. [serverless-framework](https://serverless.com/) - Deploying to AWS - `yarn deploy:vault-dev`
1. [Jest](https://jestjs.io/) - for testing - `yarn test`
1. [aws-vault](https://github.com/99designs/aws-vault) - securing AWS credentials

## Installation
1.  Download and install the LTS version of [NodeJS](https://nodejs.org/en/)
1.  Download and install [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
1.  Clone this repo with git
    ```
    (home) $: git clone https://github.com/unfor19/aws-build-badges.git
    (home) $: cd ./aws-build-badges
    (aws-build-badges) $: 
    ```
1.  Download and install [yarn](https://yarnpkg.com/lang/en/docs/install/)
1.  Install [serverless-framework](https://serverless.com/framework/docs/providers/aws/guide/installation/) globally
    ```
    (aws-build-badges) $: yarn global add serverless
    ```
1.  Install application dependencies
    ```
    (aws-build-badges) $: yarn installDeps
    ```
1.  Create a bucket for serverless deployments, and name it: `myapp-badges-deployment-STAGE`,
    where myapp is your app's name
    - For example: `myapp-badges-deployment-prod`
1.  (Optional) Download and install [aws-vault](https://github.com/99designs/aws-vault)

## Configuration
### .env
Copy `env` file to `.env` and modify the relevant variables.

These variables are releavnt only if you intend to run tests or aws-vault.
```
BUCKET_BADGES=myapp-bucket-for-testing # jest
IMAGEPATH=true                         # for tests
VAULT_PROFILE_TESTING=myapp-dev        # aws-vault
VAULT_PROFILE_DEV=myapp-dev            # aws-vault
VAULT_PROFILE_STAGING=myapp-staging    # aws-vault
VAULT_PROFILE_PROD=myapp-prod          # aws-vault
```

### .serverless.vars.yml
Copy `serverless.vars.yml` to `.serverless.vars.yml` and modify the relevant variables. 

Find and replace "myapp" with your application's name. And of course, do whatever changes you need, so it fits your infrastructure.

## Scripts
The scripts reside in the `package.json` file, under the `scripts` section.<br>
All the scripts must run while the present working directory is the project's folder, `aws-build-badges`
```
yarn test:vault          - Run jest using aws-vault profile
yarn test:aws            - Run jest using AWS credentials and profiles
yarn build:dev           - Run webpack for each service, mode=development
yarn build:prod          - Run webpack for each service, mode=production
yarn deploy:vault-STAGE  - Deploy to AWS using aws-vault profile
yarn deploy:aws-STAGE    - Deploy to AWS using AWS credentials and profiles
yarn destroy:vault-STAGE - Destroy stack using aws-vault profile
yarn destroy:aws-STAGE   - Destroy stack using AWS credentials and profiles
```

## Usage
```
(aws-build-badges) $: yarn deploy:vault-prod
yarn run v1.19.1
$ yarn build:prod && export $(cat .env) && aws-vault exec ${VAULT_PROFILE_PROD} -- sls deploy --verbose --stage=prod
$ bash build_services_prod
...
Serverless: Removing old service artifacts from S3...
Serverless: Run the "serverless" command to setup monitoring, troubleshooting and testing.
Done in 193.59s.
```

## Troubleshooting

### yarn test:vault
1. **Issue**: Fails with: `Invalid bucket name`.
   - **Solution**: Set the `BUCKET_BADGES` variable in `.env`
1. **Issue**: Failed to get credentials for jest
   - **Solution**: Set the `VAULT_PROFILE_TESTING` variable in `.env`
1. **Issue**: (node:#####) DeprecationWarning: Tapable.plugin is deprecated. Use new API on `.hooks` instead
   - **Solution**: You can ignore this warning - [details](https://stackoverflow.com/a/49971434/5285732)
## Tested on
<table>
	<thead>
		<th>OS</th>
		<th>Bash</th>
		<th>serverless-framework</th>
		<th>yarn</th>
		<th>AWS SDK</th>
		<th>aws-vault</th>
	</thead>
	<tbody>
		<tr>
	<td>Ubuntu 19.10</td>	
	<td>5.0.3</td>	
	<td>1.56.1</td>			
	<td>1.19.1</td>	
	<td>2.580.0</td>
	<td>4.7.1</td>
	</tr>
		<tr>
	<td>Windows 10</td>	
	<td>TBD</td>	
	<td>TBD</td>			
	<td>TBD</td>	
	<td>TBD</td>
	<td>TBD</td>
	</tr>
		<tr>
	<td>MacOS</td>	
	<td>TBD</td>	
	<td>TBD</td>			
	<td>TBD</td>	
	<td>TBD</td>
	<td>TBD</td>
	</tr>		
   </tbody>
</table>
