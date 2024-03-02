# Резервное копирование.

## Цель: Настроить бэкапы.

Логи бэкапа из /var/log/messages:
```
Feb 29 06:45:58 localhost systemd: Starting Borg Backup...
Feb 29 06:46:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:46:00 localhost borg: Archive name: etc-2024-02-29_09:45:58
Feb 29 06:46:00 localhost borg: Archive fingerprint: 715e4aa9b949fc7c5dc4af0061340be197a83fb8383520ec2260be0c94243275
Feb 29 06:46:00 localhost borg: Time (start): Thu, 2024-02-29 09:45:59
Feb 29 06:46:00 localhost borg: Time (end):   Thu, 2024-02-29 09:46:00
Feb 29 06:46:00 localhost borg: Duration: 0.68 seconds
Feb 29 06:46:00 localhost borg: Number of files: 1700
Feb 29 06:46:00 localhost borg: Utilization of max. archive size: 0%
Feb 29 06:46:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:46:00 localhost borg: Original size      Compressed size    Deduplicated size
Feb 29 06:46:00 localhost borg: This archive:               28.43 MB             13.49 MB                720 B
Feb 29 06:46:00 localhost borg: All archives:               56.85 MB             26.99 MB             11.84 MB
Feb 29 06:46:00 localhost borg: Unique chunks         Total chunks
Feb 29 06:46:00 localhost borg: Chunk index:                    1284                 3400
Feb 29 06:46:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:46:04 localhost systemd: Started Borg Backup.
Feb 29 06:50:16 localhost systemd: Created slice User Slice of vagrant.
Feb 29 06:50:16 localhost systemd: Started Session 6 of user vagrant.
Feb 29 06:50:16 localhost systemd-logind: New session 6 of user vagrant.
Feb 29 06:51:58 localhost systemd: Starting Borg Backup...
Feb 29 06:52:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:52:00 localhost borg: Archive name: etc-2024-02-29_09:51:58
Feb 29 06:52:00 localhost borg: Archive fingerprint: f9db0ad42f0faa72f43891ec49db0e6e624220b39b27a5da96d7e2689a9d7ffd
Feb 29 06:52:00 localhost borg: Time (start): Thu, 2024-02-29 09:51:59
Feb 29 06:52:00 localhost borg: Time (end):   Thu, 2024-02-29 09:52:00
Feb 29 06:52:00 localhost borg: Duration: 0.65 seconds
Feb 29 06:52:00 localhost borg: Number of files: 1700
Feb 29 06:52:00 localhost borg: Utilization of max. archive size: 0%
Feb 29 06:52:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:52:00 localhost borg: Original size      Compressed size    Deduplicated size
Feb 29 06:52:00 localhost borg: This archive:               28.43 MB             13.49 MB             55.88 kB
Feb 29 06:52:00 localhost borg: All archives:               56.85 MB             26.99 MB             11.90 MB
Feb 29 06:52:00 localhost borg: Unique chunks         Total chunks
Feb 29 06:52:00 localhost borg: Chunk index:                    1287                 3400
Feb 29 06:52:00 localhost borg: ------------------------------------------------------------------------------
Feb 29 06:52:04 localhost systemd: Started Borg Backup.
```

Попробуем удалить /etc и восстановить её из бэкапа:
```
[root@client ~]# systemctl stop borg-backup.timer          
[root@client ~]# systemctl stop borg-backup.service
[root@client ~]# borg list borg@192.168.56.160:/var/backup/
Enter passphrase for key ssh://borg@192.168.56.160/var/backup: 
etc-2024-02-29_15:22:20              Thu, 2024-02-29 15:22:21 [1d28d488e7b227e5afa8aa9a24ad032e4687bc491fe57c5e9fc0d3a208c3e855]
``` 
Так как после удаления каталога /etc мы потеряем доступ к серверу backup  
`[root@client etc]# borg extract borg@192.168.56.160:/var/backup/::etc-2024-02-29_13:22:50
Connection closed by remote host. Is borg working on the server?
`, перед удалением /etc, восстановим бэкап в папку new_etc, а оттуда перенесём.
```
[root@client ~]# mkdir new_etc                             
[root@client ~]# cd new_etc
[root@client new_etc]# borg extract borg@192.168.56.160:/var/backup/::etc-2024-02-29_15:22:20
[root@client new_etc]# rm -rf /etc/*
[root@client new_etc]# ls -a /etc
.  .. 
[root@client new_etc]# 
[root@client new_etc]# mv etc/* /etc/
[root@client new_etc]# ls -a /etc
.                        gcrypt          my.cnf.d           rsyslog.d
..                       gnupg           netconfig          rwtab
adjtime                  GREP_COLORS     NetworkManager     rwtab.d
aliases                  groff           networks           samba
aliases.db               group           nfs.conf           sasl2
alternatives             group-          nfsmount.conf      securetty
anacrontab               grub2.cfg       nsswitch.conf      security
audisp                   grub.d          nsswitch.conf.bak  selinux
audit                    gshadow         openldap           services
bash_completion.d        gshadow-        opt                sestatus.conf
bashrc                   gss             os-release         shadow
binfmt.d                 gssproxy        pam.d              shadow-
centos-release           host.conf       passwd             shells
centos-release-upstream  hostname        passwd-            skel
chkconfig.d              hosts           pkcs11             ssh
chrony.conf              hosts.allow     pki                ssl
chrony.keys              hosts.deny      pm                 statetab
cifs-utils               idmapd.conf     polkit-1           statetab.d
cron.d                   init.d          popt.d             subgid
cron.daily               inittab         postfix            subuid
cron.deny                inputrc         ppp                sudo.conf
cron.hourly              iproute2        prelink.conf.d     sudoers
cron.monthly             issue           printcap           sudoers.d
crontab                  issue.net       profile            sudo-ldap.conf
cron.weekly              krb5.conf       profile.d          sysconfig
crypttab                 krb5.conf.d     protocols          sysctl.conf
csh.cshrc                ld.so.cache     .pwd.lock          sysctl.d
csh.login                ld.so.conf      python             systemd
dbus-1                   ld.so.conf.d    qemu-ga            system-release
default                  libaudit.conf   rc0.d              system-release-cpe
depmod.d                 libnl           rc1.d              tcsd.conf
dhcp                     libuser.conf    rc2.d              terminfo
DIR_COLORS               locale.conf     rc3.d              tmpfiles.d
DIR_COLORS.256color      localtime       rc4.d              tuned
DIR_COLORS.lightbgcolor  login.defs      rc5.d              udev
dracut.conf              logrotate.conf  rc6.d              .updated
dracut.conf.d            logrotate.d     rc.d               vconsole.conf
e2fsck.conf              machine-id      rc.local           virc
environment              magic           redhat-release     vmware-tools
ethertypes               man_db.conf     request-key.conf   wpa_supplicant
exports                  mke2fs.conf     request-key.d      X11
exports.d                modprobe.d      resolv.conf        xdg
filesystems              modules-load.d  rpc                xinetd.d
firewalld                motd            rpm                yum
fstab                    mtab            rsyncd.conf        yum.conf
fuse.conf                my.cnf          rsyslog.conf       yum.repos.d
[root@client new_etc]# 
``` 