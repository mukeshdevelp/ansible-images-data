# ----------------------------------------------
# Ansible Assignment 1: User & Project Management System
# Target Group: dbservers (Ubuntu)
# ----------------------------------------------
# some naming mofication needed. do not execute it


# creating groups - 

ansible dbservers -b -m group -a "name=dev-team state=present"
<img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/2b48d679-d7ca-446d-b6c2-b6664026bc4f" />



ansible dbservers -b -m group -a "name=devops-team state=present"
<img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/6602d8ec-ca81-4045-bbde-11658275d6ed" />

ansible dbservers -b -m group -a "name=admin-group state=present"
<img width="761" height="221" alt="image" src="https://github.com/user-attachments/assets/39e36e5e-af69-428a-be4d-69ed3279f48d" />



echo "==> Creating Users with custom UIDs and shells..."
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

# Admin Team
ansible dbservers -b -m user -a "name=admin1 uid=2006 group=admin-group shell=/bin/sh"
ansible dbservers -b -m user -a "name=admin2 uid=2007 group=admin-group shell=/bin/sh"
ansible dbservers -b -m user -a "name=admin3 uid=2008 group=admin-group shell=/bin/sh"

## Setting password expiry policies..."
for user in devuser1 devuser2 devuser3 devops1 devops2 devops3 admin1 admin2 admin3; do
  ansible dbservers -b -m command -a "chage -M 60 -m 7 -W 10 $user"
done

echo "==> Setting up sudo access for DevOps and Admin groups..."
ansible dbservers -b -m copy -a "dest=/etc/sudoers.d/devops-team content='%devops-team ALL=(ALL) NOPASSWD: ALL' mode=0440"
ansible dbservers -b -m copy -a "dest=/etc/sudoers.d/admin-group content='%admin-group ALL=(ALL) NOPASSWD: ALL' mode=0440"

echo "==> Creating team collaboration directories..."
ansible dbservers -b -m file -a "path=/teams/dev-team state=directory mode=2775 group=dev-team"
ansible dbservers -b -m file -a "path=/teams/devops-team state=directory mode=2775 group=devops-team"

echo "==> Creating project-specific directories..."
ansible dbservers -b -m file -a "path=/projects/WebApp state=directory owner=devuser1 group=dev-team mode=2770"
ansible dbservers -b -m file -a "path=/projects/API state=directory owner=devops1 group=devops-team mode=2770"
ansible dbservers -b -m file -a "path=/projects/Mobile state=directory owner=admin1 group=admin-group mode=2770"

echo "==> Creating shared and archive directories..."
ansible dbservers -b -m file -a "path=/shared_resources state=directory mode=2777"
ansible dbservers -b -m file -a "path=/archive state=directory mode=0555"

echo "==> Creating admin-only area..."

ansible dbservers -b -m file -a "path=/admin_area state=directory owner=root group=admin-group mode=0770"

echo "==> Setting ACLs for user home directories (read-only for their teams)..."
ansible dbservers -b -m acl -a "path=/home/devuser1 entity=dev-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/devuser2 entity=dev-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/devuser3 entity=dev-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/devops1 entity=devops-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/devops2 entity=devops-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/devops3 entity=devops-team etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/admin1 entity=admin-group etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/admin2 entity=admin-group etype=group permissions=r state=present"
ansible dbservers -b -m acl -a "path=/home/admin3 entity=admin-group etype=group permissions=r state=present"

echo "==> Setting ACLs for team directories (read for other teams)..."
ansible dbservers -b -m acl -a "path=/teams/dev-team entity=devops-team etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/teams/dev-team entity=admin-group etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/teams/devops-team entity=dev-team etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/teams/devops-team entity=admin-group etype=group permissions=rx state=present"

echo "==> Setting ACLs for project directories (cross-team read access)..."
# WebApp
ansible dbservers -b -m acl -a "path=/projects/WebApp entity=devops-team etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/projects/WebApp entity=admin-group etype=group permissions=rx state=present"
# API
ansible dbservers -b -m acl -a "path=/projects/API entity=dev-team etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/projects/API entity=admin-group etype=group permissions=rx state=present"
# Mobile
ansible dbservers -b -m acl -a "path=/projects/Mobile entity=dev-team etype=group permissions=rx state=present"
ansible dbservers -b -m acl -a "path=/projects/Mobile entity=devops-team etype=group permissions=rx state=present"
