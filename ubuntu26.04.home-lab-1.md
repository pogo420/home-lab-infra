# Ubuntu 26.04 home-lab-1
* Follwing 6 points are needed before ansible kicks in.
* Infra as Code [details](./iac/README.md)

## 1) Bare minimum setup
* The `/` must use the complete nvme ssd for OS(check storage configuration in install flow).
* open_ssh must be installed.
* Network access must be enabled.
* update the os: `sudo apt update`

## 2) Fixed IP setup

* Execute `ip addr show eth0` for current IP address of server(via ethernet): inet <IP>/24
* Execute ip route for gatewayIP: `default via <GATEWAY_IP> dev eth0`
* Edit netplan config: `/etc/netplan/<config>.yaml`
    ```
      dhcp4: true
      addresses:
       - <STATIC_IP>/24
      routes:
       - to: default
         via: <GATEWAY_IP>
    ```
* From router config choose STATIC_IP, between range and not allocated.
* Check config via `netplan generate`
* Apply changes `netplan apply`
* Reboot

## 3) Passwordless auth for login(with pass) and asible(without pass)
```shell
ssh-keygen -t ed25519 -C "comment"
ssh-copy-id -i /path/to/ssh/pub/key <username>@<SERVER_IP>
```

## 4) Add user in sudoers file(Required for ansible)
* Create file: `sudo visudo /etc/sudoers.d/ansible-user`
* Add the username, in the file:
  ```
  <username> ALL=(ALL) NOPASSWD: ALL
  ``` 
* Validate by `sudo visudo -c`

## 5) SSH config backup
* backup `/etc/ssh/sshd_config`

## 6) Modify the SSH port
* **Open one ssh connection and keep it open, do not close till the changes are done.**.
* Edit config: `sudo systemctl edit ssh.socket`
* Add following:
  ```text
  [Socket]
  ListenStream=
  ListenStream=0.0.0.0:<custom_port>
  ListenStream=[::]:<custom_port>
  ```
* Reload demon: `sudo systemctl daemon-reload`
* Reload ui: `sudo systemctl restart ssh.socket`
* Verify port: `sudo ss -tlnp | grep ssh`
* Verify config: `sudo systemctl cat ssh.socket`

## Manual Activities Dump

### SSD setup

```shell
# get the name of ssd via lsblk
lsblk
sudo fdisk /dev/'<ssd name seen in lsblk>'
n # new partition and all default
p # should show one partition
w # write the changes

lsblk # to verify the partition
sudo mkfs.ext4 < /dev/'<partition_name>'
mkdir /mnt/directory
mount /dev/'<partition_name>' /mnt/directory
blkid /dev/'<partition_name>' # get the UUID
vim /etc/fstab
# add uuid, ext4, pass will be 2
```
