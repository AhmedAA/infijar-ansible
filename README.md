# Inifjar offsec collection

This collection is intended to be run inside a container or virtual machine.

## Example playbook

Below is an example of a playbook that can be defined to deploy this collection:

```yaml
---
- name: Build Offensive Security Container Image
  hosts: localhost
  connection: local

  vars:
    container: false

  tasks:
    - name: Install common offsec
      ansible.builtin.include_role:
        name: infijar.offsec.common

    - name: Install wordlists
      ansible.builtin.include_role:
        name: infijar.offsec.wordlists

    - name: Install cracking offsec
      ansible.builtin.include_role:
        name: infijar.offsec.cracking

    - name: Install Network Security offsec
      ansible.builtin.include_role:
        name: infijar.offsec.netsec
      when: build_netsec is defined and build_netsec | bool

    - name: Install Application Security offsec
      ansible.builtin.include_role:
        name: infijar.offsec.appsec
      when: build_appsec is defined and build_appsec | bool

  post_tasks:
      - name: Compile shell history from snippets
        ansible.builtin.assemble:
          src: "{{ ansible_env.HOME }}/.zsh_history.d"
          dest: "{{ ansible_env.HOME }}/.zsh_history"
          mode: '0600'

      - name: Remove build dependencies to save space
        ansible.builtin.include_role:
          name: infijar.offsec.common
          tasks_from: cleanup
        when: container | bool

      - name: Run Post-Install Cleanup
        ansible.builtin.include_role:
          name: infijar.offsec.common
          tasks_from: post_install
        when: container | bool
```

And a `requirements.yml` file to pull the collection:

```yaml
---
collections:
  - name: https://github.com/AhmedAA/infijar-ansible.git
    type: git
```

For an example project: <https://github.com/AhmedAA/infijar>

## Acknowledgments

This project would not exist if Exegol didn't
<https://github.com/ThePorgs/Exegol>.

What I have built here is way smaller scale, but sufficient for me, and using
Ansible, because I believe it is better suited for this purpose.

If you think this is interesting, then I recommend you take a look at Exegol for
something more professional.