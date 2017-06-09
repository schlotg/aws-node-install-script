# aws-node-install-script
This is a script I use to get Node up and running on AWS Linux instances. To use this script create an Amazon Linux instance in your AWS console and then ssh into it. To launch the script type: 'curl -L https://github.com/schlotg/aws-node-install-script/blob/master/install | bash

## n
The installer installs Node via 'n' which makes it really easy to change node versions. For a complete reference on 'n' and it's commands go to: https://github.com/tj/n

## Upstart
A Upstart script is created during the install process which allows your app to start as a service. Upstart will restart the App if it detects it has exited. You can use the following commands with upstart: 
```
  sudo start <app-name>
  sudo stop <app-name>
  sudo restart <app-name>
```  
The Upstart configuration file lives in /etc/init/app-name.conf. The stdout out of your app when running through upstart is piped to /var/log/app-name.log You can watch it with the following command: ```tail -f /var/log/<app-name>.log```

## AWS CodeDeploy
The installer also installs the AWS CodeDeploy Service on the instance to make it easy to deploy. See the AWS documentation for information on how to setup a YML file and deployment scripts. I recommend deploying via CodeDeploy and Github.

## Git Clone
Your App is installed via git clone and the installer asks you to add this instance's ssh keys 
(both public and user), to your git repository. Both keys are used so you can git pull as sudo or as user. The key files are stored on the AWS instance at: /root/.ssh/id_rsa.pub and /home/.ssh/id_rsa.pub

## Running node as a Super Doer (sudo)
In order to run Node or NPM as super doer you will need to add it's binary path to the /etc/sudoers file. The install script gives instructions for this and they are repeated here:
```
  sudo su
    vi /etc/sudoers
    <Press the i key>
    <Find the line 'Defaults    secure_path = '>
    <Type the following at the end of the line> :home/ec2-user/n/bin
    <Type ESC and then :wq! and RETURN>
  exit
```
## Loading Changes into your Shell
After initial installation you can restart the terminal or type: 
  . ~/.bashrc
to load the environment changes into your shell.

## Enviroment Variables
I reccomend keeping all the environment variables that contain account information out of your repository and stored in a controlled secure location. You can then load them into App using the dotenv library. https://github.com/motdotla/dotenv The easiest way I have found to create a .env file on your instance is to:
```
  sudo vi .env
  <Press the i key>
  <Paste them from your clipboard into vi>
  <Type ESC and then :wq! and RETURN>
```

## Final Thoughts
You will still need to install all your project dependencies just like you would on your development machine. Once you have everything installed and running be sure to create an AMI and then base all future deplyments off of this image. Also be sure to ```sudo start <app-name>``` so that the app is up and running before you exit the shell. To debug and instance, typcially you will want to stop the service first and then manually run Node as you would on your development machine.