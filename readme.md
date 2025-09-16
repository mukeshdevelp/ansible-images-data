# ----------------------------------------------
# Ansible Assignment 1: User & Project Management System
# Target Group: dbservers (Ubuntu)
# ----------------------------------------------



## creating groups - 

  ansible dbservers -b -m group -a "name=dev-team state=present"
  <img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/2b48d679-d7ca-446d-b6c2-b6664026bc4f" />



  ansible dbservers -b -m group -a "name=devops-team state=present"
  <img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/6602d8ec-ca81-4045-bbde-11658275d6ed" />

  ansible dbservers -b -m group -a "name=admin-group state=present"
  <img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/39e36e5e-af69-428a-be4d-69ed3279f48d" />

final result on host- 




<img width="355" height="60" alt="Screenshot from 2025-09-16 16-30-39" src="https://github.com/user-attachments/assets/ddc1293f-bf5c-4896-a84a-12ceb4edc650" />


## Creating Users with custom UIDs and shells
  # Dev Team
  ansible dbservers -b -m user -a "name=devuser1 uid=2000 group=dev-team shell=/bin/bash"
  
  ansible dbservers -b -m user -a "name=devuser2 uid=2001 group=dev-team shell=/bin/bash"
  
  ansible dbservers -b -m user -a "name=devuser3 uid=2002 group=dev-team shell=/bin/bash"
  
  <img width="1228" height="387" alt="user_1_and_3_creation" src="https://github.com/user-attachments/assets/160f86dd-e62c-4f87-b9c8-2d8bb1f60b53" />
  
  # DevOps Team
  ansible dbservers -b -m user -a "name=devops1 uid=2003 group=devops-team shell=/bin/zsh"
  <img width="1173" height="244" alt="user3" src="https://github.com/user-attachments/assets/42025891-fdb9-401e-b9ad-b30a70e2cbf5" />
  
  ansible dbservers -b -m user -a "name=devops2 uid=2004 group=devops-team shell=/bin/zsh"
  
  
  ansible dbservers -b -m user -a "name=devops3 uid=2005 group=devops-team shell=/bin/zsh"
  <img width="1363" height="335" alt="zsh shell" src="https://github.com/user-attachments/assets/04523eed-5923-4073-b4ed-f85a252b0ce0" />

  # Admin Team
  ansible dbservers -b -m user -a "name=admin1 uid=2006 group=admin-group shell=/bin/sh"

  
  ansible dbservers -b -m user -a "name=admin2 uid=2007 group=admin-group shell=/bin/sh"
  
  
  ansible dbservers -b -m user -a "name=admin3 uid=2008 group=admin-group shell=/bin/sh"
  <img width="1289" height="278" alt="admin shell" src="https://github.com/user-attachments/assets/a443466e-465b-410e-9aa3-b22069478d66" />

## Setting password expiry policies..."
  for user in devuser1 devuser2 devuser3 devops1 devops2 devops3 admin1 admin2 admin3; do
    ansible dbservers -b -m command -a "chage -M 60 -m 7 -W 10 $user"
  done

<img width="1366" height="768" alt="passwd expiry" src="https://github.com/user-attachments/assets/852ccebd-52d0-40e5-9be2-93dadf64d6f2" />

## Setting up sudo access for DevOps and Admin groups
    
  ansible dbservers -b -m copy -a "dest=/etc/sudoers.d/devops-team content='%devops-team ALL=(ALL) NOPASSWD: ALL' mode=0440"
  
  
  ansible dbservers -b -m copy -a "dest=/etc/sudoers.d/admin-group content='%admin-group ALL=(ALL) NOPASSWD: ALL' mode=0440"
  <img width="1115" height="416" alt="sudoers" src="https://github.com/user-attachments/assets/fa212cec-a770-4f14-9170-209b4f997eb5" />


## Creating team collaboration directories
  ansible dbservers -b -m file -a "path=/teams/dev-team state=directory mode=2775 group=dev-team"
  <img width="1103" height="244" alt="teams" src="https://github.com/user-attachments/assets/bea1971f-f35d-4616-a75b-eb328e24980f" />

  
  ansible dbservers -b -m file -a "path=/teams/devops-team state=directory mode=2775 group=devops-team"
  
  <img width="1105" height="189" alt="teams (1)" src="https://github.com/user-attachments/assets/eb39057b-b84b-46ae-81c4-a7026c783a18" />

## Creating project-specific directories
  ansible dbservers -b -m file -a "path=/projects/WebApp state=directory owner=devuser1 group=dev-team mode=2770"
  
  
  ansible dbservers -b -m file -a "path=/projects/API state=directory owner=devops1 group=devops-team mode=2770"
  
  
  ansible dbservers -b -m file -a "path=/projects/Mobile state=directory owner=admin1 group=admin-group mode=2770"

## Creating shared and archive directories
  ansible dbservers -b -m file -a "path=/shared_resources state=directory mode=2777"
  
  
  ansible dbservers -b -m file -a "path=/archive state=directory mode=0555"

## Creating admin-only area

  ansible dbservers -b -m file -a "path=/admin_area state=directory owner=root group=admin-group mode=0770"
## Setting ACLs for user home directories (read-only for their teams)
  ansible dbservers -b -m acl -a "path=/home/devuser1 entity=dev-team etype=group permissions=r state=present"
  
  ansible dbservers -b -m acl -a "path=/home/devuser2 entity=dev-team etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/devuser3 entity=dev-team etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/devops1 entity=devops-team etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/devops2 entity=devops-team etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/devops3 entity=devops-team etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/admin1 entity=admin-group etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/admin2 entity=admin-group etype=group permissions=r state=present"
  
  
  ansible dbservers -b -m acl -a "path=/home/admin3 entity=admin-group etype=group permissions=r state=present"

## Setting ACLs for team directories (read for other teams)
  ansible dbservers -b -m acl -a "path=/teams/dev-team entity=devops-team etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/teams/dev-team entity=admin-group etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/teams/devops-team entity=dev-team etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/teams/devops-team entity=admin-group etype=group permissions=rx state=present"

## Setting ACLs for project directories (cross-team read access)
  # WebApp
  ansible dbservers -b -m acl -a "path=/projects/WebApp entity=devops-team etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/projects/WebApp entity=admin-group etype=group permissions=rx state=present"

  <img width="1141" height="273" alt="projects" src="https://github.com/user-attachments/assets/790f843d-53b2-43de-aa86-8c05dd7b8653" />

  # API
  ansible dbservers -b -m acl -a "path=/projects/API entity=dev-team etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/projects/API entity=admin-group etype=group permissions=rx state=present"
  
  # Mobile
  
  ansible dbservers -b -m acl -a "path=/projects/Mobile entity=dev-team etype=group permissions=rx state=present"
  
  
  ansible dbservers -b -m acl -a "path=/projects/Mobile entity=devops-team etype=group permissions=rx state=present"

  ## final results
  # Dir structure - for users
  <img width="1046" height="228" alt="final dir" src="https://github.com/user-attachments/assets/f6107206-f96e-4377-94ba-9356a1a19ebb" />
  # Dir Structure - projects, teams, archive and others
  <img width="1040" height="138" alt="Screenshot from 2025-09-16 17-03-45" src="https://github.com/user-attachments/assets/e33260bb-cba5-4999-938d-0192ec5bb4cc" />

  # All groups and teams
  <img width="628" height="169" alt="Screenshot from 2025-09-16 16-31-28" src="https://github.com/user-attachments/assets/e384abf4-8f7f-42a4-b698-aaa8801e0492" />

