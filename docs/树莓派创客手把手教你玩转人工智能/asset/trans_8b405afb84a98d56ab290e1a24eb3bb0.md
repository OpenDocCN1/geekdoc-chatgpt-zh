
If you want to access the Raspberry Pi over the internet, you need a public IP address. Generally, if you are connecting via an `ADSL` router, you might not have a public IP. In this case, you might need an internal network penetration service like Peanut Shell (Hsk) (detailed operations can be found on the Peanut Shell official website: `https://hsk.oray.com/`).

Remote file copying and sharing with the Raspberry Pi is also very simple; just use the `scp` command:

```
scp pi@192.168.0.2:/home/pi/testfile1 .
```

This command means copying a file from the Raspberry Pi to the current directory. When using `SCP`, you can also use wildcards and the `-r` option together; the latter allows you to recursively copy all items within a folder.

> **Note**
>
> Using `FTP` is no longer secure; it is recommended not to use it.

## 2.1.5 Shared Folders

`samba` has many customizable options. If you just want it to work properly, you only need to add your workgroup name to the default settings, as shown in Figure 2-5.

![](EPUB/images/image_021.jpg)

**Figure 2-5**

You might think you need a service like Baidu Netdisk to share files between different machines, but this is unnecessary within a LAN. The `samba` service allows a `Windows` computer to access the Raspberry Pi as a shared device. You can install `samba` with the following command:

```
$ sudo apt-get install samba samba-common-bin
```

Now edit the `/etc/samba/smb.conf` file and modify the `workgroup` variable (set to `WORKGROUP` for versions below `Windows 7`, and `HOME` for `Windows 7` and above). Next, you need to ensure `pi` is a network user:

```
$ sudo smbpasswd -a pi
```

Then restart the service:

```
$ sudo service samba restart
```

You should now be able to see the Raspberry Pi in Windows File Explorer. Now modify the `samba` configuration file to adjust shared devices and permissions:

```
$ sudo nano /etc/samba/smb.conf
```

Jump to the last line and add the following content:

![](EPUB/images/image_022.jpg)

Then restart the service:

```
$ sudo service samba restart
```

At this point, you can access, read, and write to the shared folders.

## 2.2 Process Management

The `top` command displays the `CPU` and memory usage of currently running programs. You can manually suspend a command running in the command line, or use the `fg` command to resume it, as shown in Figure 2-6.

![](EPUB/images/image_023.jpg)

**Figure 2-6**

Sometimes you may feel a running program has frozen and does not respond to your input, including the quit key `Q`. At this point, it seems only `Ctrl+C` can close it. This section deals with "crashes"; you don't need to reboot at all—you can simply close the corresponding process in the background with a few commands.

### 2.2.1 Displaying Processes

You can view processes running on the system using the `ps` command. The `ax` options display all processes for all users; the `u` option specifies displaying processes for a specific user; `w` and `ww` configure the output format.

Try `ps auxww` and other combinations yourself. You will notice there is no "-" before the command's options because this command has been carried over from early `UNIX` `ps`. Other `UNIX` branches have improved the `ps` command, so you might see commands like `ps -e`.

The list returned by `ps aux` has many columns, including username, `PID`, etc., where the first process `init` has a `PID` of `1`.

The `kill` command we will learn next is related to `PID`. If you want to kill a program and know its `PID` is `3012`, just type `kill 3012`. To quickly find a program's `PID`, say the `vi` tool's `PID`, type:

```
$ ps aux | grep -i vi
```

Generally, the `-i` option is not required, but adding it ensures you are not disturbed by case sensitivity issues. The `killall` command followed by the program name, e.g., `killall firefox`, can also close a process.

Now let's look at a slightly more complex example:

```
kill $(ps aux | grep '\[f\]irefox' | awk '{print $2}')
```

Let's talk about `awk` first. We won't go deep into it here; you just need to know it prints the second column of the output, which is the `PID`; additionally, the `\[f\]` trick prevents the `grep` process itself from being included in the output.

### 2.2.2 Modern Process Managers

