# clean notarization crap
so it wont actually OPEN the (possibly malicious) bash file you only wanted to INSPECT: xattr -rc /some-malware-dir/
(this remove all extended attributes and thus all notarization info from downloaded files/executables)

is solution to notarization error "can't be opened because it is from an unidentified developer" when trying to simply READ and not EXECUTE e.g. /some-malware-dir/some_malware_install.sh
