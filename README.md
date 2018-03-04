# Install Docker + Docker Compose in Ubuntu

## Machine prerequisites:

Since we're using Ansible, we need to preinstall on all the hosts a version of Python.
For Ubuntu servers, specifically 17.10, `python3` is installed, so you can use it in your
inventory file by adding `ansible_python_interpreter=/usr/bin/python3` to the host line
you want to control with Python 3.

Alternatively, you can run the following command to install Python 2 and the required
Ansible dependencies on all machines:

```
ansible --sudo -m raw -a "yum install -y python2 python-simplejson" -i hosts
```

Replace `-i hosts` with the location of the Inventory file or remove it to use
`/etc/ansible/hosts`.

Additionally, passwordless SSH is recommended, and you can configure usernames and `become`
from your Ansible Inventory file, although `root` or an account with root permissions is
required (hence the `become: true`) in the yaml file.

To achieve passwordless SSH, log in to both your control machine and the host machine. In the
host machine, copy the `~/.ssh/id_rsa.pub` file to your clipboard, then paste the contents
into the managed machine to `~/.ssh/authorized_keys`. If the folder isn't there or the file
doesn't exist create both, and also set the `chmod` of the `~/.ssh` folder to 700 and the
`authorized_keys` file to `640`.

You can specify a custom inventory file other than `/etc/ansible/hosts` by passing the `-i`
argument to your calls. You can test connectivity to your machines being set up by using
`ansible -m ping all` to see if you can ping them. You should see an output like follows:

```
myhost.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Of course I shouldn't mention it but Ansible 2.4 or above should be installed on the control
machine. You can get them from [the appropriate sources](http://docs.ansible.com/ansible/latest/intro_installation.html)
or installing it with `pip` by running `sudo pip install ansible`.

## Running this playbook

The playbook file is named `ubuntu-docker-and-compose.yml` which you can run on one or
more hosts by executing:

```
ansible-playbook -i hosts ubuntu-docker-and-compose.yml
```

Where `-i hosts` uses an Inventory file called `hosts` in the same directory the YAML file
is. You can additionally pass `--check` to do a dry-run of the playbook actions and see if
they suit your needs.

## Additionally installed packages

Besides `docker` and `docker-compose`, there are a couple of other packages installed for convenience:

* `ufw` set up to deny incoming, allow outgoing and allow incoming on 22, 80 and 443
* `fail2ban` with the default configuration, it'll at least leave those hackers out
* `curl` so you can do those juicy `curl` downloads
