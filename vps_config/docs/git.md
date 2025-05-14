## Configuring Git

By default git is already installed. Execute the following lines for configuration.

```
git config --global user.name "Full Name"
git config --global user.email "Email Address"
git config --global core.editor nano
git config --global color.ui true
```

To show all configuration run:

```sh
git config --list
```

To count total number of commits:

```sh
git rev-list --all --count
```

Configuration for remote repository

```sh
git remote add origin "url.git"
git remote -v
git pull origin master
git push origin master
```

In case of error: "fatal: refusing to merge unrelated histories" use `--allow-unrelated-histories` flag. For more details run: `man git`.

