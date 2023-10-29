---
comments: true
---

# ansible:handyTricks

safely limit ansible playbook to one host

``` ansible
ansible-playbook -i inventory --limit=hostname playbook.yaml
```
