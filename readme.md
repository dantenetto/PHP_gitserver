## PHP gitserver ##

### _What is it?_ ###
This is a simple way to control access to a git repository shared among
many users. It works using ssh and only one user at the server machine (github like)


### Definitions ###
a file for permission must be create with `0600` mode. must be placed at
`$HOME/.git_control/git_user_permission`
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
`$HOME/.git_control/log`
witch will be created if the log is enabled. it is activated by default, but can be deactivated in the gitserver file.



### How to use ###
create the folter .git_control under the home linux user.
the repository are named with the same name of the root folder.
e.g.:
`/home/git/myrepo`
means that to give access to some user in the `.git_user_permission`
the repo key must be "myrepo".

it uses SSH to work. Using the `authorized_keys` to invoke the command.



### Installing it ###
1. copy the gitserver file to /usr/local/bin and make it executable

2. create a directory `.git_control` under the home of the linux user. (it can be configurable in the gitserver file)

3. create a file named `git_user_permission` under this `.git_control`
    folder and change its mode to `0600`.

4. go to `.ssh/authorized_keys` and add the user you want to grant access
    to it, like this example:

   `command="gitserver {USER}",no-port-forwarding,no-X11-forwarding,no-agent-forwarding ssh-[...] user@machine-desktop`

   the `{USER}` must be replaced by the username that will be bound to the key and used in the `.git_user_permission` file.


5- lock the user so it's not allowed to use the password login.
    use the command:
    `passwd -l {linuxuser}`
    so, it will not allow a user to login using ssh without has a key listed in the authorized_keys

any doubt, mail me.
