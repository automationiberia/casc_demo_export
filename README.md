Demo export objects from  Ansible Controller
============================================

This repository try to explain how can use the filtree_create role of redhat-cop/controll_configuration collection to export all objects from an Ansible Controller.


Helpful Links
-------------

| Name | Link |
|------|-------------|
|redhat-cop organization in Github | https://github.com/redhat-cop|
|Collection controller_configuration repository | https://github.com/redhat-cop/controller_configuration |
|controller_configuration collection in galaxy | https://galaxy.ansible.com/redhat_cop/controller_configuration |
|Role filetree_create repository | https://github.com/redhat-cop/controller_configuration/tree/devel/roles/filetree_create |
| awx.awx collection | https://docs.ansible.com/ansible/latest/collections/awx/awx/index.html |



Step by Step procedure
----------------------

1. **Configure Dependencies**

  A collections/requirements.yml file is needed. This repo has an example.

2. **Install collections**

  ```
  ansible-galaxy collection install -r collections/requirements.yml -p collections
  ```
3. **Create playbook**

  A playbook to launch the filetree_create role is needed. This repo has an example: demo_export_objects.yml.

4. **Create variables file**

   A variables file with credential and access variables encrypted with vault is needed. To create it:

   ```
   ansible-vault create configure_connection_controller_credentials.yml

   New Vault password:
   Confirm New Vault password:

   ---
   vault_controller_username: <admin_controller_user>
   vault_controller_password: <admin_controller_password>
   vault_controller_hostname: <admin_hostname>
   vault_controller_validate_certs: <(true|false)>
   ...

   ```

5. **Run playbook**

    Playbook Extra Variables:

    | Name | Description | Valid Vaules |
    |------|-------------| ------------ |
    |input_tag| Define tags to select what type of objects it will be exported | all, credentials, credential_types, execution_environments, groups, hosts, inventory, inventory_sources, job_templates, notification_templates, organizations, projects, roles, teams, users, workflow_job_template_nodes, workflow_job_templates |
    |vault_controller_[username&#124;password&#124;hostname&#124;validate_certs] | Define access variables to Ansible Controller. This example adds these variables from the encrypted file configure_connection_controller_credentials.yml| N/A |
    |output_path | Local path where files with exported object will be saved. Default value: /tmp/filetree_output | N/A |

    ```
    ansible-playbook demo_export_objects.yml -e '{input_tag: [all]}' -e @configure_connection_controller_credentials.yml --ask-vault-password
    ```
5. **Review results**

   Every file and directory with exported data should be reviewed before use them as Configuration as Code.

   Example of file tree created:

   ```
   $ tree /tmp/filetree_output
   /tmp/filetree_output
   ├── casc-admin
   │   ├── current_credentials.yaml
   │   ├── current_groups_casc-admin Localhost.yaml
   │   ├── current_hosts_casc-admin Localhost.yaml
   │   ├── current_inventories.yaml
   │   ├── current_job_templates.yaml
   │   ├── current_organization.yaml
   │   ├── current_projects.yaml
   │   └── current_workflow_job_templates.yaml
   ├── casc-myorg
   │   ├── current_credentials.yaml
   │   ├── current_groups_casc-myorg Localhost.yaml
   │   ├── current_hosts_casc-myorg Localhost.yaml
   │   ├── current_inventories.yaml
   │   ├── current_job_templates.yaml
   │   ├── current_organization.yaml
   │   ├── current_projects.yaml
   │   ├── current_teams.yaml
   │   ├── current_users_casc-myorg-orgadmin.yaml
   │   ├── current_users_casc-myorg_user1.yaml
   │   ├── current_users_casc-myorg_user2.yaml
   │   ├── current_users_casc-myorg_user3.yaml
   │   └── current_workflow_job_templates.yaml
   ├── current_credential_types.yaml
   ├── current_roles_admin.yaml
   ├── current_roles_casc-myorg-orgadmin.yaml
   ├── current_roles_casc-myorg team1.yaml
   ├── current_roles_casc-myorg_user1.yaml
   ├── current_roles_casc-myorg_user2.yaml
   ├── current_roles_casc-myorg_user3.yaml
   ├── Default
   │   ├── current_groups_Demo Inventory.yaml
   │   ├── current_hosts_Demo Inventory.yaml
   │   ├── current_inventories.yaml
   │   ├── current_job_templates.yaml
   │   ├── current_organization.yaml
   │   └── current_projects.yaml
   └── ORGANIZATIONLESS
       └── current_credentials.yaml
   ```
