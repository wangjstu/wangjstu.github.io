---
title: linux rsync examples
date: 2023-12-13 18:55:25
tags:
  - rsync
  - Linux
categories:
  - rsync
---

## 基础信息
- **Rsync** (**Remote Sync**) 是Linux/Unix操作系统下，用于同步远端与本地文件、目录的工具。在rsync命令的帮助下，可以跨网络远程与本地复制、同步数据、目录，执行数据备份，并在两台Linux机器之间进行镜像。
- rsync优势
	- 高效的文件传输——rsync使用增量传输算法，它只传输源文件和目标文件之间的差异，这大大减少了传输的数据量，使其高效地同步大文件或目录。
	- SSH远程文件同步——rsync支持通过SSH进行本地和远程文件传输，支持在本地和远程系统之间进行同步，或者跨多台机器镜像目录。
	- 增量备份—— rsync适合增量备份，因为它只通过传输新的或修改的文件来有效地创建和更新备份。
	- 保留文件权限——rsync可以保留各种文件属性，如权限、所有权、时间戳和符号链接，从而确保复制的文件在目的地保留其原始特征。
	- 支持带宽控制—— rsync允许您在文件传输期间限制带宽使用，因为它在两端发送和接收数据时使用压缩和解压缩方法。
	- 更快——在传输文件时，rsync比scp (Secure Copy)更快，特别是在同步大目录或处理已经部分传输或存在于目标上的文件时。



