# bash:tipsntricks

This is a collections of useful bash shell tips and tricks I've learned over the years.

``` shell title="$_ lists everything after the last space"
$ ls /etc/nginx/conf.d
$ echo $_
/etc/nginx/conf.d
```

---


``` bash title="add color to scripts"
color_output() {
    local color=$1
    local text=$2
    case $color in
        red) echo -e "\033[31m$text\033[0m" ;;
        green) echo -e "\033[32m$text\033[0m" ;;
        yellow) echo -e "\033[33m$text\033[0m" ;;
        blue) echo -e "\033[34m$text\033[0m" ;;
        purple) echo -e "\033[35m$text\033[0m" ;;
        cyan) echo -e "\033[36m$text\033[0m" ;;
        *) echo "$text" ;;
    esac
}

echo "$(color_output red 'This text would display in red.')"
echo "$(color_output green 'This text would display in green.')"
echo "$(color_output blue 'This text would display in blue.')"
echo "$(color_output yellow 'This text would display in yellow.')"

```
<figure markdown>
![example of output](/assets/images/linux/bashcolorprompt.png){ loading=lazy }
  <figcaption>example of output</figcaption>
</figure>

---
