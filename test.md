<body bgcolor="#002B36">
<font color="#6E8992">
## **Cody's Linux Blog**
![alt text](/home/jeff/ls.png)
Tried using ruby jeykll, what a pain in the ass with version incompatibilities!<br>
Going to try straight up markdown...

This is a bit of recent code I wrote for aurch. It checks the host for AUR updates.

 ```
readarray -t aurpkgs < <(pacman -Sl ${REPONAME} 2>/dev/null | awk '{print $2,$3}' ; pacman -Qm )
if	[[ $1 == -Luhq ]]; then	:
    else
	echo; echo "${czm} Checking for updates:"
	printf '%s\n' "${aurpkgs[@]%' '*}" | nl | column -t
fi
	rm -f /tmp/aurch-updates /tmp/aurch-updates-newer

for pkg in "${aurpkgs[@]}"; do {

    	pckg="${pkg%' '*}"	
	check=$(curl --compressed -s "https://aur.archlinux.org/rpc/?v=5\&type=info&arg\[\]=${pckg}" \
		| jshon -e results -a -e  Version \
		| awk -F\" '{print $2}')
	compare=$(vercmp "${pkg#*' '}" "${check}")

	if	[[ -n  ${check} && ${compare} == -1 ]]; then
		echo "${pkg} -> ${check}" >>/tmp/aurch-updates
    	elif	[[ -n  ${check} &&  ${compare} == 1 ]]; then
    		echo "${pkg} <- ${check}" >>/tmp/aurch-updates-newer
	fi } &

done; wait

if	[[ $1 == -Luhq ]]; then
	awk '{print $1}' /tmp/aurch-updates
    else
	echo; echo "${czm} Updates available:"
	 column -t /tmp/aurch-updates
	echo
	if	[[ -s  /tmp/aurch-updates-newer ]]; then
		echo " Newer than update:"
		 column -t /tmp/aurch-updates-newer
		echo
	fi
fi
```
