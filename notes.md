# Recursively clean MacOS sierra+ notarization crap
xattr -rc /some-malware-dir/

(this remove all extended attributes and thus all notarization info from downloaded files/executables)

Explanation: If you download any executable or shell script from internet and you dont execute file but you use any (hex) editor to open/read executable or script file (e.g. /some-malware-dir/some_malware_install.sh) then macos notarization won't let you. Unfortunately Mac cares not if you open or execute file. It still shows same message like "some_malware_install.sh can't be opened because it is from an unidentified developer". The info that a file is from internet is in some extended attributes.
Today it is well known you can always override and open anyways in security settings.
But if you do that it won't simply open hex editor and let you inspect file BUT INSTEAD it straight EXECUTES file you only wanted to INSPECT!!

This is very dangerous so only solution is prevent notarization crap from popping up in the first place by applying above solution.

# Open DMG's if you deliberately crippled fsck_xyz
```
hdiutil attach -nomount <some-dmg.dmg>
```
> /dev/disk2          	Apple_partition_scheme         	
> /dev/disk2s1        	Apple_partition_map            	
> /dev/disk2s2        	Apple_Driver_ATAPI             	
> /dev/disk2s3        	Apple_HFS

```
sudo mount_hfs -o ro </dev/disk2s3 /Volumes/EFIMOUNT/
```
