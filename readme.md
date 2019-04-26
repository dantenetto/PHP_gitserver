## PHP gitserver ##

### What is it? ###
This is a simple script to control the access to a git repository shared among
many users. It works using ssh and a single user at the server machine (github like)


### Definitions ###
A file for permission configuration must be created with `0600` mode, and must be placed at:
`~/.git_control/git_user_permission`

Its syntax is a valid json, like the following example:
    
    {
        "user": {
            "reponame": "rw",
            "anotherrepo": "r"
        },

        "user2": {
            "reponame": "r"
        }
    }

A log folder will be created at:
`~/.git_control/log`

Logs are active by default, but they can be disabled in the gitserver file.


### How to use ###
The repository should be named with the same name of its root directory.
e.g.:
`~/myrepo`
means that to give access to some user in `.git_user_permission`,
the repo key must be "myrepo".

It works by using the ssh `authorized_keys` to invoke the command.

To create the repository as a "server" repository (without a working branch) you'll need to run the command init with `--bare` option.
e.g.:

    git init --bare ~/myrepo
    
If your linux user is locked, as will be explained later, and you're creating the repository using a root user, it'll be necessary to set the git user as owner of the files:

    chown -hR {gitlinuxuser}:{gitlinuxgroup} /home/{gitlinuxuser}/myrepo
    
    
### Install ###
1. Check if php is installed by running:

            php -v

    if it isn't, run:

            [rpm] yum install php
            [deb] apt-get install php
            [mac] brew install php

1. Copy the gitserver file to `/usr/local/bin` and make it executable

        cp gitserver /usr/local/bin/gitserver
        chmod 0755 /usr/local/bin/gitserver

1. Create a linux user, if you haven't done it already

        useradd {gitlinuxuser}
        
1. Create a directory `.git_control` under the home direcotry of the git linux user. (You can configure this path in the gitserver file)

1. Create a file named `git_user_permission` under the previously created `.git_control`
    directory and change its mode to `0600`.

        chmod 0600 git_user_permission

1. Open `.ssh/authorized_keys` and add the user you want to grant access
    to, by adding a command line, like so:

        command="gitserver {USER}",no-port-forwarding,no-X11-forwarding,no-agent-forwarding ssh-[...]

   The `{USER}` must be replaced by the username that will be bound to the key placed in `ssh-[...]`, and should match the username being used inside `.git_user_permission`

1. Lock the linux user to disable password logins,
    by using the command:
    
        passwd -l {gitlinuxuser}
        
    so the system won't allow a user to login using ssh without habing a key listed in `authorized_keys`

### Access ###
1. To clone the repository in another machine run the command:

        git clone ssh://{gitlinuxuser}@{machine-dns-or-ip}/~/myrepo
        
    or if port is required:
    
        git clone ssh://{gitlinuxuser}@{machine-dns-or-ip}:{port-number}/~/myrepo
    

Any doubt, mail me.
