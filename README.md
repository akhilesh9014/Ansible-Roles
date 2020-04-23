#**How to Manage Multistage Environments with Ansible**
*****

##**Ansible Recommended Strategy: Using Groups and Multiple Inventories**
So far, we’ve looked at some strategies for managing multistage environments and discussed reasons why they may not be a complete solution. However, the Ansible project does offer some suggestions on how best to abstract your infrastructure across environments.

The recommended approach is to work with multistage environments by completely separating each operating environment. Instead of maintaining all of your hosts within a single inventory file, an inventory is maintained for each of your individual environments. Separate group_vars directories are also maintained.

The basic directory structure will look something like this:

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

