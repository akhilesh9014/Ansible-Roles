#**How to Manage Multistage Environments with Ansible**
*****

##**Ansible Recommended Strategy: Using Groups and Multiple Inventories**
So far, we’ve looked at some strategies for managing multistage environments and discussed reasons why they may not be a complete solution. However, the Ansible project does offer some suggestions on how best to abstract your infrastructure across environments.

The recommended approach is to work with multistage environments by completely separating each operating environment. Instead of maintaining all of your hosts within a single inventory file, an inventory is maintained for each of your individual environments. Separate group_vars directories are also maintained.

The basic directory structure will look something like this:

