
### Digital Ocean Ubuntu 16.04 LTS OpenVPN + Stouts.openvpn

This playbook is indended to very quickly pop up an OpenVPN VPN server.

**When you run Stouts.openvpn for the first time, you will need to restart the computer before you can connect to the server. I don't know why.**


#### Guide

1. [Make an account and get a $10 free credit at digital ocean](https://m.do.co/c/a4d54c9e5004)

2. Add your `SSH key` to `Digital Ocean`
    1. Enter the digital ocean web interface
    2. go to `settings->security->SSH keys`
    3. Paste your key, found by default `$HOME/.ssh/id_rsa.pub`
        - tip: make a key with `ssh-keygen`
    4. On server creation, the key will be added in server's `root` user's `authorized_keys`
    5. `ansible` reads from the default `id_rsa.pub` location
        - you can set a different location as a command line argument in `manage.sh`

3. Create a new server on Digital Ocean
    1. Choose the `Ubuntu 16.04.1 x64` base image 
    2. Use the $5 option
    3. Choose a datacenter near you for low latency.
    4. Check your preferred `ssh key` under `Add your SSH keys`
    5. Choose a hostname like `popup-openvpn`
    6. Notes
        - You will have 1 TB monthly transfer
        - They call a `server` a `droplet`

4. Configure this repo for your users
    1. Clone this repository on your local computer
        - `git clone git@github.com:robbintt/popup-openvpn.git`
    1. Configuration files: 
        - `hosts` 
        - `group_vars/stoutsopenvpn`
    2. Add the IP address of the server to your `hosts` file
        - This is available in your digital ocean dashboard
    3. Add each of the following to your `group_vars/Stoutsopenvpn`
        - `user` 
        - `password`
        - `openvpn_client` 

5. It's time to automatically set things up!!
    1. Install `ansible` locally
        - Optional: do this using your preferred method
        - `todo` (pip method is cross platform)
    2. execute: `manage.sh`
        - The ansible script should run without any errors
        - (The restarter role fails if it doesn't need to restart or something)
    3. At the end of the script your server needs restarted. I don't know why
        - todo - investigate if this was a one time thing or is always true...
        - if so have the ansible script always restart?
    4. To reconfigure or update your server you may run `manage.sh` again
5. Assemble a .ovpn file on your server and put it on your devices
    1. TODO 



#### Todo

Make sure to reference your digital ocean referral code in the guide and at the top of this document. Free server money is great.

1. Add an automatic updates tool.
2. Move Stouts.openvpn default to 2048 bit certificate.
    - Add variables in `group_vars/Stoutsopenvpn`
3. Test this guide from very beginning to very end.
    - Beginning: Create a Digital Ocean Account
    - End: Install the ovpn cert and put the password in on all your devices
    - Very End: Maintaining your server
4. Add a guide from very beginning to very end
5. Create per-device table of recommended OpenVPN clients
6. Ask for suggestions and additions from noisebridge
7. Deploy to the world at large (reddit?)
8. Maybe write a digital ocean article.


#### Notes for todo

1. Default: root cannot login with a password over ssh
    - This means the only valid credential is an ssh key


#### Requirements

- Server: Digital Ocean Ubuntu 16.04.1 Release
- Local `~/.ssh/id_rsa.pub` private key deployed to remote root `authorized_keys`
    - This is done through Digital Ocean on droplet deployment


#### Stouts.openvpn

The [Stouts.openvpn](https://github.com/Stouts/Stouts.openvpn) repo is included as a subrepo in `/roles/Stoutsopenvpn`. 


#### Manually building your `ovpn` for deploying keys to IOS

You will be converting four files into one file using a xml type syntax. Follow the appropriate section of the [DigitalOcean Guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-14-04)


#### Future

1. rebooter isn't working for some reason. The sleep command probably needs to be a shell...
2. set [tzdata](https://gist.github.com/jerm/fc7f33f6a6d6534f6fde)!! already did this on some hosts so make it idempotent.
3. See if you can move the `restart wait_for` to a `handler` so it doesn't always hang 15 seconds.
    - or add a `when` condition?
4. Review [Best Practices](http://docs.ansible.com/ansible/playbooks_best_practices.html)


#### References

Some useful links and additions.


#### Using Ansible Vault

You might use this to encrypt your `group_vars/Stoutsopenvpn`

- Encrypt a file: `ansible-vault encrypt foo.yml bar.yml baz.yml`
- Decrypt a file: `ansible-vault decrypt foo.yml bar.yml baz.yml`
- View file contents: `ansible-vault view foo.yml`
- Run Playbook w/ Vault password: `ansible-playbook site.yml --ask-vault-pass`
    - This is managed in `manage.sh` in this script


##### Links

- [YAML Syntax Details](http://docs.ansible.com/ansible/YAMLSyntax.html)

- [Ansible Homebrew](http://docs.ansible.com/ansible/homebrew_module.html) exists...

- Ansible Modules Used:
    - [Apt](http://docs.ansible.com/ansible/apt_module.html)
    - [User](http://docs.ansible.com/ansible/user_module.html)
    - [Command](http://docs.ansible.com/ansible/command_module.html#command)
    - [Shell](http://docs.ansible.com/ansible/shell_module.html)
    - [Raw](http://docs.ansible.com/ansible/raw_module.html)
    - [lineinfile](http://docs.ansible.com/ansible/lineinfile_module.html)
    - [apache2_module](http://docs.ansible.com/ansible/apache2_module_module.html)
    - [Vault](http://docs.ansible.com/ansible/playbooks_vault.html)
    - [Copy](http://docs.ansible.com/ansible/copy_module.html)
    - [Git](http://docs.ansible.com/ansible/git_module.html)
    - [Become](http://docs.ansible.com/ansible/become.html)
    - [mysql_user](http://docs.ansible.com/ansible/mysql_user_module.html)
    - [get_url](http://docs.ansible.com/ansible/get_url_module.html)
    - [unarchive](http://docs.ansible.com/ansible/unarchive_module.html)

- [YAML Syntax](http://docs.ansible.com/ansible/YAMLSyntax.html)
- [Patterns](http://docs.ansible.com/ansible/intro_patterns.html) - decide which hosts to manage
    - grep for this to see a good example in a task
- [Best Practices](http://docs.ansible.com/ansible/playbooks_best_practices.html)


