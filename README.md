# Playbooks to help deploying Rails app

### Install requirements

```
ansible-galaxy install -r requirements.yml
```

### Setup the server to run a new Rails application 
As a convention, we use ports 3001-3999 for our Rails application because Puma by default is running on port 3000. For each application, you will need to make sure that the port is free. In addition, we will also use the port as the UID/GID for the user of the application, you will also need to ensure that the UID/GID is not in used. The app name will be used as the username and primary group name, and the application will be in /home/app/app.

This example shows how to create a new Rails application using Rbenv, Puma and systemd.
The add_rails_app.yml playbook will create a user fooapp with uid/gid: 3001, install rbenv in the fooapp user's home directory, install ruby version 3.1.2, create a systemd unit file fooapp.service that will run Puma on port 3001.

```
ansible-playbook add_rails_app.yml -e "rails_env=production app=fooapp puma_port=3001 ruby_version=3.1.2 app_domain=myapp.ca"
```

Once the setup is complete, you can login to the server as user fooapp to create your new Rails app. Your pubkey should have already been added to /home/fooapp/.ssh/authorized_keys.

Now your server is setup and ready for you to run your Rails application on it.

### Install a Rails app from git repo

Suppose you have prepared your server to run the new Rails app as above, and you have created a blank MySQL database **fooapp** on your MySQL database server **mysqlhost** for the app already. You can install the new app using the following playbook.

```
ansible-playbook -i inventory install_rails_app.yml -e"rails_env=production app=fooapp repo_branch=master repo=https://github.com/yorkulibraries/fooapp.git mysql_database=fooapp mysql_username=fooapp mysql_password=fooapp mysql_host=mysqlhost" --limit targethost
```

### Update an existing Rails app 
Assuming you have deployed an app **fooapp** using the above method, and the git branch used is **master**.

```
ansible-playbook -i inventory update_rails_app.yml -e"rails_env=production app=fooapp repo_branch=master" --limit targethost
```