## rsync help
- **rsync --help**
```shell
rsync  version 3.0.6  protocol version 30
Copyright (C) 1996-2009 by Andrew Tridgell, Wayne Davison, and others.
Web site: http://rsync.samba.org/
Capabilities:
    64-bit files, 64-bit inums, 64-bit timestamps, 64-bit long ints,
    socketpairs, hardlinks, symlinks, IPv6, batchfiles, inplace,
    append, ACLs, xattrs, iconv, symtimes

rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
are welcome to redistribute it under certain conditions.  See the GNU
General Public Licence for details.

rsync is a file transfer program capable of efficient remote update
via **a fast differencing algorithm**.

Usage: rsync [OPTION]... SRC [SRC]... DEST
  or   rsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST
  or   rsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST
  or   rsync [OPTION]... SRC [SRC]... rsync://[USER@]HOST[:PORT]/DEST
  or   rsync [OPTION]... [USER@]HOST:SRC [DEST]
  or   rsync [OPTION]... [USER@]HOST::SRC [DEST]
  or   rsync [OPTION]... rsync://[USER@]HOST[:PORT]/SRC [DEST]
The ':' usages connect via remote shell, while '::' & 'rsync://' usages connect
to an rsync daemon, and require SRC or DEST to start with a module name.

Options
 -v, --verbose               increase verbosity
                             显示详细信息。
 -q, --quiet                 suppress non-error messages
     --no-motd               suppress daemon-mode MOTD (see manpage caveat)
						     精简输出模式。
 -c, --checksum              skip based on checksum, not mod-time & size
							 打开校验开关，强制对文件传输进行校验。
 -a, --archive               archive mode; equals -rlptgoD (no -H,-A,-X)
     --no-OPTION             turn off an implied OPTION (e.g. --no-D)
						     归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD。
 -r, --recursive             recurse into directories
							 对子目录以递归模式处理。
 -R, --relative              use relative path names
     --no-implied-dirs       don't send implied dirs with --relative
						     使用相对路径信息。
 -b, --backup                make backups (see --suffix & --backup-dir)
							 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。
     --backup-dir=DIR        make backups into hierarchy based in DIR
						     将备份文件(如~filename)存放在在目录下。
     --suffix=SUFFIX         set backup suffix (default ~ w/o --backup-dir)
						     定义备份文件前缀。
 -u, --update                skip files that are newer on the receiver
							 仅仅进行更新，也就是跳过所有已经存在于目标设备上更新的文件，只更新目标设备上修改时间晚于源头设备上的文件。
     --inplace               update destination files in-place (SEE MAN PAGE)
						     使用替换模式更新目标文件
     --append                append data onto shorter files
						     追加数据到文件
     --append-verify         like --append, but with old data in file checksum
						     类似于——append，但在文件校验和中使用旧数据
 -d, --dirs                  transfer directories without recursing
							 不递归地传输目录。
 -l, --links                 copy symlinks as symlinks
							 在复制文件时保留源文件中的符号链接。如果源文件是符号链接，那么目标文件也将是符号链接。
 -L, --copy-links            transform symlink into referent file/dir
							 拷贝文件时，将源文件的符号链接替换为链接所指向的实际文件。这使得目标目录包含的是链接指向的文件，而不是符号链接本身。`-L` 选项可能会导致目标目录中的文件不再是符号链接，而是实际文件的副本。因此，在使用这个选项时需要小心，确保你的需求是将符号链接替换为链接所指向的实际文件。
     --copy-unsafe-links     only "unsafe" symlinks are transformed
     --safe-links            ignore symlinks that point outside the source tree
 -k, --copy-dirlinks         transform symlink to a dir into referent dir
							 在文件复制时保留源文件的扩展属性。扩展属性是与文件关联的元数据，提供了额外的信息，例如文件的安全上下文或其他特殊属性。
 -K, --keep-dirlinks         treat symlinked dir on receiver as dir
							 会在复制时保留源文件的粘滞位。这可以确保目标目录中的文件继承了源文件的粘滞位。
 -H, --hard-links            preserve hard links
							 复制文件时保留源文件的硬链接关系。硬链接是多个文件名指向同一 inode 的情况，而 `rsync -H` 会尝试在目标目录中保留这种关系，以便实现源目录中的硬链接关系。
 -p, --perms                 preserve permissions
							 会在复制文件时保留源文件的权限（包括所有者、所属组、权限位等）。这确保了目标文件在复制后具有与源文件相同的权限设置。`-p` 选项仅保留基本的权限信息，而不保留特殊的 ACL（Access Control List）等扩展权限。如果需要保留更多的权限信息，可以考虑使用 `-A` 选项。
 -E, --executability         preserve the file's executability
							 保留文件的可执行属性。
     --chmod=CHMOD           affect file and/or directory permissions
						     影响文件和/或目录的权限
 -A, --acls                  preserve ACLs (implies --perms)
							 保留 ACLs（访问控制列表），相当于 `--perms`。
 -X, --xattrs                preserve extended attributes
							 保留扩展属性。
 -o, --owner                 preserve owner (super-user only)
							 保留文件所有者（仅超级用户）
 -g, --group                 preserve group
							 保留文件所属组
     --devices               preserve device files (super-user only)
						     保留设备文件（仅超级用户）
     --copy-devices          copy device contents as regular file
						     将设备文件内容作为常规文件复制
     --specials              preserve special files
						     保留特殊文件。
 -D                          same as --devices --specials
							 同 `--devices --specials`。
 -t, --times                 preserve modification times
							 保留修改时间。
 -O, --omit-dir-times        omit directories from --times
							 在 `--times` 时省略目录的修改时间。
     --super                 receiver attempts super-user activities
						     接收方尝试超级用户操作。
     --fake-super            store/recover privileged attrs using xattrs
						     使用扩展属性存储/恢复特权属性。
 -S, --sparse                handle sparse files efficiently
							 高效处理稀疏文件。
 -n, --dry-run               perform a trial run with no changes made
							 演示运行，不实际进行更改。
 -W, --whole-file            copy files whole (without delta-xfer algorithm)
							 整个文件传输（不使用增量传输算法）。
 -x, --one-file-system       don't cross filesystem boundaries
							 不跨越文件系统边界。
 -B, --block-size=SIZE       force a fixed checksum block-size
							 强制使用固定的校验块大小。
 -e, --rsh=COMMAND           specify the remote shell to use
							 指定用于远程操作的Shell命令。
     --rsync-path=PROGRAM    specify the rsync to run on the remote machine
						     指定在远程机器上运行的`rsync`程序的路径。
     --existing              skip creating new files on receiver
						     跳过在接收方创建新文件。
     --ignore-existing       skip updating files that already exist on receiver
						     跳过更新接收方上已存在的文件。
     --remove-source-files   sender removes synchronized files (non-dirs)
						     发送方删除已同步的文件（非目录）。
     --del                   an alias for --delete-during
						     `--delete-during`的别名。
     --delete                delete extraneous files from destination dirs
						     从目标目录中删除多余的文件。
     --delete-before         receiver deletes before transfer, not during
						     接收方在传输之前删除文件，而不是在传输过程中。
     --delete-during         receiver deletes during transfer (default)
						     接收方在传输过程中删除文件（默认）。
     --delete-delay          find deletions during, delete after
						     查找删除项，传输完成后再删除。
     --delete-after          receiver deletes after transfer, not during
						     接收方在传输后而不是在传输过程中删除文件。
     --delete-excluded       also delete excluded files from destination dirs
						     从目标目录中删除被排除的文件。
     --ignore-errors         delete even if there are I/O errors
						     即使有I/O错误也继续删除。
     --force                 force deletion of directories even if not empty
						     强制删除目录，即使它们不为空。
     --max-delete=NUM        don't delete more than NUM files
						     不要删除超过指定数量的文件。
     --max-size=SIZE         don't transfer any file larger than SIZE
						     不传输大于指定大小的文件。
     --min-size=SIZE         don't transfer any file smaller than SIZE
						     不传输小于指定大小的文件。
     --partial               keep partially transferred files
						     保留部分传输的文件
     --partial-dir=DIR       put a partially transferred file into DIR
						     将部分传输的文件放入指定目录。
     --delay-updates         put all updated files into place at transfer's end
						     在传输结束时将所有更新的文件放置到目标位置。
 -m, --prune-empty-dirs      prune empty directory chains from the file-list
							 从文件列表中删除空目录链。
     --numeric-ids           don't map uid/gid values by user/group name
						     不通过用户名/组名映射`uid/gid`值。
     --timeout=SECONDS       set I/O timeout in seconds
						     设置I/O超时时间（秒）。
     --contimeout=SECONDS    set daemon connection timeout in seconds
						     设置守护进程连接超时时间（秒）。
 -I, --ignore-times          don't skip files that match in size and mod-time
							 不跳过大小和修改时间匹配的文件。
     --size-only             skip files that match in size
						     只跳过大小匹配的文件。
     --modify-window=NUM     compare mod-times with reduced accuracy
						     以较低的准确性比较修改时间。
 -T, --temp-dir=DIR          create temporary files in directory DIR
							 在目录DIR中创建临时文件。
 -y, --fuzzy                 find similar file for basis if no dest file
							 如果没有目标文件，则寻找相似的基准文件。
     --compare-dest=DIR      also compare destination files relative to DIR
						     相对于指定的目录`DIR`，在传输源文件时与目标目录进行比较。具体来说，`rsync`会首先检查源文件是否与`DIR`中的文件匹配，如果匹配，则`rsync`会将该文件的内容复制到目标目录，但不会在目标目录中创建新文件。
     --copy-dest=DIR         ... and include copies of unchanged files
						     包括未更改的文件的副本。
     --link-dest=DIR         hardlink to files in DIR when unchanged
						     在未更改时硬链接到DIR中的文件。
 -z, --compress              compress file data during the transfer
							 在传输过程中压缩文件数据。
     --compress-level=NUM    explicitly set compression level
						     明确设置压缩级别。
     --skip-compress=LIST    skip compressing files with a suffix in LIST
						     跳过在LIST中具有后缀的文件的压缩。
 -C, --cvs-exclude           auto-ignore files the same way CVS does
							 自动忽略文件，就像CVS一样。
 -f, --filter=RULE           add a file-filtering RULE
							 添加文件过滤规则。
 -F                          same as --filter='dir-merge /.rsync-filter'
                             repeated: --filter='- .rsync-filter'
                             同 `--filter='dir-merge /.rsync-filter'`，即 `--filter='- .rsync-filter'`。
     --exclude=PATTERN       exclude files matching PATTERN
						     排除与PATTERN匹配的文件。
     --exclude-from=FILE     read exclude patterns from FILE
						     从FILE中读取排除模式。
     --include=PATTERN       don't exclude files matching PATTERN
						     不排除与PATTERN匹配的文件。
     --include-from=FILE     read include patterns from FILE
						     从FILE中读取包含模式。
     --files-from=FILE       read list of source-file names from FILE
						     从FILE中读取源文件名列表。
 -0, --from0                 all *-from/filter files are delimited by 0s
							 所有*-from/filter文件以0分隔。
 -s, --protect-args          no space-splitting; only wildcard special-chars
							 不拆分参数中的空格；仅保留通配符特殊字符。
     --address=ADDRESS       bind address for outgoing socket to daemon
						     绑定传出套接字到守护程序的地址。
     --port=PORT             specify double-colon alternate port number
						     指定双冒号的备用端口号。
     --sockopts=OPTIONS      specify custom TCP options
						     指定自定义TCP选项。
     --blocking-io           use blocking I/O for the remote shell
						     为远程Shell使用阻塞I/O。
     --stats                 give some file-transfer stats
						     提供一些文件传输统计信息。
 -8, --8-bit-output          leave high-bit chars unescaped in output
							 在输出中保留高位字符而不进行转义。
 -h, --human-readable        output numbers in a human-readable format
							 以人类可读的格式输出数字。
     --progress              show progress during transfer
						     在传输过程中显示进度。
 -P                          same as --partial --progress
							 同 `--partial --progress`。
 -i, --itemize-changes       output a change-summary for all updates
							 输出所有更新的变更摘要。
     --out-format=FORMAT     output updates using the specified FORMAT
						     使用指定的FORMAT输出更新。
     --log-file=FILE         log what we're doing to the specified FILE
						     将我们的操作记录到指定的FILE中。
     --log-file-format=FMT   log updates using the specified FMT
						     使用指定的FMT记录更新。
     --password-file=FILE    read daemon-access password from FILE
						     从FILE中读取守护进程访问密码。
     --list-only             list the files instead of copying them
						     列出文件而不是复制它们。
     --bwlimit=KBPS          limit I/O bandwidth; KBytes per second
						     限制I/O带宽；每秒KB字节。
     --write-batch=FILE      write a batched update to FILE
						     将批量更新写入FILE。
     --only-write-batch=FILE like --write-batch but w/o updating destination
						     类似于 `--write-batch`，但不更新目标。
     --read-batch=FILE       read a batched update from FILE
						     从FILE中读取批量更新。
     --protocol=NUM          force an older protocol version to be used
						     强制使用较旧的协议版本。
     --iconv=CONVERT_SPEC    request charset conversion of filenames
						     请求文件名的字符集转换。
 -4, --ipv4                  prefer IPv4
							 优先使用IPv4。
 -6, --ipv6                  prefer IPv6
							 优先使用IPv6。
     --version               print version number
						     打印版本号。
(-h) --help                  show this help (-h works with no other options)
							显示帮助信息（-h在无其他选项的情况下也起作用）。

Use "rsync --daemon --help" to see the daemon-mode command-line options.
显示有关`rsync`守护进程模式的命令行选项和帮助信息，以便用户了解如何配置和运行`rsync`作为守护进程。
Please see the rsync(1) and rsyncd.conf(5) man pages for full documentation.
See http://rsync.samba.org/ for updates, bug reports, and answers

```

