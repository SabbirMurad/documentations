## Customizing Bash Prompt

In BASH, we can customize and change the BASH prompt as the way you want by changing the value of **PS1** environment variable.

Customize the **root account** prompt by editing `nano ~/.bashrc` as root and paste the following lines at the end of the file:

```
#Custom color for PS1.

CYAN="\e[0;36m"
BLUE="\e[0;34m"
WHITE="\e[00m"
CEND="\e[m"

RED_BACKGROUND="\e[41m"
GREEN_BACKGROUND="\e[42m"

PS1="\[$WHITE\]\[$RED_BACKGROUND\]\u\[$WHITE\]@\[$CYAN\]\h: \[$BLUE\]\w\[$CEND\]>> "
```

Customize the **user account** prompt by editing `nano ~/.bashrc` as user and paste the following lines at the end of the file:

```
#Custom color for PS1.

CYAN="\e[0;36m"
BLUE="\e[0;34m"
WHITE="\e[00m"
CEND="\e[m"

LIGHT_RED="\e[91m"
LIGHT_GREEN="\e[92m"

PS1="\[$WHITE\]\[$LIGHT_GREEN\]\u\[$WHITE\]@\[$CYAN\]\h: \[$BLUE\]\w\[$CEND\]>> "
```
