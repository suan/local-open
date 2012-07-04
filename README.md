local-open
==========
Do you sometimes open webpages from the terminal, but work in a headless VM or one with some crappy X11 environment? Wish you could just open those webpages in your much nicer base machine browser instead? Then **local-open** is for you! (Yes, this scratches a very specific itch `=P`)

how it works
------------
Simply put, when you provide a URL to **local-open**, it issues the appropriate `open` command on your base machine through SSH.

If the URL is specific to the current machine (contains `localhost` or `127.0.0.1`), a domain name which is reachable from the base machine will be substituted, if possible. **local-open** is also smart enough to know when you're *not* in a SSH session, and will just open the URL on the current machine in those cases.

OSX and Linux is supported.

setup
-----
- Download the `local-open` script and place it somewhere in your `PATH` on the *remote machine*. For example, run this from `~/bin/`:

    ```
    curl -L -O http://github.com/suan/local-open/raw/master/local-open
    ```
- Set the desired config variables, either by `export`ing them in your `.bashrc` or by setting them in a `~/.localopenrc` file. These are the available options:
  - **LOCAL_OPEN_PORT**: Port for the SSH connection to the base machine, e.g. `1999`. *Defaults to 22*
  - **LOCAL_OPEN_HOST**: Host for the SSH connection to the base machine. *Defaults to "localhost"*
  - **LOCAL_OPEN_USER**: Username for the SSH connection to the base machine. *Defaults to the current SSH session's username.*
  - **LOCAL_OPEN_CMD**: The "open" command to use on the base machine, e.g. `"open"` or `"xdg-open"`. *Defaults to "open".*
  - **ALT_LOCALHOST**: The domain name which will replace `localhost` or `127.0.0.1` in URLs. *Defaults to the hostname of the current machine.*
- If your base machine is inaccessible from the remote one, you need to setup a reverse SSH tunnel. The below section has instructions.
- Now, when you do `local-open <URL>`, the URL will be opened on your base machine's browser!
- If you like, you could create an `xdg-open` function in your `.bashrc` or equivalent which calls `local-open` instead for URLs. This would make any program which uses `xdg-open` automatically use `local-open`. (Some people don't like overriding system commands like that so I've left that out)

### setting up a reverse SSH tunnel
If your base machine is inaccessible from the remote one, you need to establish a reverse SSH tunnel from the *base machine* to the remote one. I highly recommend using [AutoSSH][autossh], which will monitor and keep your tunnel alive (available in all package managers and homebrew). You could set such a tunnel to run on startup. To open a reverse tunnel on the remote host's port 1999, with port 1998 as the "monitoring" port:

```
autossh -M 1998 -f -N -R 1999:localhost:22 user@remotehost
```
Without `autossh`:

```
ssh -f -N -R 1999:localhost:22 user@remotehost
```


### fugitive.vim integration ###
To make **local-open** work when you use fugitive.vim commands such as `:Gbrowse`, ensure the following exists in your `~/.gitconfig`:

```
[web]
  browser = local-open
[browser "local-open"]
	cmd = local-open
```

### [hub][hub_link] integration ###
To use **local-open** when you perform commands such as `git browse`, you need to set the `$BROWSER` environment variable. Put this in your `~/.bashrc` or equivalent:

```bash
export BROWSER="local-open"
```

bugs, etc.
----------
Please goto the project page at http://github.com/suan/local-open to report bugs, request features, or if you want to browse the code.



[autossh]: http://www.debianadmin.com/autossh-automatically-restart-ssh-sessions-and-tunnels.html
[hub_link]: https://github.com/defunkt/hub