## Rsync安装
```Shell
$ sudo apt install rsync         # Debian, Ubuntu and Mint
$ sudo yum install rsync         # RHEL/CentOS/Fedora,  Rocky/AlmaLinux
$ sudo emerge -a sys-apps/rsync  # Gentoo Linux
$ sudo apk add rsync             # Alpine Linux
$ sudo pacman -S rsync           # Arch Linux
$ sudo zypper install rsync      # OpenSUSE
```

## 常用案例

### 本地复制/同步文件
- 备份`backup.tar`到目录`/tmp/backups/`
```Shell
[root@wangjstupc]# rsync -zvh backup.tar.gz /tmp/backups/
created directory /tmp/backups   //不存在目录会自动创建目录
backup.tar.gz

sent 224.54K bytes  received 70 bytes  449.21K bytes/sec
total size is 224.40K  speedup is 1.00
```

### 本地复制/同步目录
- 将目录 `/root/rpmpkgs` 备份到`/tmp/backups/`目录下，完成后，`/tmp/backups/`目录下有一个`rpmpkgs`目录：
```Shell
[root@wangjstupc]# rsync -avzh /root/rpmpkgs /tmp/backups/
sending incremental file list
rpmpkgs/
rpmpkgs/httpd-2.4.37-40.module_el8.5.0+852+0aafc63b.x86_64.rpm
rpmpkgs/nagios-plugins-2.3.3-5.el8.x86_64.rpm

sent 3.47M bytes  received 96 bytes  2.32M bytes/sec
total size is 3.74M  speedup is 1.08
```

