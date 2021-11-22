# Welcome to Kudos #

Here is how you can get started up as part of the dev team. 

# Websites #

By now you should have access and login credentials to the following websites. You should contact your lead if you do not. 

1. [BambooHR](https://kudos.bamboohr.com)

2. [KudosPlatform](https://weare.kudosnow.com/home)

3. [Microsft365](https://login.microsoftonline.com/)

4. [OneLogin](https://kudosinc.onelogin.com/)
    1. To enable OneLogin, you will need to add the beowser extension. After signing up/logging in click on any service and follow the steps that are prompted to add the extension.

5. [Jira](https://kudoswork.atlassian.net/jira/your-work) 

6. [Rollbar](https://rollbar.com/)
    1. To get full access to the teams rollbar, sign-up/sign-in using GitHub (Login/Signup page should have that option)
    2. Contact your lead to grant you access to the team

On all these sites Make sure to update all personal information such as emails, contact info, emergency contacts etc. 



# Required Tools to Download #

1. Download the folllowing Microsoft Office Tools 
    1. Word
    2. PowerPoint
    3. Excel
    4. Outlook (and sign in)
    5. Teams 
Once downloaded make sure you sign in using your Kudos login credentials 

2. Download Desktop version of Zoom 

3. Your favourtie IDE 

## Optional Tools ##

1. Github Desktop 

2. Anything else you want to download so you can better use your laptop (Chrome, Firefox, etc.)

3. Password Manager (Recomeneded) 
    1. Crucial to use strong, unique passwords for everything (1Password)

# Getting Github Access #

In order to get access to the github complete the following steps 

1. Create a new Github account with name Yourname-Kudos
Make sure you sign up using your work email. 

2. Under Profile change your company to @Kudos This can be done by 
    1. Clicking your avater in the top-right corner
    2. Select Profile from toolbar
    3. Scroll down until you see Company 
    4. Click the green Update Profile Button 

3. Contect your lead on teams, who will then add you to the repos 

You will also have to add your RSA Public key to your Github. 

1. Check directory ~/.ssh you should have files called something like id_rsa and id_rsa.pub. If They exist you can move on to step 3, otherwise follow step 2.

2. You can generate your ssh keys by executing the following commands in terminal 
    ```bash 
    $ ssh keygen -t rsa 
    ``` 
    1. Hit enter to accept the defualt location for your public/private keys 
    2. Enter a passphrase. You will be ascked this passphrase again
    3. Re enter passphrase 

3. Copy your public key to clipboard using the following command in terminal.
    ```bash
    $ pbcopy < ~/.ssh/id_rsa.pub
    ```

4. Go to your github and click on your avatar in top left and click Settings

5. Select SSH and GPG keys from the toolbar on the left 

6. Click the green New SSH Key button

7. Paste the key into the key box that pops up and click Add SSH Key when done. 

You can now go to the web-dev repository and follow the steps in the ReadMe to set up your environment locally.

# Issues #

## Initial Build ##

# Prerequisites
- Github account with access to Kudos' repos
- [Docker for Mac](https://www.docker.com/docker-mac)
	* Configure with 2 cpus, 6gb ram (Preferences > Advanced)
- [homebrew](https://brew.sh)
- git

If you are having the issues getting the build to complete whilst running $ ./setup-env.rb follow these steps

1. Restart Docker and re-clone the GitHub repository

2. When running yo web-dev select the following services 
    1. albums-service
    2. apollo-gateway
    3. db
    4. elasticsearch
    5. kong
    6. kudos
    7. localstack x
    8. mailcatcher
    9. pg-db
    10. redis
    11. spaces-service
    12. webpacker x

3. In a code editor delete/comment out all lines that have kudos-bundle-sync in files docker-sync.yml and docker-compose-full.yml
 
4. Rerun the following and the build should work now (this will give you local login credentials)
    ```bash
    $ ./setup-env.rb 
    ```
    
5. in order to have the local host work properly follow the following steps 
    1. run 
    ```bash
    $ sudo echo "weare.lvh.me 127.0.0.1 
    cdn.lvh.me 127.0.0.1" | sudo tee -a /etc/host
    $ sudo killall -HUP mDNSResponder
    ```
    2. after running docker-sync-stack-start and waiting for it to finish, in a seperate terminal run 
    ```bash
    $ docker-compose restart apollo-gateway 
    $ docker-compose restart webpacker
    ```
    Note: This step will need to be done every time you restart the local environment

    3. navigate to http://weare.lvh.me:3000 and sign in using the credentials given after running $ ./setup-env.rb from step 4 

## Webpacker ##

If you are having issues with webpacker (Webpacker exited with code 1) when running docker sync first try the follwing

1. Stop docker sync

2. Run
```bash
docker-compose run --rm kudos bundle install
```

3. Rerun the web-dev build by doing the follwing steps 
```bash
git pull
yo web-dev
docker-sync clean
./setup-env.rb
docker-sync-stack start
```

4. Restart docker sync

If that doesnt work try restarting both docker and your computer. \\
If after all those steps your sync is still broken your LAST resort is to purge docker and redownload all the databases
(this step takes roughly 30-60 minutes, advised to consult a team memeber before doing). \\

To purge docker 

1. Stop docker sync 

2. In docker click the bug icon

3. Select clean/purge data (it will ask you to confirm)

4. Wait for the above step to complete and docker to restart (~1-2 minutes)

5. In terminal restart docker sync (will take 30-45 minutes)
```bash
docker-sync-stack start
```

6. Once that is done in another terminal window run 
```bash
docker-compose run --rm kudos bundle install
docker-compose run --rm kudos yarn install
docker-compose run --rm kudos bundle exec rake db:setup
make kong-setup
docker-compose exec spaces-service npm run db:setup
docker-compose exec tango-rewards npm run db:setup
docker-compose exec campaign-service npm run db:setup
docker-compose exec albums-service npm run db:setup
docker-compose restart webpacker
docker-compose restart apollo-gateway
```

After running all above commands the localhost should once again work

## Pushing to Github 

The following steps will help you push to github 

1. Navigate to the directory for which the fix is for (ex. Kudos-Mobile)

2. Create a branch and navigate to the branch
```bash
git branch KD-<TicketNumber-<mini-description-of-issue>
git checkout branch name
```

3. Add all files you changed 
```bash
git add <path/to/file>
```

4. Commit and push the changes
```bash
git commit -m "KD-<ticketNumber> | Small description of fix"
git push
```
Note if this is your first push on this branch the terminal will log a specifc command to use in order to push

5. On your browser go to github and navigate to the repo. You should see a bar on top that asks to confirm pull request. Click on that

6. You will be asked to fill in some details regarding the fox and assign people to review. Fill in those boxes, confirm everything is done on checklist and click confirm merge. 

# Setting up Local DBs #

## MySQL Workbench ##

In order to test data bases for the local Kudos Web app first ensure that you have mySQLWorkbench downloaded. Then do the following steps 

1. At the top next to MySQL Connections click the + icon to add a new connection

2. Add the follwing information to the respective fields 
```bash
    Hostname: 127.0.0.1
    Port: 3306
    Username: root
```

3. Make sure your docker-sync is running

4. Click on the connection and enter password: root

You should now be connected to the local database

## Postico ##

Our servers also make use of PostgreSQL in order to set up a local host of the database please download Postico and do the follwoing steps

1. Add a new connection (opening postico for first time should automatically start this process)

2. Add the follwing information to the respective fields 
```bash
    Host: localhost
    Port: 5432
    User: postgres
    Password: postgres
    Database: kudos_space
```

You can now connect to the PostgreSQL databases

# Setting up Staging #

To set up staging of the web-app go to OneLogin and click staging. You may need permission if you dont have it, if this is the case contact your direct head and they should be able to provode this from you. 

To set up staging for the DB, you will need to insatll the following tools 

1. SQLWorkbench

2. a VPN 

For this, you will need access to redash. In Jira, create a ticket under IT requesting access to redash and SRE will provide that for you. 
Once you have access go this [link](https://d-9d672a8ff2.awsapps.com/start/#/) and click staging VPN start here and download/install the correct Client based on your OS. Also download the endpoint file. 
After installing you will be met with a popup asking for display name (any name as this is just an alias) and the configuration file which is what you downlaoded earlier. After this you will be able to start the VPN. 

3. Follow the instructions [here](https://kudosinc.onelogin.com/notes/129809)

4. Install git-crypt by running 
```bash
brew install git-crypt 
```

5. navigate to kudos (web app) directory and execute 
```bash
git crypt unlock 
```

6. run from the kudos directory
```bash
cat /charts/staging-secrets.yaml
```

7. decode the output from base64 by running
```bash
echo <base64 string> | base64 --decode 
```

8. Open up mySQL workbench and click new connection and set the fields as follows 
```bash
Hostname: web-db-rds.csu8kaorkmk1.ca-central-1.rds.amazonaws.com
Port: 3306
Username: kudos
Default Schema: kudos_staging
```

Hit enter and click on the connection. Enter the password that was deocded from base64 and you can now use the DB on staging. 