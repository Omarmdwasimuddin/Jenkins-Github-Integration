## Jenkins-Github-Integration

#### click: Manage Jenkins--->click: Plugins--->click: Install plugins--->search: git plugin [git plugin thakle thik ache]
#### github e repo create koro, nam daw Test-Jenkins, public rakho.
#### Jenkins er New Item click koro--->Enter an item name: Github Jenkins--->select: Freestyle project--->click: OK
#### click: Source Code Management--->click: Git--->paste koro github repo url--->Branch Specifier (blank for 'any') e daw: */main--->Triggers e select koro: GitHub hook trigger for GITScm polling?--->Build Steps e click koro: Add build step--->select koro: Execute Windows batch command?--->Command: echo Build triggered from GitHub!--->click: Save
#### Browser e search koro: ngrok download ba visit koro: https://ngrok.com/download/windows--->Download select koro--->Download button click koro--->Download hoyar por zip file ke extract all koro--->file click koro--->cmd open hobe.
#### abar browser er https://ngrok.com/download/windows?tab=download jaw Add your authtoken er Sign up e click koro--->process gulo complete kore sign up korle authtoken send korbe
####
