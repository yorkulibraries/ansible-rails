# Playbooks to help deploying Rails app

### Install requirements

```
ansible-galaxy install -r requirements.yml
```

### Setup a new Rails application 
As a convention, we use ports 3001-3999 for our Rails application because Puma by default is running on port 3000. For each application, you will need to make sure that the port is free. In addition, we will also use the port as the UID/GID for the user of the application, you will also need to ensure that the UID/GID is not in used. The app name will be used as the username and primary group name, and the application will be in /home/app/app.

This example shows how to create a new Rails application using Rbenv, Puma and systemd.
The add_rails_app.yml playbook will create a user fooapp with uid/gid: 3001, install rbenv in the fooapp user's home directory, install ruby version 3.1.2, create a systemd unit file fooapp.service that will run Puma on port 3001.

```
ansible-playbook add_rails_app.yml -e "app=fooapp puma_port=3001 ruby_version=3.1.2 app_domain=myapp.ca rails_env=production"
```

Once the setup is complete, you can login to the server as user fooapp to create your new Rails app. Your pubkey should have already been added to /home/fooapp/.ssh/authorized_keys.

```
ssh fooapp@demos
fooapp@demos:~$ gem install rails
fooapp@demos:~$ rails new fooapp
```

Your Rails app will be in /home/fooapp/fooapp directory, test that the application would run:

```
fooapp@demos:~$ cd /home/fooapp/fooapp
fooapp@demos:~/fooapp$ rails server -e production -p 3001
```



If all is well, you can start the application as a systemd unit as followed:
```
sudo systemctl start fooapp
sudo systemctl status fooapp
```
You should be able to open your browser to http://fooapp.myapp.ca/ to see the app running.

### Update an existing Rails app 
Assuming you have deployed an app **fooapp** using the above method, and the git branch used is **master**.

```
ansible-playbook -i inventory update_rails_app.yml -e"app=fooapp repo_branch=master" --limit targethost
```
