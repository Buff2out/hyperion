❯ ssh root@64.188.70.209
Last login: Sun Nov  2 16:29:35 2025 from 46.39.249.16

[root@nixos-installer:~]# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0    7:0    0 316.2M  0 loop /nix/.ro-store
loop1    7:1    0    36K  1 loop /run/nixos-etc-metadata
sr0     11:0    1  1024M  0 rom
vda    253:0    0    20G  0 disk
├─vda1 253:1    0    19G  0 part
├─vda2 253:2    0     1K  0 part
└─vda5 253:5    0   975M  0 part

[root@nixos-installer:~]# wipefs -a /dev/vda

[root@nixos-installer:~]# dd if=/dev/zero of=/dev/vda bs=512 count=4096
4096+0 records in
4096+0 records out
2097152 bytes (2.1 MB, 2.0 MiB) copied, 0.0750978 s, 27.9 MB/s

[root@nixos-installer:~]# partprobe /dev/vda

[root@nixos-installer:~]# parted /dev/vda
GNU Parted 3.6
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel gpt
(parted) unit s
(parted) mkpart primary 2048s 4095s
(parted) set 1 bios_grub on
(parted) mkpart primary 4096s 100%
(parted) quit
Information: You may need to update /etc/fstab.


[root@nixos-installer:~]# mkfs.ext4 /dev/vda2
mke2fs 1.47.2 (1-Jan-2025)
Creating filesystem with 5242112 4k blocks and 1310720 inodes
Filesystem UUID: b209859d-d8c0-4552-a1cf-a81f4f8b3cdc
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done


[root@nixos-installer:~]# mount /dev/vda2 /mnt

[root@nixos-installer:~]# ^C

[root@nixos-installer:~]# mkdir -p /mnt/boot

[root@nixos-installer:~]# mkdir -p /mnt/dev /mnt/proc /mnt/sys /mnt/run

[root@nixos-installer:~]# mount --bind /dev /mnt/dev

[root@nixos-installer:~]# mount --bind /proc /mnt/proc

[root@nixos-installer:~]# mount --bind /sys /mnt/sys

[root@nixos-installer:~]# mount --bind /run /mnt/run

[root@nixos-installer:~]# ls network/
addrs.json  routes-v4.json  routes-v6.json

[root@nixos-installer:~]# cd network/

[root@nixos-installer:~/network]# cat addrs.json routes-v4.json routes-v6.json
[{"ifindex":1,"ifname":"lo","flags":["LOOPBACK","UP","LOWER_UP"],"mtu":65536,"qdisc":"noqueue","operstate":"UNKNOWN","group":"default","txqlen":1000,"link_type":"loopback","address":"00:00:00:00:00:00","broadcast":"00:00:00:00:00:00","addr_info":[{"family":"inet","local":"127.0.0.1","prefixlen":8,"scope":"host","label":"lo","valid_life_time":4294967295,"preferred_life_time":4294967295},{"family":"inet6","local":"::1","prefixlen":128,"scope":"host","noprefixroute":true,"valid_life_time":4294967295,"preferred_life_time":4294967295}]},{"ifindex":2,"ifname":"ens3","flags":["BROADCAST","MULTICAST","UP","LOWER_UP"],"mtu":1500,"qdisc":"fq","operstate":"UP","group":"default","txqlen":1000,"link_type":"ether","address":"52:54:00:07:9e:d3","broadcast":"ff:ff:ff:ff:ff:ff","altnames":["enp0s3"],"addr_info":[{"family":"inet","local":"64.188.70.209","prefixlen":32,"broadcast":"64.188.70.209","scope":"global","label":"ens3","valid_life_time":4294967295,"preferred_life_time":4294967295},{"family":"inet6","local":"2a12:bec4:1bb0:de5::2","prefixlen":64,"scope":"global","valid_life_time":4294967295,"preferred_life_time":4294967295},{"family":"inet6","local":"fe80::5054:ff:fe07:9ed3","prefixlen":64,"scope":"link","protocol":"kernel_ll","valid_life_time":4294967295,"preferred_life_time":4294967295}]}]
[{"dst":"default","gateway":"10.0.0.1","dev":"ens3","flags":["onlink"]}]
[{"dst":"2a12:bec4:1bb0:de5::/64","dev":"ens3","protocol":"kernel","metric":256,"flags":[],"pref":"medium"},{"dst":"fe80::/64","dev":"ens3","protocol":"kernel","metric":256,"flags":[],"pref":"medium"},{"dst":"default","gateway":"2a12:bec4:1bb0:de5::1","dev":"ens3","metric":1024,"flags":["onlink"],"pref":"medium"}]

