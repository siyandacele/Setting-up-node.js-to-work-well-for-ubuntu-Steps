# Node setup in Ubuntu to allow you to use node cli's packages
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