The output of the `ps` command also tells you some useful information, such as memory and `CPU` usage. Besides `ps`, the `top` command displays real-time changing hardware usage; the `free` command has similar functions, displaying available memory, etc.; `htop` is an enhanced version of `top`, which can be installed and used via `apt`:

```
$ sudo apt-get install htop
```

Of course, the current `Raspbian` system already has the `htop` command built-in.

You can use the mouse wheel to scroll up, down, left, and right through the `htop` output information, and even use the `k` command directly in this interface to kill certain processes. Like the `top` command, you can use the `Q` key to exit this interface. On multi-core processors, `htop` also displays the usage of each core, as shown in Figure 2-7.

![](EPUB/images/image_024.jpg)

**Figure 2-7**

`htop` not only displays processes and their corresponding usage rates but also allows you to interact with it.

### 2.2.3 Background Tasks

Adding an `&` symbol after a command allows it to run in the background. Try `man top &`, and you will get output similar to `[1] 12768`. The first number is the job number, automatically assigned by the terminal; the second is the `PID`. Now `man top` is running in the background; you can interact with the background task in the command line via the job number. Try opening another task in the background, then type `fg 1`, and you will see the `man` command running again.

For a task, you can use `Ctrl+Z` to suspend it. The `fg` command without parameters always executes the most recently suspended task or background task (note the difference between the two). The job number assigned just now is only valid in this terminal; you can see background tasks by typing `jobs`, and `jobs -l` will also output the `PID`s of these tasks.

`nohup` runs a program in the background, specifically allowing the program to continue executing after the terminal exits. After the terminal exits, the program's output and error messages are saved to the `nohup.out` file (in the directory where you ran the process). This brings us back to the redirection we learned earlier. You can redirect output to another file; `1>` redirects to `stdout`, `2>` redirects to `stderr`, and `&>` redirects to both `stdout` and `stderr`:

```
$ nohup myprog &>backgroundoutput.txt &
```

A common use case for `nohup` is related to network connections. For example, you want to restart an already connected network:

```
$ sudo nohup sh -c "ifconfig wlan0 down && ifconfig wlan0 up"
```

Note that the output `nohup.out` file requires `sudo` permissions to read and write. Of course, you can also change permissions yourself:

```
$ sudo chown pi:pi nohup.out
```

`nohup` is very useful for programs that need to run in the background for a long time, such as a sensor program.

### 2.2.4 Signals

When we kill a process in `htop`, we are actually sending a signal to that process. Common ones include `SIGTERM`, `SIGINT`, and `SIGKILL`.

The first signal is sent when we type `kill` in the command line. Upon receiving this signal, the process will first save data, then release system resources.

`SIGINT` is the signal sent by `kill -2`, which has the same effect as `Ctrl+C`; you will lose unsaved data. `SIGKILL` corresponds to `kill -9`; this signal tells the kernel to close the process immediately without any warning; it is best not to try sending `SIGKILL` directly.

`HUP` signal corresponding to `kill -1` has the lowest danger level; for many daemons, this signal just makes them perform a round of self-check. On machines with very low fault tolerance, `kill -1` is the preferred choice.

## 2.3 Downloading, Compiling, and Installing

The `curl` command has the same basic functionality as `wget`, while also possessing advanced features like setting proxies, cookies, and `FTP` uploads. The `tar` command can compress or decompress files, as shown in Figure 2-8.

![](EPUB/images/image_025.jpg)

**Figure 2-8**

### 2.3.1 Downloading and Decompressing

`wget` is a very handy tool; its function is to directly download links, but note that it offers no security guarantees. Compared to `wget`, `curl` is a more secure choice. To download a file, we can type:

```
$ curl -OSL https://pypi.python.org/packages/source/R/RPi.GPIO/RPi.GPIO-0.6.5.tar.gz
```

`curl` outputs to standard output by default; `-O` saves the download to a file, `-S` prints errors encountered, and `-L` retries downloading upon encountering errors.

The `GPIO` file we just downloaded is compressed with `gzip` and can be decompressed with `gunzip`, or with the following command:

