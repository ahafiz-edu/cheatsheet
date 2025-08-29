



### Managing Custom Repo
**adding DNF repo**         : `dnf config-manager --add-repo <repo_URL>`
**removing DNF repo**       : `rm -f /etc/yum.repos.d/<repo_ID>.repo && dnf makecache`
**enabling DNF repo**       : `dnf config-manager --enable <repo_ID>`
**disabling DNF repo**      : `dnf config-manager --disable <repo_ID>`
