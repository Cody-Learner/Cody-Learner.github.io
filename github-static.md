
# **Cody Learner**
![https://github.com/Cody-Learner!](doc5.png  "Examples")

**Sunday, 19. December 2021**

After a few years of using github and github.io for terminal screenshots and images, thought I'd look into taking advantage of the github.io to set up a static web page. 

I learned that jekyll seemed to be the go to tool for the job, so without hesitation, I dove into installing it without knowing anything about it.* After struggling with version incompatibilities, (github is only compatible with an older version than Arch has available in the official repos), I learned a markdown editor would suffice.
 
Next I tried a few markdown editors and ended up settling on [remarkable](http://remarkableapp.github.io/linux.html) . I installed the git version from the AUR repos, [remarkable-git](https://aur.archlinux.org/packages/remarkable-git/) using my home grown AUR helper [aurch](https://github.com/Cody-Learner/aurch) to build and install it.

Remarkable won me over with it's nice usable GUI layout, live preview, and page style options. It has a handful of style options built in, which include 'Github' and 'Metro Vibes Dark'. This means I can use it for both my github README markup and this page. I've struggled for years with the github README.md because I didn't want to use the right tool for the job, a markdown editor. Instead, I used my browser for 'after view' after posting it on github. This process usually ended with dozens of edits, going through the whole git and upload process each time an edit was needed, and burned up a bunch of time in the process.

\* I've since learned there's an AUR version. I may look into that as well.

<br>


This is a bit of recent code I recently wrote for aurch. This code checks the host for AUR updates. I'm posting it as a preview, so I can see how code looks in the markdown. I notice the original indentation is lost in the process. It replaces my preferential use of tabs with more common four spaces.

	check_host_updates(){
	
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
	}