```
$ tar zxvf RPi.GPIO-0.6.5.tar.gz
```

Note that no "-" symbol is needed here. In `zxvf`, `z` means using `gzip` to decompress, `x` means extract files, `v` means `verbose` (output processing info), and the final `f` means not using standard input but file input.

After decompression, you will get a new folder, and then you can install:

```
$ cd RPi.GPIO-0.6.5
$ sudo python setup.py install
```

If you encounter a `zip` file, just use the `unzip` command directly:

```
$ unzip 2018-11-13-raspbian-stretch-lite.zip
```

### 2.3.2 Manual Compilation

Before package managers appeared, one had to learn to manually compile downloaded software packages. Most software packages at that time were developed in `C` or `C++`, and users often had to compile with `gcc` themselves and handle linking issues.

After `configure` and `make` scripts became widely used, these troubles disappeared. These two scripts can automatically check software dependencies, set correct compilation options, and even install documentation in the correct locations.

Most software provides `.deb` packages, so we can simply use `apt` or `dpkg` to solve the problem. Sometimes projects indeed don't offer such convenience, such as packages downloaded from `GitHub` or manually downloaded source code, which must be compiled manually.

After downloading, the general steps are to decompress the `tar` package:

```
$ tar xvf latest-software.tgz
```

Then `cd` into the source folder, find the `README` and `INSTALL` files; read the `README` file briefly, it usually tells you installation-related matters. Next, you can install; the installation steps are the same in most cases, just these few commands:

```
$ ./configure
$ make
$ sudo make install
```

> **Tip**
>
> Refer specifically to the software's `README`, `INSTALL`, and other installation files.

`Git` was developed by `Linus` as a version control system for the `Linux` kernel code, with the goal of making the software simple and easy to use, as shown in Figure 2-9.

![](EPUB/images/image_026.jpg)

**Figure 2-9**

Using the `./configure` command directly on the Raspberry Pi might install software to locations we don't want, such as `/usr/local/lib`. In subsequent use, problems may arise because the library search paths do not include these installation paths.

Solving this is very simple: just set a new installation path yourself:

```
$ ./configure --prefix=/usr --libdir=/usr/lib/arm-linux-gnueabihf
```

### 2.3.3 Installation via Scripts

Sometimes thoughtful developers provide one-click installation scripts, such as `edublocks` (a programming education software), which can be installed with the following command:

```
$ curl -sSL get.edublocks.org | bash
```

This command downloads the corresponding script and runs the installation directly. Here, `curl`'s `-s` option means don't show the progress bar, `-S` means print error messages, and `-L` means follow redirects. If you don't want to give so many permissions directly to the script, you can compromise by downloading it locally first and then installing. For example, download the script and rename it to `install.sh`:

```
$ curl -o install.sh -L get.edublocks.org
```

You can install the software via `sh install.sh`. Observe its output, and you will find it automatically installs many necessary dependencies. Before running the script, you had better understand what it does; otherwise, there are huge potential security risks.

Writing all installation steps into a script and running it directly can save you time configuring the environment, as shown in Figure 2-10.

![](EPUB/images/image_027.jpg)

**Figure 2-10**

Additionally, `Python` is a very popular language now; it designed some mechanisms that make `python` libraries very easy to install.

Although `Debian`-based systems already have powerful `APT` and `dpkg`, some languages and tools still come with built-in package installation systems, including the famous `emacs`. In future development, you will most commonly use `Javascript`'s `npm` and `Python`'s `pip`. `pip` usage is similar to `apt`:

```
$ sudo pip3 install numpy
```

The process of installing the `numpy` library here is very simple.

### 2.3.4 Disk Images

When burning system image files, the general `cp` command cannot achieve the desired effect. You need a command that can modify the `SD` card's file system; that command is `dd`.

When using the `dd` command, you must confirm that the command's operand is the correct disk. `dd` functions to convert file formats and copy files; its characteristic is that it copies data byte-by-byte faithfully. To burn a system image, we first need to eject the second `MicroSD` card. Before re-inserting and removing, use `sudo fdisk -l` to confirm the device name. If you see a `/dev/sdb` with a size matching the `SD` card, eject `sdb1` first: `umount /dev/sdb1`, then burn the image:

