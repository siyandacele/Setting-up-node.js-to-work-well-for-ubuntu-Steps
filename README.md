# Let begin with installing node on ubuntu (Documenting my struggle setting up ubuntu)
## Generally speaking, loading arbitrary data from a URL into a root shell session is not a good idea and I wish people would stop peddling it as a solution for everything - "Please just run this script".

As an alternative, here's the "Ubuntu Way" of doing the same, where you can see how the system is being updated and know what repositories and what keys are added to your system configuration:

```
curl https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
sudo apt-add-repository "deb https://deb.nodesource.com/node_13.x $(lsb_release -sc) main"
sudo apt-get update
sudo apt-get install nodejs
This is for the latest (at time of writing) Nodejs version 13. Other versions can also be gotten with a simple change to the repo URL - consult nodesource.com documentation for details.
```

# Node setup in Ubuntu to allow you to use node cli's packages command
Follow this steps if you getting the below message on the Ubuntu terminal when trying to run node cli's commands
```bash
$ ng --help
The program 'ng' is currently not installed. You can install it by typing:
sudo apt install ng-common
```

Path known
Assuming you have a global modules set up for npm (by default ~/.node_modules_global)
$ npm prefix -g

Now set the path of modules/bin to our bash path environment to access over terminal
```bash
$ echo -e "export PATH=$(npm prefix -g)/bin:$PATH" >> ~/.bashrc 
$ source ~/.bashrc
```
###  How about setting a new one!
```bash
$ mkdir ~/path/module
$ npm config set prefix '~/path/module'
$ sudo chown -R $USER <directory>
```
Now the final touch, you can install angular/cli package
```bash
npm install -g @angular/cli
```

NestJs package
```bash
npm i -g @nestjs/cli
```

#### If already installed your cli's use their commands
```bash
ng new new- project
```
```bash
ng new new- project
```
```bash
nest new project-name
```
