# **How to Manage Multistage Environments with Ansible** #:smile:
*****

#**Ansible Recommended Strategy: Using Groups and Multiple Inventories**


So far, we’ve looked at some strategies for managing multistage environments and discussed reasons why they may not be a complete solution. However, the Ansible project does offer some suggestions on how best to abstract your infrastructure across environments.

The recommended approach is to work with multistage environments by completely separating each operating environment. Instead of maintaining all of your hosts within a single inventory file, an inventory is maintained for each of your individual environments. Separate group_vars directories are also maintained.

The basic directory structure will look something like this: # :point_down:

```
.
├── ansible.cfg
├── environments/         # Parent directory for our environment-specific directories
│   │
│   ├── dev/              # Contains all files specific to the dev environment
│   │   ├── group_vars/   # dev specific group_vars files
│   │   │   ├── all
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts         # Contains only the hosts in the dev environment
│   │
│   ├── prod/             # Contains all files specific to the prod environment
│   │   ├── group_vars/   # prod specific group_vars files
│   │   │   ├── all
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts         # Contains only the hosts in the prod environment
│   │
│   └── stage/            # Contains all files specific to the stage environment
│       ├── group_vars/   # stage specific group_vars files
│       │   ├── all
│       │   ├── db
│       │   └── web
│       └── hosts         # Contains only the hosts in the stage environment
│
├── playbook.yml
│
└── . . .
```

# *if you wish to know the my simple staging environments setup click the [link](https://github.com/akhilesh9014/Ansible-multi-stage-Role.git) :point_left: button 

actually here i was setup three stages #DEV, #QA, #UAT

As you can see, each environment is distinct and compartmentalized. The environment directories contain an inventory file (arbitrarily named hosts) and a separate group_vars directory.

There is some obvious duplication in the directory tree. There are web and db files for each individual environment. In this case, the duplication is desirable. Variable changes can be rolled out across environments by first modifying variables in one environment and moving them to the next after testing, just as you would with code or configuration changes. The group_vars variables track the current defaults for each environment.

One limitation is the inability to select all hosts by function across environments. Fortunately, this falls into the same category as the variable duplication problem above. While it is occasionally useful to select all of your web servers for a task, you almost always want to roll out changes across your environments one at a time. This helps prevent mistakes from affecting your production environment.

Setting Cross-Environment Variables
One thing that is not possible in the recommended setup is variable sharing across environments. There are a number of ways we could implement cross-environment variable sharing. One of the simplest is to leverage Ansible’s ability to use directories in place of files. We can replace the all file within each group_vars directory with an all directory.

Inside the directory, we can set all environment-specific variables in a file again. We can then create a symbolic link to a file location that contains cross-environment variables. Both of these will be applied to all hosts within the environment.

Begin by creating a cross-environment variables file somewhere in the hierarchy. In this example, we’ll place it in the environments directory. Place all cross-environment variables in that file:
```
cd environments
touch 000_cross_env_vars
Next, move into one of the group_vars directory, rename the all file, and create the all directory. Move the renamed file into the new directory:
```
```
cd dev/group_vars
mv all env_specific
mkdir all
mv env_specific all/
Next, you can create a symbolic link to the cross-environmental variable file:
```
```
cd all/
ln -s ../../../000_cross_env_vars .
```
When you have completed the above steps for each of your environments, your directory structure will look something like this: 
                  # :point_down:
```
.
├── ansible.cfg
├── environments/
│   │
│   ├── 000_cross_env_vars
│   │
│   ├── dev/
│   │   ├── group_vars/
│   │   │   ├── all/
│       │   │   ├── 000_cross_env_vars -> ../../../000_cross_env_vars
│   │   │   │   └── env_specific
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts
│   │
│   ├── prod/
│   │   ├── group_vars/
│   │   │   ├── all/
│   │   │   │   ├── 000_cross_env_vars -> ../../../000_cross_env_vars
│   │   │   │   └── env_specific
│   │   │   ├── db
│   │   │   └── web
│   │   └── hosts
│   │
│   └── stage/
│       ├── group_vars/
│       │   ├── all/
│       │   │   ├── 000_cross_env_vars -> ../../../000_cross_env_vars
│       │   │   └── env_specific
│       │   ├── db
│       │   └── web
│       └── hosts
│
├── playbook.yml
│
└── . . .
```

The variables set within 000_cross_env_vars file will be available to each of the environments with a low priority.

#Setting a Default Environment Inventory
***
It is possible to set a default inventory file in the ansible.cfg file. This is a good idea for a few reasons.

First, it allows you to leave off explicit inventory flags to ansible and ansible-playbook. So instead of typing:
```
ansible -i environments/dev -m ping
You can access the default inventory by typing:
```
```
ansible -m ping
```
Secondly, setting a default inventory helps prevent unwanted changes from accidentally affecting staging or production environments. By defaulting to your development environment, the least important infrastructure is affected by changes. Promoting changes to new environments then is an explicit action that requires the -i flag.


To set a default inventory, open your ansible.cfg file. This may be in your project’s root directory or at /etc/ansible/ansible.cfg depending on your configuration.

Note: The example below demonstrates editing an ansible.cfg file in a project directory. If you are using the /etc/ansibile/ansible.cfg file for your changes, modify the editing path below. When using /etc/ansible/ansible.cfg, if your inventories are maintained outside of the /etc/ansible directory, be sure to use an absolute path instead of a relative path when setting the inventory value.
```
nano ansible.cfg
```
As mentioned above, it is recommended to set your development environment as the default inventory. Notice how we can select the entire environment directory instead of the hosts file it contains:
```
[defaults]
inventory = ./environments/dev
```
You should now be able to use your default inventory without the -i option. The non-default inventories will still require the use of -i, which helps protect them from accidental changes...  :+2:
:thumbsup::
