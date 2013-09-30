## PHP gitserver ##

### What is it? ###
This is a simple way to control access to a git repository shared among
many users. It works using ssh and only one user at the server machine (github like)


### Definitions ###
a file for permission must be create with `0600` mode. must be placed at
`~/.git_control/git_user_permission`
the syntax is a valid json like the example:
    
    {
        "user": {
            "reponame": "rw",
            "anotherrepo": "r"
        },

        "user2": {
            "reponame": "r"
        }
    }

there is a log folder placed at:
`~/.git_control/log`
which will be created if the log is enabled. it is activated by default, but can be deactivated in the gitserver file.


### How to use ###
the repository is named with the same name of the root folder.
e.g.:
`~/myrepo`
means that to give access to some user in the `.git_user_permission`
the repo key must be "myrepo".

it works using the ssh `authorized_keys` to invoke the command.

to create the repository as a server repository you will need to run the command init with --bare option.
e.g.:

    git init --bare ~/myrepo
    
if your git linux user is locked, as will be explained later, and you are creating the repo using a root user, will be necessary to set the git user as owner of the files:

    chown -hR {gitlinuxuser}:{gitlinuxgroup} /home/{gitlinuxuser}/myrepo
    
    
### Install ###
1. check if the php is installed in the machine running:

            php -v

    if it isn't installed, run:]

            [rpm] yum install php
            [deb] apt-get install php

1. copy the gitserver file to /usr/local/bin and make it executable

        cp gitserver /usr/local/bin/gitserver
        chmod 0755 /usr/local/bin/gitserver

1. create a git user if you haven't done already.

        useradd {gitlinuxuser}
        
1. create a directory `.git_control` under the home of the git linux user. (this path can be configured in the gitserver file)

1. create a file named `git_user_permission` under the `.git_control`
    folder and change its mode to `0600`.

        chmod 0600 git_user_permission

1. go to `.ssh/authorized_keys` and add the user you want to grant access
    to it, like this example:

        command="gitserver {USER}",no-port-forwarding,no-X11-forwarding,no-agent-forwarding ssh-[...]

   the `{USER}` must be replaced by the username that will be bound to the key placed in `ssh-[...]` and will be used in the `.git_user_permission` file

1. lock the user so it's not allowed to use the password login.
    use the command:
    
        passwd -l {gitlinuxuser}
        
    so, it will not allow a user to login using ssh without has a key listed in the authorized_keys

1. to clone the repository in another machine run the command:

        git clone ssh://{gitlinuxuser}@{machine-dns-or-ip}/~/myrepo
        
    or if you need to specify the port:
    
        git clone ssh://{gitlinuxuser}@{machine-dns-or-ip}:{port-number}/~/myrepo
    

any doubt, mail me.