[root@nixos-installer:~/network]# cd ..

[root@nixos-installer:~]# ls /etc/nix
nix/   nixos/

[root@nixos-installer:~]# ls /etc/nix
nix/   nixos/

[root@nixos-installer:~]# ls /etc/nixos/
configuration.nix  nix-server

[root@nixos-installer:~]# cd nix-server
-bash: cd: nix-server: No such file or directory

[root@nixos-installer:~]# ls /etc/nixos/nix-server/
client-config.json.example  flake.nix                   project.md
client-vless-link.txt       ginpee.toml                 README.md
configuration.nix           hardware-configuration.nix  server-vars.nix
deploy.sh                   modules                     setup-proxy-profile.py
flake.lock                  networking.nix

[root@nixos-installer:~]# cd /etc/nixos/nix-server/

[root@nixos-installer:/etc/nixos/nix-server]# nix flake check
warning: Git tree '/etc/nixos/nix-server' is dirty
error:
       … while checking flake output 'nixosConfigurations'
         at /nix/store/k1m4qlhi23jbbsdv1sf9z8y85pasgxxa-source/flake.nix:18:7:
           17|     {
           18|       nixosConfigurations.server = nixpkgs.lib.nixosSystem {
             |       ^
           19|         system = "x86_64-linux";

       … while checking the NixOS configuration 'nixosConfigurations.server'
         at /nix/store/k1m4qlhi23jbbsdv1sf9z8y85pasgxxa-source/flake.nix:18:7:
           17|     {
           18|       nixosConfigurations.server = nixpkgs.lib.nixosSystem {
             |       ^
           19|         system = "x86_64-linux";

       … while evaluating the option `system.build.toplevel':

       … while evaluating definitions from `/nix/store/xjjq52iwslhz6lbc621a31v0nfdhr5ks-source/nixos/modules/system/activation/top-level.nix':

       (stack trace truncated; use '--show-trace' to show the full, detailed trace)

       error:
       Failed assertions:
       - The ‘fileSystems’ option does not specify your root file system.
       - You must set the option ‘boot.loader.grub.devices’ or 'boot.loader.grub.mirroredBoots' to make the system bootable.

[root@nixos-installer:/etc/nixos/nix-server]# nano hardware-configuration.nix
-bash: nano: command not found

[root@nixos-installer:/etc/nixos/nix-server]# nix-shell -p vim
warning: Nix search path entry '/nix/var/nix/profiles/per-user/root/channels/nixos' does not exist, ignoring
warning: Nix search path entry '/nix/var/nix/profiles/per-user/root/channels' does not exist, ignoring
error:
       … while calling the 'import' builtin
         at «string»:1:18:
            1| {...}@args: with import <nixpkgs> args; (pkgs.runCommandCC or pkgs.runCommand) "shell" { buildInputs = [ (vim) ]; } ""
             |                  ^

       … while realising the context of a path

       … while calling the 'findFile' builtin
         at «string»:1:25:
            1| {...}@args: with import <nixpkgs> args; (pkgs.runCommandCC or pkgs.runCommand) "shell" { buildInputs = [ (vim) ]; } ""
             |                         ^

       error: file 'nixpkgs' was not found in the Nix search path (add it using $NIX_PATH or -I)

[root@nixos-installer:/etc/nixos/nix-server]# vim hardware-configuration.nix
-bash: vim: command not found

[root@nixos-installer:/etc/nixos/nix-server]# nix flake check
warning: Git tree '/etc/nixos/nix-server' is dirty
checking NixOS configuration 'nixosConfigurations.server'^C