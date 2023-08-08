# Terminal

```

# sorted by modified

find . -type f -not -path "*/__pycache__/*" -printf "%TY-%Tm-%Td %TT %p\n" | sort -n

# sorted by modified reverse

find . -type f -not -path "*/__pycache__/*" -printf "%TY-%Tm-%Td %TT %p\n" | sort -nr

# with sub dirs

ls -ltR

# list of file names into txt file e.g. to create bundle.js

find . --include=\*.js -print > all_files.txt


```
