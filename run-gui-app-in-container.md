# Run Gui App Inside Container

List the display service available in the host machine.

```sh
$ systemctl list-units | grep -i 'display'

gdm.service
loaded active running   GNOME Display Manager
```

As in my case it's `gdm`

```sh
 sudo systemctl status gdm
```

Now the main part

```sh
xhost +local:root
```

The `xhost` command is used to manage the access control list for the X Window System (X11) server on Unix-like operating systems. It allows you to grant or revoke access permissions for clients (applications) to connect to the X server.
The specific command `xhost +local:root` performs the following:

`+:` This option adds (grants) an entry to the access control list.
`local:` This keyword specifies that the entry applies to local (Unix domain socket) connections.
`root:` This argument specifies that the entry applies to the "root" user (user ID 0).

So, when you run `xhost +local:root`, you're adding an entry to the X server's access control list that allows the root user (UID 0) to connect to the X server over local Unix domain sockets.

This command is necessary when running GUI applications inside Docker containers because, by default, the Docker container runs with root privileges, and the root user inside the container is not allowed to connect to the X server on the host machine. Running `xhost +local:root` grants the necessary permission for the root user inside the container to access the X server on the host, enabling you to display GUI applications from within the container on your host's desktop.
However, it's important to note that this command loosens the security restrictions on the X server, as it allows the root user (which has elevated privileges) to access the X server. Therefore, it's recommended to use this command cautiously and only when necessary, and to revoke the access granted by running `xhost -` after you're done running the GUI application inside the container.

Now i dot 2 solutions

```sh
docker run -it --env="DISPLAY" --net=host -u=0 -v /tmp/.X11-unix/:/tmp/.X11-unix/ --name=gui-test-nonprev rockylinux:9.3 bash
# or
docker run -it --env="DISPLAY" --net=host --privileged --name=gui-test rockylinux:9.3 bash
```

but the letter part is not recommanded. cause it escalates privilages. whis is not i like.

now inside the container

```sh
dnf install -y firefox

# to run
firefox
```