### 将本地目录复制到远程服务器
- 将目录`rpmpkgs` 备份到远程机器下
```Shell
[root@wangjstupc]# rsync -avzh /root/rpmpkgs root@192.168.0.188:/root/
// 同步完成后，192.168.0.188的root目录下有一个rpmpkgs目录
```

### 将目录从远程复制到本地服务器
- 将目录`/root/rpmpkgs` 备份到本地`/tmp/myrpms`
```Shell
[root@wangjstupc]# rsync -avzh root@192.168.0.188:/root/rpmpkgs /tmp/myrpms
// 同步完成后，本地目录/tmp/myrpms下有一个rpmpkgs目录
```

### 通过ssh Rsync
- 使用rsync，我们可以使用SSH (Secure Shell)进行数据传输。使用SSH协议，可以确保数据在加密的安全连接中传输，这样就没有人可以读取您的数据。使用rsync时，我们需要提供用户/密码来完成特定的任务，使用SSH选项将以加密的方式发送登录信息，保证密码安全。要通过SSH使用rsync，可以使用-e选项指定远程shell命令，通常是SSH
```Shell
[root@wangjstupc]# rsync [OPTIONS] -e ssh /path/to/source user@remote:/path/to/destination
```

### 使用SSH将文件从远程服务器复制到本地服务器
- 通过ssh，将`192.168.0.188`上文件`/root/test.cfg` 备份到本地`/tmp/myrpms`
```Shell
[root@wangjstupc]# rsync -avzhe ssh root@192.168.0.188:/root/test.cfg /tmp/myrpms
// 同步完成后，本地目录/tmp/myrpms下有一个test.cfg
```

