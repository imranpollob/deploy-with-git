# Auto Deploy Project to Server Using Git

Case Scenario,
- I have a github repo at my local machine
- I have a server
- I want to push my git changes to github as well as to my server
- I want to automate after deployment commands

I have a node project named __node-app__. 
Replace __node-app__ with your __your-project-name__

Steps to follow:
1. __[Local]__ Login to server using SSH. For example: 

    `ssh server_username@server.ip.address`
2. __[Server]:__ Create an empty folder __node-app__
3. __[Server]:__ Create a bare git folder. My folder name is __bare-project.git__. Replace the name with your desired name. For my case the command is 
    
    `git init --bare /path/to/bare-project.git`. 
    
    _Note:_ `.git` at the end is optional, it's just a good convention.
4. __[Server]:__ Create a post-receive Hook file inside the `hooks` folder. For me

    `nano /path/to/bare-project.git/hooks/post-receive`

    Write the following content on the file

    ```sh
    #!/bin/sh

    # Checkout the files
    git --work-tree=/path/to/node-app --git-dir=/path/to/bare-project.git checkout -f
    ```
5. __[Server]:__ Make the `post-receive` file executable

    `chmod +x /path/to/bare-project.git/hooks/post-receive`
6. __[Local]__ On your local project, create a new remote. For my case I selected the name production

    `git remote add production 'server_username@server.ip.address:/path/to/bare-project.git'`
7. __[Local]__ Finally, push to production

    `git push --set-upstream production master`


Now check the files on your server.

To run after deployment commands just edit the `post-receive` file.

```sh
#!/bin/sh

# Checkout the files
git --work-tree=/path/to/node-app --git-dir=/path/to/bare-project.git checkout -f

# install dependencies and run
cd /path/to/node-app
npm install
npm start
```