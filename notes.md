# notes

## More reference links
* [online guid generator](https://guidgenerator.com/)

## Create fzpz part file
```
$ cd part
$ zip -u ../testPart.fzpz *.svg *.fzp
```

## file paths
```
~/.config/Fritzing/bins/*.fzb
~/.config/Fritzing/parts/svg/user/«view»/*.svg
~/.config/Fritzing/parts/user/*.fzp
/usr/share/fritzing/bins/*.fzb
/usr/share/fritzing/bins/*/*.fzb
/usr/share/fritzing/parts/svg/«category»/«view»/*.svg
```

## Finding files
```
$ find /usr/share/fritzing/ -iname "*dip_relay*" -type f -print
```
