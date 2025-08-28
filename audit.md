audit rule = tell kernel auditd subsystem what events to capture

### What can audir rules capture ?
- syscall rules : track specific system calls, often filtered by UID, GID, path, or other fields
- filesystem / path rules : watch files or directories for access, modification, attribute changes
- control rules : change behavior of auditing itself, limits, buffers, immutable mode

### Rule Construction
- path watch syntax
```
-w <path> -p <permissions> -k <key>
```
- syscall rule syntax
```
-a action,filter -S <syscall> -F <field=value> -k <key>
```

### Path watch rules
- `-w <path>` : watch a file or directory
- `-p <permission>` : filter on access type (r,w,x,a)
- `-k <key>` : tag the rule with a search key

### syscall rules (action and filters)
- `-a action,filter` : add a rule with action + filter
	- `always,exit` : always log on syscall exit
	- `never` : ignore
	- `user,exit` : log only for user-space processes
- `-S syscall` : which syscall to watch (`open`, `execve`, `chmod`)
- `-F field=value` : filters for narrowing down events
	- common fields:
	- `arch=b64` / `arch=b32` : syscall architecture
	- `auid=1000` : audit user ID (real login UID)
	- `uid=0` : effective UID
	- `gid=1000`
	- `path=/etc/shadow`
	- `exe=/usr/bin/passwd`
	- `success=0`
- `-k <key>` : tag the rule
```
-a always,exit -F arch=b64 -S execve -F auid>=1000 -F auid!=4294967295 -k user_exec
```

### control rules (affect audit framework itself)
- `-b <num>` : set buffer size
- `-f <action>` : set failure mode if auditd cant keep up
	- 0 = silent
	- 1 = print failure but continue
	- 2 = panic (kernel lockdown)
- `-e <mode>` : set audit enabled / disabled / immutable
	- 0 = disable
	- 1 = enable
	- 2 = immutable (cant change until reboot)

---
- persistent rules live in `/etc/audit/rules.d/*.rules`
- temporary / runtime rules live in the kernel loaded via `auditctl`
- `augenrules --load` will first flushes all rules from kernel, then rebuild `/etc/audit/audit.rules` and loads them into the kernel

### Delete audit rules
- delete syscall rule
```
auditctl -d always,exit -F arch=b64 -S open -F auid>=1000 -F auid!=unset -k access
```
- delete file watch
```
auditctl -W /etc/passwd -p wa -l passwd_changes
```
- delete all rules
```
auditctl -D
```