### 使用SSH将文件从本地服务器复制到远程服务器
- 通过ssh，将本地`/tmp/myrpms/test.cfg`备份到本地`192.168.0.188`上文件`/root/` 目录下
```Shell
[root@wangjstupc]# rsync -avzhe ssh  /tmp/myrpms/test.cfg root@192.168.0.188:/root/
// 同步完成后，远程目录/root下会有一个test.cfg
```

### 传输数据时显示进度
- 通过`--progress`显示进度
```Shell
[root@wangjstupc]# rsync -avzhe ssh --progress /tmp/myrpms/test.cfg root@192.168.0.188:/root/
// 同步完成后，远程目录/root下会有一个test.cfg
```

### 同步特定(include)扩展名的文件
- 通过`--include`显示进度
```Shell
[root@wangjstupc]# rsync -avz --include='*.txt' /path/to/source/ user@remote:/path/to/destination/
// 同步/path/to/source/ 下txt结尾文件到user@remote:/path/to/destination/下
```

### 排除(exclude)特定扩展名的文件
- 通过`--exclude`显示进度
```Shell
[root@wangjstupc]# rsync -avz --exclude='*.txt' /path/to/source/ user@remote:/path/to/destination/
// 同步/path/to/source/ 下非txt结尾文件到user@remote:/path/to/destination/下
```

### exclude与include同时使用
- 这两个选项允许我们通过指定参数来包含和排除文件。这些选项帮助我们指定那些你想要在同步中包含的文件或目录，并排除你不想要传输的文件和文件夹。
```Shell
[root@wangjstupc]# rsync -avze ssh --include 'R*' --exclude '*' root@192.168.0.188:/var/lib/rpm/ /root/rpm
// rsync命令将只包括那些以'R'开头的文件和目录，而不包括所有其他文件和目录。
```

### 在Rsync中使用`--delete`选项
- 如果一个文件或目录在源端不存在，但在目标端已经存在，那么您可能需要在同步时删除目标端现有的文件或目录。
```Shell
[root@wangjstupc]# rsync -avz --delete root@192.168.0.151:/var/lib/rpm/ /root/rpm/
```

### 使用-delete选项
- 如果一个文件或目录在源端不存在，但在目标端已经存在，那么您可能需要在同步时删除目标端现有的文件或目录。
```Shell
[root@wangjstupc]# rsync -avz --delete root@192.168.0.151:/var/lib/rpm/ /root/rpm/
```

### 设置文件传输限制
- 指定要传输或同步的最大文件大小。使用`--max-size`选项来执行此操作。在本例中，最大文件大小为200k，因此该命令将只传输等于或小于200k的文件。
```Shell
[root@wangjstupc]# rsync -avzhe ssh --max-size='200k' /var/lib/rpm/ root@192.168.0.188:/root/tmprpm
```

### 传输后自动删除源文件
- 使用`--remove-source-files`将源服务器上文件传输后进行移除。
```Shell
[root@wangjstupc]# rsync --remove-source-files -zvh backup.tar.gz root@192.168.0.188:/tmp/backups/
```

### rsync命令预演运行
- 使用`--dry-run`可以提前查看命令效果而不真正执行
```Shell
[root@wangjstupc]# rsync --dry-run --remove-source-files -zvh backup.tar.gz root@192.168.0.188:/tmp/backups/
```

### 设置带宽限制和传输文件
- 将数据从一台机器传输到另一台机器时，可以使用`--bwlimit`选项设置带宽限制。这个选项帮助我们限制I/O带宽。
```Shell
[root@wangjstupc]# rsync --bwlimit=100 -avzhe ssh  /var/lib/rpm/  root@192.168.0.188:/root/tmprpm/
```

### 强制同步整个文件
- rsync只同步更改的块和字节，如果你明确地想同步整个文件，那么你可以使用' -W '选项。
```Shell
[root@wangjstupc]# rsync -zvhW backup.tar /tmp/backups/backup.tar
```


## 参考文档
- [rsync-local-remote-file-synchronization-commands](https://www.tecmint.com/rsync-local-remote-file-synchronization-commands)
- [rsync man](https://man.linuxde.net/rsync)