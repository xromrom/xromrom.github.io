# iOS Keychain grabbing

The easiest way to read data from iOS Keychain is using `keychaineditor` tool.
In this tutorial I show how to install and use it.

Prerequisites:
* jailbroken iOS 12.4.6 device
* installed OpenSSH

## Install

1.	Connect via SSH to iOS device:
```shell
ssh root@192.168.X.X
```
2.	Install keychaineditor:
```shell
wget https://github.com/NitinJami/keychaineditor/raw/master/keychaineditor.deb
dpkg -i keychaineditor.deb
```
3.	Check installation:
```shell
keychaineditor –v
```

## How to Use

Try to find something in keychain with command
```shell
keychaineditor -f <app name>
```

More commands (for example, editing of keychain items) at [repo for the tool](https://github.com/NitinJami/keychaineditor)
