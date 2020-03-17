---
layout: post
title:  "Productivity Tools for Working from Home"
date:   2020-03-15 13:00
categories: PhD Study
tags: ["WFH", "Productivity","COVID", "PhD"]
---

These days, because of COVID-19, the best approach to keep up with the deadlines while staying safe is to start working from home. However, many of us are more or less concerned about the negative impact on the productivity. These nice little tools will help your WFH so much easier! 

---

## Connection tools

#### `sshfs` - mount your remote drive locally **(recommend)**

Nice tool to mount the remote drive to a directory of your local computer, so you can access the remote drive as if it is in local. However, from my experience this is slow over the internet, for both navigation and transmission.

#### `scp` or `sftp`- secure file transfer protocols 

Command-line tools for file transmission, if you haven't used `sshfs`. `FileZilla` is an application which does the same thing, but with a graphical interface.

#### `tmux`(linux) or `screen`(mac) - session manager **(recommend)**

After you ssh in, you could start a tmux session by running `tmux`, kick the job to run, and then detach the tmux session by `Ctrl-b d`, and you can log out the ssh session. 

In another ssh session, you can reattach the tmux session by `tmux a -t 0` (suppose your tmux session id is 0, which you can check by running `tmux ls`). Everything in the last ssh session is kept the same, and ideally job has already finished! 

In the old days, you will need to use `nohup &` to set a process as a background process. 

Apart from this, tmux also allow panel management, and you can also open multiple panel, adjust the size and position of each panel, and switch them, with the key of `Ctrl-b`. Everything is just pro.

#### `VNC` - Remote desktop server for Linux systems **(recommend `TigerVNC viewer`)**

In your host, run `x11vnc` or `vncserver` with specified device and port number, you will start a vnc server session. Then in your client, you can use vncviwer to manipulate the screen of the host.

Personally, for the client running locally, I recommend using `TigerVNC viewer` rather than `RealVNC viewer`, because its frame rates are higher (meaning less laggy) , and it can resize the server window to the client window resolution.

In the opensuse system, you could also set up in `YaST -> Remote Administration (VNC) -> Allow Remote Administration with Session Management` to have a complete screen, but potentially will grant the client some unecessary access.

---

## Python Coding tools


I also wanted to add a little bit of how to make our python programming easier.


#### Python package management tool `Anaconda` (recommend)


Combine virtual environment and package management. You can install and manage your python packages without root privilege. When you activate a virtual environment by `source activate <env_name>`, Running `pip install` will directly install the packages in that virtual environment. This is very important especially when you have conflicting python environment setting. 

Also a virtual environment means no risk of messing up! You could even run `conda list --revision` to list your revisions, and rollback to a specific one!


#### `Jupyter noteook` set up remotely **(recommend)**


IPython on browser, with markdown cell, just like writing a notebook. You could set up your local computer to use a remotely run notebook, with the following step.

```sh
(host) $ `jupyter notebook --no-browser --port=8889
localhost:8889/?token=<...>
```

```sh
(client) $ `ssh -N -f -L localhost:8887:localhost:8889 <user>@<host>
```

In the client, you can now access `localhost:8887` in the browser. When asked for the token, just input the one in the first step. 

Ref: https://amber-md.github.io/pytraj/latest/tutorials/remote_jupyter_notebook


#### `IPython kernel` set up remotely


Ipython is a cell-like python interactive session. What I want to mention is that you could open a ipython kernel at the host, and run it in your local machine.

Set up is as follow:

```sh
(host) $ jupyter --runtime-dir
<path_to_jupyter_runtime>
```

Start your ipython kernel, note down the kernel number (in this case 25955).

```sh
(host) $ ipython kernel
[IPKernelApp] To connect another client to this kernel, use:
[IPKernelApp] --existing kernel-25955.json
```

Then use the file transmission tool `scp` to get it locally.

```sh
(client) $ scp <user>@<host>:<path_to_jupyter_runtime>/kernel-25955.json ./
[open client spyder] connecting to an existing kernel
```

And finally run the kernel locally, or to import the kernel in your `Spyder` (I will talk next) with ssh connection.


#### Matlab-like IDE for Python `Spyder`


Spyder is a atlab-like IDE with IPython. You can execute your code cell by cell, and inspect the variable values like Matlab. Support both Qt and inline plotting.

You can also set up your spyder to use the remote kernel. Note that this only work for when you have a simple python script. Let's say you have your current Python script that uses some functions in another file *utils/my_util*:

``` python
from utils.my_util import *
```
This actually means the *utils/my_util* file in your host. So if you change your local *utils/my_util*, it doesn't have any impact on the remote directory, unless you use `sshfs`. I tried using the remote ipython kernel with `sshfs` in `Spyder`, it was very laggy.


#### `X11` set up remotely

My friend also mentioned that Spyder can be launched using X11 forwarding. I haven't tried it yet, but I think it will be useful too!

---

In summary, if you are a graphical user, use `VNC` and `sshfs`. To benefit from the kernel power, use the remote ipython kernel. However at some point you will start to experience a lack of smoothness. Then, it will be time to consider other tools such as `tmux` + `vim` to edit your coding file. Alternatively, you can open a `jupyter notebook` session. 

Hope my article is useful to you. I also welcome more suggestions. Happy WFH! \o/

