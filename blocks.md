#### Agree standards for the use of comments and names used on blocks/module calls within playbooks.

>The following points will be used to raise discussion. Additional comments welcome.

>•	All code migrated from Projects within the lab must contain a Prologue. 
>• The Prologue should be completed with any details deemed useful from a code perspective.
>• If Running or expected data structures are complex or limited debugging is displayed within the code, then it’s useful to populate them here.
>Use the following format.  
>```
>---
># PROLOG:
>#  Original Author: Steve Paynter
>#  Date           : 16/09/2022
>#  Desc           : Template used to deploy job templates from Gitlab SOT
>#  Git Source     : ansibletower_sre/sre_adhoc/-/edit/main/SOT_deploy_job_template.yml
>#
># ENV:
>#  AAP Template   : True - 
>#  AAP Workflow   : False - 
>#  Ansible Core   : False - 
>#  EE             : DWP_DEFAULT
>#  Ansible Version: 2.9 at least
>#
># USEAGE: 
>#  Running        : 
>#                 : 
>#   
>#  Expected:      : 
>#                 
>#
>#  Requirements   : N/A
>#
>#
>#  Notes          : N/A
>#
>#
># DONT MAKE CHANGES TO THIS SCRIPT LOCALLY AS THIS IS MANAGED BY GIT. (see above)
>```
> ### Code comments.  
>We have already been gradually adopting this process. Use common sense.  
>There is little use in adding comments before a debug command.  
>However, if complex loops or variables are being parsed and fed into a play, then document this in a comment prior to the code with a useful description.>In 
>Comments shcould explain WHY we're doing something, in a simliar fashion to Git commenting.
>There is littel or no point in mentioning WHAT we are doing, as this is visible in the code to the reader.
>
>Use the following format.
>
>```
> # Add a comment in this format. Dont extend lines to far that it becomes unreadable.
> # Spread them over multiple lines.
>```
>
>### Naming Standards
>All plays should be named with a relevant name. First, as this is required to pass linting and secondly it assists in helping others when reading code.
>It may also be worth numbering each play. This can assist in troubleshooting code when failures occur within Job Template outputs.
>
>```
>    - name: "0.0: Setup Project Token For Gitlab in EE"
>      set_fact:
>        _project_token: '{{ lookup("env", "GITLAB_TOKEN") }}'
>```
