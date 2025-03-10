# Recursively clean MacOS sierra+ notarization crap
xattr -rc /some-malware-dir/

(this remove all extended attributes and thus all notarization info from downloaded files/executables)

Explanation: If you download any executable or shell script from internet and you dont execute file but you use any (hex) editor to open/read executable or script file (e.g. /some-malware-dir/some_malware_install.sh) then macos notarization won't let you. Unfortunately Mac cares not if you open or execute file. It still shows same message like "some_malware_install.sh can't be opened because it is from an unidentified developer". The info that a file is from internet is in some extended attributes.
Today it is well known you can always override and open anyways in security settings.
But if you do that it won't simply open hex editor and let you inspect file BUT INSTEAD it straight EXECUTES file you only wanted to INSPECT!!

This is very dangerous so only solution is prevent notarization crap from popping up in the first place by applying above solution.

# Open DMG's if you deliberately crippled fsck_hfs
__This only applies if you did not use the true(1) method to cripple fsck_hfs__
aka manually mounting a MacOS DMG file
```
hdiutil attach -nomount <some-dmg.dmg>
```
> /dev/disk2          	Apple_partition_scheme

> /dev/disk2s1        	Apple_partition_map

> /dev/disk2s2        	Apple_Driver_ATAPI

> /dev/disk2s3        	Apple_HFS

```
sudo mount_hfs -o ro /dev/disk2s3 /Volumes/<your-mount-folder>/
```
(the device which contains the Apple_HFS FS may vary with your machine or DMG. You must create the mount folder first.)

Explanation: You may have "turned off" fsck_hfs because you didn't want it to screw up your possibly incorrectly ejected USB drives, or also your intact ones. Congratulations! You just have gained back a tiny bit of control over your MacBook Pro and its MacOS X! Please leave it that way!
If you're however trying to open any installer package now you will run into a problem:

> The following disk images couldn't be opened

> Reason: no mountable file systems

The solution to it is following the steps mentioned to manually mount your DMG.
In Finder you can now open /Volumes/<your-mount-folder/> and open the installer package as usual.

# ~~Unpack PKG in PKG (nested PKG)~~
# Extract "compressed" PKG
```
pkgutil --expand-full <some-pkg.pkg> ./<destination-dir>
```
Explanation: You want to peek into some pkg but you can't because ~~the real deal is contained within an inner pkg which you cannot seem to browse in~~ Finder doesn't show an entry "Show Package Contents" in the PKG's contextual menu. You cannot or you don't want to install the pkg either to retrieve the contents of the inner pkg the normal way. Reason is that this time the pkg is not a directory but an archive. You must extract it to reveal its contents.

That's where the (full) expansion switch comes in handy.

On a sidenote: For me, issuing just "pkgutil --expand <some-pkg.pkg> ./<destination-dir>" worked equally well...

# How hex diff files in macOS High Sierra
```
$ brew install colordiff
$ colordiff --side-by-side --suppress-common-lines <(xxd -g 1 file1.bin) <(xxd -g 1 file2.bin)
```

# How diff branches (untested!)
```
$ git diff <path1> <path2>
```

# How find subfolder usage in path (~~partially~~fully tested!)
```
$ du -cxhd 1 <path>
```

# How git on github

```
$ git clone https://github.com/<username>/<meaningful-repo>.git
$ cd ./<meaningful-repo>
(modify file(s))
$ git add .
($ git reset <some-file>) # undo add
($ git diff --cached)     # Figure out what the differences are in the first place
$ git commit -m "some meaningful msg"
$ git remote set-url origin "https://<username>@github.com/<username>/<meaningful-repo>.git" # might not work if not once logged in to github ==> 2fa
$ git push origin <branch-name>
(modify file(s) remote)
$ git fetch origin
```

# Recursively chown files
```
$ sudo chown -R $(whoami) <dir>
```

# How get coder chars (SWISS Layout)
* Backslash: {Option} + {Shift} + {7}
* Tilde: {Option} + {n}

# How compile C hello world into Macho 64 executable using XCode 10, while making the intermediate assembly (Intel syntax <3) step accessible
```
$ clang -fno-stack-protector -masm=intel test0.c -S
$ mv ./test0.s test0.asm
############################################################
### (At this point you can modify the assembly at will!) ###
############################################################
$ as test0.asm -o test0.o
$ ld -lc -macosx_version_min 10.13 -o test0 test0.o
```

# How recursively list all regular files sorted by date
* Find all "regular files (-type f)" that are at least 16 bytes in size
* When printing the current file path terminate with a \x00 instead of any other EOL character
* Have xargs(1) read the current line until the 0 character is encountered as opposed to e.g. space character
* Have ls(1) parameters such that it prints the full path for the current file, which supplied by xargs(1) command
* Have it print the time in sort-friendly ISO format
* Replace all but the date and the full path by a (space) delimiter and print the result
* Have cut(1) remove all replacement delimiters (in this example there were 4 leading ones in addition to the 6th and the 7th (time and timezone))
* Have sort(1) interpret the result numeric
```
$ find . \( -type f -size +16c \) -print0 | xargs --null ls -oAd --full-time | awk '{$1=""; $2=""; $3=""; $4=""; $6=""; $7=""; print}' | cut -d" " -f5,8- | sort -n
```

# How create multiple partitions in MBR partition scheme using diskutil
(Warning: This erases everything!)
```
diskutil partitionDisk disk<n> MBR "<filesystem personality1>" <NAME1> <size1> "<filesystem personality2>" <NAME2> <size2> "<filesystem personality3>" <NAME3> R
```
e.g.
```
$ diskutil partitionDisk disk1 MBR "MS-DOS FAT16" MSDFAT16 $((32*1024*1024))B "MS-DOS FAT32" MSDFAT32 R
```

# How verbose boot into macOS Recovery
Press Cmd+r+v at the same time

# Better way of crippling fsck_xxx
1. Reboot in macOS Recovery
2. Find fsck_xxx as follows
```
# cd /
# find . | grep -i fsck_
```
3. Remove by true(1)
```
# cd <folder containing currently considered fsck_xxx>
# mv <currently considered fsck_xxx> <currently considered fsck_xxx_d>
# mv true <currently considered fsck_xxx>
```