```
sudo dd if=~/Downloads/2018-11-13-raspbian-stretch-lite.img of=/dev/sdb bs=1M
```

The abbreviations `dd` and `if` have a long history, dating back to the 1960s. When using the `dd` command in the future, you must check whether the target disk is correct; otherwise, you may lose important data! Finally, `bs=1M` can also be set to `bs=4M`. After burning, you can use this `SD` card to boot another system.

## 2.4 System Boot and Shutdown

### 2.4.1 Traditional Boot Process

Over the past few years, most `Linux` distributions have changed their boot methods; basically, those old tutorials on how to configure `rc.local` are obsolete, although they still work, they are not recommended. One thing hasn't changed: `Linux` still follows the original design—the first process started becomes the parent of all subsequent processes. Originally this process was `/sbin/init`, now it is `/lib/systemd/systemd`; both have a `PID` of `1`.

The boot process completes most work, then displays the user interface. You can view the system's welcome messages via the `dmesg` command; these messages are saved in the `/var/log/kern.log` file.

The boot process is the starting point of user space. In user space, your programs can modify system behavior without changing kernel source code. Generally, `Linux` distributions implement `UNIX`'s `SystemV` boot mode, through which you can define process run levels, including single-user mode, terminal mode, graphical interface mode, and shutdown.

Now `Jessie` systems no longer use the `service` command; you should use `systemctl` to add services to `systemd`. Links will appear in this folder later; you should not manually modify these links, as shown in Figure 2-11.

![](EPUB/images/image_028.jpg)

**Figure 2-11**

`dmesg` displays system boot information, as shown in Figure 2-12.

![](EPUB/images/image_029.jpg)

**Figure 2-12**

Startup items defined in the `rc0` to `rc6` files under the `/etc` folder will always start at system boot, regardless of the defined run level.

Scripts under `/etc/init.d` can be used to start your databases, servers, and other services. These services can be checked with the `status` command and restarted with the `reload` command. The `reload` command re-checks configuration files but does not fully restart the service.

```
$ sudo /etc/init.d/couchbase-server status
```

We discussed mainly the `Sys-V` boot method above. This method has a major drawback: low efficiency. Next, we introduce a more efficient boot method.

### 2.4.2 New Era Boot: Systemd

The characteristic of `Systemd` is that it starts processes concurrently. After setting a boot target, `Systemd` attempts to resolve the target's dependencies without starting unrelated processes. `Systemd` configuration files are in `/etc/systemd/system`. Note that `Systemd` uses the `systemctl` command, for example:

```
$ sudo systemctl restart ssh
```

Restarts the `ssh` service. For compatibility, `Systemd` did not deprecate the `service` command, for example:

```
$ sudo service apache2 reload
```

Still works normally.

In the `/etc/systemd/system/multi-user.target.wants` folder, you will find many files like `cron.service`. Check with `ls`, and you will find these links point to same-named files in `/lib/systemd/system`; simply put, they handle dependencies between files. Next, we introduce `crontab`:

```
$ sudo systemctl enable postgresql.service
```

This command creates a link, and then `postgreSQL` will run at system boot.

```
$ sudo systemctl daemon-reload
```

This command makes these changes take effect immediately for `systemd`.

Although run levels have no effect in `systemd` mode, you can still check the run level via `who -r`.

For compatibility, `Systemd` systems retain `init.d` and other script files.

`Systemd` is a complex system, including components from `systemd.unit` to `systemd.slice`, each with its own help page, as shown in Figure 2-13.

![](EPUB/images/image_030.jpg)

**Figure 2-13**

`Systemd` can automatically handle link relationships between files, but sometimes you may need to manually manage link relationships; this is where the `ln` command comes in.

The `ln` command can create a link to a file, allowing a file to appear in two different locations in the system:

```
$ sudo ln -s /usr/share/doc/python-numpy/THANKS.txt
```

