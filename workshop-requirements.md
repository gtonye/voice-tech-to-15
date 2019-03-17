## Abstract

The following document outlines the requirement for the VTTO #15 workshop for building cross platform application.

## Requirements

#### Notes

For the commands to install the required programs and applications below, an ubuntu machine (18.04.2 LTS) is used as an example. Some adjustements might be required for other platforms. Google or Stackoverflow could be a good place to find equivalents for any given platform.

Ubuntu uses a package manager,`sudo apt update` ensures that the package has the latest version

#### NodeJS

*NodeJS* will be used to do the local development.

All the installers are available at [https://nodejs.org/en/](https://nodejs.org/en/).

On ubuntu we will use `sudo apt install nodejs` to install NodeJS.

[*NPM*](https://www.npmjs.com/get-npm) (node package manager) should be packaged with Node, but in the case of Ubuntu for example it requires an extra `sudo apt install npm`.
To check if npm is already installed you can use `npm -v`

> If you have other projects using specific node version, feel free to install [*NVM*](https://github.com/creationix/nvm) to manage multiple version of Node.

#### IDE

For the workshop we will use [sublime text](https://www.sublimetext.com/) as an editor to write the code and a terminal to run the commands. Most of the code is writtend in javascript ES6, any IDE (WebStorm, visual studio code) can be used. 

#### Git

We will need to checkout code on from the current vtto repository. Git will be required. On Ubuntu git is installed using `sudo apt install git`.

#### Jovo CLI

[`jovo-cli`](https://github.com/jovotech/jovo-cli) will be required to deploy to the Alexa Skill and Google Assistant. On Ubuntu `jovo-cli` is installed using `sudo npm install -g jovo-cli`.


#### Infrastructure

The goal of the workshop is to build an application for Google Assistant and Alexa with mutualized code and infrastructure.

[Jovo](https://www.jovo.tech/) will help to achieve that but a few components in the infrastructure have to be set up.


#### AWS Credentials

The backend, the fulfilment, the endpoint of the webservice will live on an AWS lambda. To use it you will need an AWS account and a user.
- Go to [console.aws.amazon.com](http://console.aws.amazon.com). Create an AWS account.
> We will only use the Free Tier during this workshop, there will be no charges.
- Log in to [console.aws.amazon.com](http://console.aws.amazon.com) after creating the account. Make sure that you are on _N.Virginia_ at the top right near your account name. Under _Services_ on top left, find _IAM_ (type in the search bar) and click on it.
- Follow the steps on [this page](https://developer.amazon.com/docs/smapi/manage-credentials-with-ask-cli.html#create-aws-credentials) to setup a *Policy* (authorization) and create a *user*.
> After creating the user, you should have retrieved *access key ID* and *secret access key*, Save these information in a file.


#### AWS lambda

The code that will be written during the workshop will live on a lambda that the Action and the Skill will interact with.

To create a lambda (See (Here)[https://www.jovo.tech/tutorials/host-google-action-on-lambda#create-a-lambda-function] for detailed steps):
- Login on to [console.aws.amazon.com](http://console.aws.amazon.com). Under services on the top right, find _Lambda_.
- On the new page, click on _Create a function_. Verify that _Author From Scratch_ is checked. You can use *application-endpoint* as a name.
- Select _Create a new role_ on the role selection menu and enter *application-endpoint-role*. In the Policy search for _Basic Lambda_, select the choice and then hit _Create function_.
- Once the function is created. On the left add as a trigger _API Gateway_. It will toogle a configuration menu. Select _Create a New Api_ under _API_ and _Open_ for security. Click on _Add_ then click on _Save_.

> Save the lambda ARN in a file.


#### Alexa skill

> If you already have a skill (and most importantly the *skill ID*), feel free to skip to the next section.

To create an Alexa skill: 
- You will need a [http://developer.amazon.com](developer.amazon.com) account.
- Once created, login and go to [https://developer.amazon.com/alexa](https://developer.amazon.com/alexa)
- Click Your _Alexa Consoles_ and then click _Skills_. This opens the developer console and displays any skills you have already created.
- Click _Create Skill_.
- Choose a _name_ and a _distribution language_ (we can start by setting this to English CA)
- Select _Custom_ as a type of skill and _Provision your own_ for the backend resources.
- After clicking on the _Create Skill_ button, select _Start from scratch_ and press _Choose_.
Skill is created, no need to worry about anything else for now, click on the top left on _Your skills_, and on the next page under the newly created skill click on _View skill ID_.

> Save the Skill ID in a file.


###### ASK cli

In order to interact with the skill, Jovo will use the [Alexa Skill Kit command line interface](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html)
All the [pre-requesite](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html#prerequisites) should be available if you have done the steps above. (Notes for windows users there is an extra `npm install -g --production windows-build-tools`)

- Run in a terminal `npm install -g ask-cli` (on ubuntu you may need to do `sudo npm install -g ask-cli`).
- After the installation, run `ask init`, it will open a page on the browser for you to login using the account your [developer.amazon.com](http://developer.amazon.com) account.
- After logging you can go back to the terminal and say *Yes* to link an amazon account, enter the *ACCESS KEY ID* and the *SECRET KEY* that you stored during the AWS section.

#### Google action

> If you already have an action and the service account credentials for that action, feel free to skip to the next section.

To create the Google Action:
- Create a gmail account if you do not have one already on [http://mail.google.com](http://mail.google.com), then go to [console.dialogflow.com](http://console.dialogflow.com) to create an agent (see [tutorial](https://www.jovo.tech/tutorials/google-action-tutorial-nodejs#create-an-agent-on-dialogflow) for more information).
- Create an agent, you can use a name similar to the one used on Alexa, you just need to remove any space in the name (you can replace them with `-`). Select the language and for Google project you can set it to _Create a new Google project_.
Once the agent is created, we will not worry about changing anything else for now. The next step is to retrieve the service account credentials.

A service account is an email associated with Google projects that allow programatic interactions with the action for any system that has the proper credentials.

To create the service account credentials for the newly created action:
- Make sure that the agent is using the V2 api of dialog flow. See [here](https://www.jovo.tech/tutorials/deploy-dialogflow-agent#step-1:-updating-to-the-dialogflow-v2-api) for walkthrough with screen captures. Go the agent settings and make sure that _API version_ is on V2 (set it otherwise).
- Still on the setting page, click on the service account email, this will lead you to a Google Cloud page with the list of service accounts for the newly creatd project. 
- Click at the top on _Create service account_. You can name it *jovocli* and add *service account for jovo cli* on the description and click on _Create_.
- On the new page, click on Role and search Dialogflow, hover on it and click on _Dialogflow API admin_. Click on _Continue_.
- On the new page click at the bottom on _Create Key_, make sure _JSON_ is selected and click on _Create_. It will ask you to download the credentials key on the machine. Save it on the Desktop.

You now have the Action created and the credentials to interact with it.

- You can now go to [https://console.actions.google.com](https://console.actions.google.com).
- Log in with the gmail account and click on import project.
- Select the name of the agent above
- When asked for a template, select _Build a Customized experience_.
- Under _Quick setup_ add the invocation name and _Save_
- Go back to [http://console.dialogflow.com](http://console.dialogflow.com). Click on _Integration_, _Integration Settings_ and _TEST_.

###### GCloud

In order to programatically interact with the agent, Jovo uses Google Cloud SDK. Intall on the machine following the [appropriate guide](https://cloud.google.com/sdk/docs/quickstarts)

To follow the [Linux quickstart guide](https://cloud.google.com/sdk/docs/quickstart-linux), we install curl using `sudo apt install curl`. 

After installing gcloud cli, we initialize it using `gcloud init`. It will require to authenticate. Login with the gmail account created above and accept the permission request. After logging in, select the project with the name similar to the agent created above.

You are now all set for the interaction with google.

## Sample application

Let's now create a sample application to make sure everything is ready for building our cross platform application.

- Using jovo-cli in a terminal, let's create a new application by running `jovo new sample-application`.
- Open the new sample application folder using _Sublime Text_ (or any IDE). Open the file `project.js`. In this project we will configure all the tools we have set so far.
- See [here](https://www.jovo.tech/docs/project-js#projectjs) for full documentation on project.js. We will add the following:
```
{
    alexaSkill: {
        nlu: {
            name: 'alexa',
        },
        skillId: '<your-skill-id>',
        askProfile: '<your-ask-cli-profile>'
    },
	googleAction: {
		nlu: 'dialogflow',
	    dialogflow: {
	        projectId: '<your-project-id>',
	        keyFile: './path/to/key-file',
	    }
	},
	host: {
	    lambda: {
	        arn: '<your-lambda-arn>',
	        askProfile: '<your-ask-cli-profile>', // if left out: "default" profile is used
	    }
	}
}
```
and replace all the elements that we have previously collected throughout the setup.

- We will also need to update `./models/en-US.json`, to set it to *en-CA* as this is what we set for the skill when creating it.
- We can update the _invocation name_ `./models/en-CA.json`.

You can then run `jovo build` and `jovo deploy`. Once completed you can then run a test in the Alexa simulator and the Google simulator.
