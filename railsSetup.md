These instructions assume that you have an editor.  [VSCode](https://code.visualstudio.com/) is strongly recommended.

We will be using the latest version of Ruby and Rails. As of this writing, those are Ruby 3.2.2 and Rails 7.0.4.2 -- but any current version of Ruby and Rails 7 should be fine.

In the next sections, there are specific descriptions on how to set up your machine. Depending on your specific machine configuration, there may be differences in what you need to do.  If you have trouble, ask via Slack on how to get around it.

There are specific steps for installation on

*   Linux Ubuntu
*   Mac OSX
*   Windows 10/11 64bit (you must have at least 8G of memory, and you must have administrator access)

If you do not have a machine of this description — for example, if your Windows machine only has 4G of memory — you will not be able to do local Rails development on your machine. Put a message in Slack and we will consider alternatives.

Windows Setup
-------------

It is not possible to do Rails development in Windows native. Therefore you need to configure the Windows Subsystem for Linux (WSL). If you already have WSL configured, go to the section below on how to configure the Linux environment for Rails development. WSL setup has quite a few steps. (You must be logged into Windows as an administrator account in order to perform them. You can check by going into control panel > user accounts > user accounts. If you do not see “administrator” underneath your account, you must change your account type to administrator and then reboot the machine before trying to install WSL.) Before you start, you should install the following programs in Windows (not in WSL): [Visual Studio Code](https://code.visualstudio.com/) and [Postman](https://www.postman.com/downloads/). Then do the following:

1.  Make sure virtualization is enabled on the machine. Press Ctrl-Shift-Esc. This brings up the task manager. Then click on the performance tab (it might not be obvious which tab, but it has a little graph icon). You should then see a page with a line that says Virtualization: Enabled. If you do not see this line, you need to enable virtualization. (Some few older machines don’t support virtualization at all.)
2.  If you need to enable virtualization, you need to get into the Bios settings. The way you do this depends on the machine model and on the level of Windows installed. For Windows 10, you power off the machine and power it back on pressing F1 or F10 or something as it comes up (the key you use depends on the manufacturer). For Windows 11, you go into Settings, System, Recovery, Advanced Startup. This combination brings up the Bios. Then you need to find your way through the Bios settings to enable virtualization. Different manufacturers organize the Bios settings differently. Anyway, once you find it and turn it on, save and exit and you will be back in Windows. You should verify that virtualization now shows as enabled in the task manager.
3.  Now click on the Windows icon and find the Powershell program. Select Run as Administrator to open your Powershell session. Then type:  
    ```
    wsl --install
    ``` 
    This will run for a while, and in addition to WSL itself, it will install Linux. By default, it installs a current level of Ubuntu, which is fine. I don’t remember if you have to restart Windows.
4.  Now click on the Windows icon and find Ubuntu. Run that app. When it is running, you should pin it to the task bar, because you will need it frequently.
5.  Go back to your Windows and install the [VisualStudio Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).
6.  Go back to your WSL Linux session. Do the command **whoami** to see if you are running as root.
7.  If the whoami command does not return “root”, that’s good. You have to do one more check. Do a `sudo su -` That should prompt you for a password and then take you to a root session. If it does, good! You can type exit to get out of the root session and your Ubuntu is set up! You can skip steps 8-12. If the sudo command does not work (this should be rare) ask for help via slack.
8.  If the whoami returns “root”: Hmm, I’m not sure why this happens, but you don’t want to do development while running as root, so there are some additional steps necessary.  Enter the following commands:
    ``` 
    cd /root  
    code test.txt
    ``` 
    This is to test that your vscode editor comes up correctly. You should also start a terminal from within VsCode to make sure that works. If all works, you have a functional editor, so you can close the file without saving it.
9.  You need to create the Linux user account that you will use for development. The following commands require administrator authority, but because you are running as root, you don’t need to do sudo. Enter the following, substituting in the name you want to use. I used john in this example: 
    ``` 
    useradd -m -s "/bin/bash" john  
    passwd john
    ```  
    You will be prompted to enter a password. You should make it secure, but you should also be sure you can remember it as you will have to type it from time to time.
10. You now need to give the new user the ability to do administrative commands with sudo. Enter the following commands:   
    ```
    cd /etc  
    code sudoers
    ``` 
    That will bring up your vscode editor with the sudoers file. Be careful with this file! You will find a line
    ```  
    root ALL=(ALL:ALL) ALL
    ```  
    Under it you should add a line  
    ```
    john ALL=(ALL:ALL) ALL
    ```
    except of course you use the username you created. Then save and close the file.
11. Now you need to configure your distribution so that the user you created is the default user for Linux sessions. Type the following commands:  
    ```
    touch wsl.conf  
    code wsl.conf
    ``` 
    You are now editing the file wsl.conf in the /etc directory. Be careful with this file! Put in the following lines: 
    ``` 
    [user]  
    default=john
    ```  
    substituting the name of the user you created, and save and close the file.
12. Now you type exit to end your Ubuntu terminal session. Bring up a powershell session as an administrator and type  
    ```  
    wsl --shutdown
    ```
    Wait a while. (I don’t know why, but it seems like it takes WSL a while to notice that the configuration has changed.) Then start a new Ubuntu terminal session. You should see that you are logged on as the user you created. You should be in the home directory for that user. This is where you’ll put your code.

After this point, you just do the same setup instructions for your development environment as you would if it were ordinary Linux — see the Linux section below. Some users have noticed a problem that their Windows executable programs can stand in front of the Linux ones. There are tweaks to fix this. Ask a question in Slack. One way to move files from your Windows environment to/from your WSL Ubuntu environment is to use the file manager. You’ll find a Linux/Ubuntu folder there. If you prefer to copy files using the command line, you can do, in Powershell or a command prompt:
```
net use z: \wsl$\ubuntu
```
and then you can copy files to/from directories on that z virtual disk. Another way is to use your Linux terminal. Your Windows files are accessible from within the terminal at the directory /mnt/c . Usually you will not need to move your code to Windows.

**Do all git operations from the Linux command line, or from the Linux terminal session within vscode.**

## Linux Setup on Ubuntu

This information is for Ubuntu.  The same steps can be done on other Linux distributions, but you have to use different installation tools. If you are running Linux with a graphical user interface (i.e. not as part of the Windows Subsystem for Linux) you need to install Virtual Studio Code and Postman.

First, install rbenv. At a terminal command line, enter the following commands:

```
sudo apt-get update
sudo apt-get install rbenv
echo 'eval "$(~/.rbenv/bin/rbenv init - bash)"' >> ~/.bashrc
```
Then close your terminal window and open a new one. Then type these commands. The sudo commands prompt you for a password.
```
sudo apt-get install libffi-dev
sudo apt-get install libyaml-dev
git -C "$(rbenv root)"/plugins/ruby-build pull
```
Sometimes it seems that the git command above will return a directory not found error. If it does, substitute the following command:
```
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```
Then do:
```
rbenv install 3.2.2
rbenv rehash
rbenv global 3.2.2
```
Then verify your installation with the following command.
```
ruby --version
```
The last command should return ruby 3.2.2 with some other things following. If you do not see this **STOP**. You do not have ruby or rbenv installed correctly. Go back and retry the previous steps, or put a message in the slack channel.

Then enter the following commands:
```
gem install bundler
gem install rails -v '~> 7.0'
rbenv rehash
sudo apt-get install curl
```
Note that you may need to install a slightly different version of Rails, because the supported versions change from time to time.

### Optional Components

With the upgrade to Rails 7, it seems that it is no longer necessary to install yarn. If you need to install yarn for some reason, the command is:
```
sudo apt-get install yarn
```
With the upgrade to Rails 7, it seems that it is no longer necessary to install Node.js, although you may elect to do so. Node.js installation is often done on Linux using nvm. To install nvm and node, enter the following commands (the curl command is one long line ending with bash):
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
source ~/.profile 
nvm install --lts
```
If you get a command not found, close your terminal session, open a new one, and retry the command. Verify your node installation using:
```
node --version
```
### Testing the Rails Installation

Verify the Rails installation by creating a sample rails server. Enter the following command:
```
rails new sample1
```
This will run for quite some time, and will create a directory called sample1 with many files and folders. Then type the following commands:
```
cd sample1
bin/rails server
```
This will start the server. You can now verify that the server is running by going this link: http://localhost:3000 . You should see Rails logo and a report of the version of Rails and Ruby you are running. If you don’t get this far, put a message in the slack channel. If you do get this far, go back to your command line and press Ctrl-C to stop the server.

The git program is part of Linux, but you should do some configuration, with these commands (substituting your name and email address):
```
git config --global credential.helper store
git config --global user.name "John Q. Public"
git config --global user.email "johnq@sample.com"
git config --global init.defaultBranch main
```
Mac OSX Setup
-------------

Macs come in two flavors, those with the Intel chip and those with the Apple (M1) chip. Check to see which one you have by clicking the Apple icon and selecting “About this Mac”. On newer Macs, the chip type is displayed in the center of the screen. On older Macs, you can find this information by clicking “Overview”. If it says Apple instead of Intel, you have the M1 chip. Now, there is a lot of information on the net that says that you must run an emulator called Rosetta in order to run Ruby and Rails on the M1 chip. **This is no longer true!** Don’t use Rosetta. On both the M1 and Intel Macs, you follow the same steps:

1.  Install Xcode-select, as follows. Open a terminal session. (If you are on a Mac with the M1 chip, type “arch” and press enter. You should see “arm64”. If not, you are running Rosetta, and you need to turn that off before proceeding. If you have the Intel chip, don’t worry about this.) Then type: You will be prompted to enter your password. Note: Sometimes after upgrading OSX, you have to repeat this step to reinstall Xcode-select.
    ```  
    xcode-select --install
    ```
2.  Install Homebrew. First check if you already have it with “which brew”.
    *   If you see a ‘not found’ error, do the following to install the latest version of Homebrew:
        ```  
        /bin/bash -c "$(curl -fsSL href="https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
        ```
        If you are prompted for your password, enter it. Then type:
        ```         
        echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile 
        eval "$(/opt/homebrew/bin/brew shellenv)"
        brew doctor 
        ```        
        You should see “Your system is ready to brew.”
    *   If, instead of the not found error, you see a file path, check to see that it matches your processor. For Intel processors, the filepath returned by “which brew” should start with “/usr/local”. If you have an Apple M1 processor, the filepath should start with “/opt/homebrew”. If your processor and install location don’t match up follow these instructions to uninstall: [https://github.com/homebrew/install#uninstall-homebrew](https://github.com/homebrew/install#uninstall-homebrew) and start step 2 over.
3.  Install rbenv. This manages Ruby and the associated gems, including Rails. Type:
    ```    
    brew install rbenv
    touch .zshrc
    echo 'eval "$(rbenv init -)"' >> ~/.zshrc
    eval "$(rbenv init -)"
    brew upgrade ruby-build
    ```
4.  Install Ruby. Type:
    ```
    rbenv install 3.2.1
    rbenv rehash
    rbenv global 3.2.1
    ```
5.  Install Node, as described here: [https://nodejs.org/en/](https://nodejs.org/en/) You should install the LTS version.
6.  Install yarn, with brew install yarn.
7.  Install Rails, as follows:
    ```
    gem install rails -v '~> 7.0'
    rbenv rehash
    ```
8.  Configure git, using the following commands (but substituting your name and email address):
    ```
    git config --global credential.helper osxkeychain
    git config --global user.name "John Q. Public"
    git config --global user.email "johnq@sample.com"
    git config --global init.defaultBranch main
    ```
Now, verify the Rails installation by creating a sample rails server. Enter the following command:
    ```
    rails new sample1
    ```
This will run for quite some time, and will create a directory called sample1 with many files and folders. Then type the following commands:
    ```
    cd sample1
    bin/rails server
    ```
This will start the server. You can now verify that the server is running by going to this link: [http://localhost:3000](http://localhost:3000) . You should see a Rails logo and information about the version of Rails and Ruby you are running. If you don’t get this far, put a message in the slack channel. If you do get this far, go back to your command line and press Ctrl-C to stop the server.

You should also install [Postman.](https://www.postman.com/downloads/)
