# Basic Server Setup
I recommend the following steps when setting up 'shop' at a new remote server:

## SSH Keys
Typing your password each time is tedious. Set up some SSH keys and be done with it! You need to set up two SSH keys: 
1. One on your local machine and github (in order to ssh to the remote without password)
2. One on the remote server to access github.

### Creating SSH keys
Follow the instructions [here](https://help.github.com/en/enterprise/2.16/user/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) to create a new SSH key and add it to the ssh-agent.
> Switch the operating system on the top for setting things up on a Mac or Linux server.

### Connecting SSH keys
For github just copy the content of ~/.ssh/id_rsa.pub and add it as new SSH keys in `Settings > SSH and GPG Keys`

To connect to the remote server do `ssh-copy-id -i "user@hostname.example.com` and enter the server password. The next time you log in, you shouldnt be asked for a password anymore.
