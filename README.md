# Ansible influx grafana integration

Create `secrets.enc` and add:
```
influx_pass : yourpass
grafana_pass : yet another password
```

Afterwards, encrypt it via:
```sh
ansible-vault encrypt secrets.enc
```

Finally, run ansible whenever you need new infrastructure via:
```sh
ansible-playbook -i inventory.yaml --ask-become-pass -e @secrets.enc --ask-vault-pass install.yaml
```