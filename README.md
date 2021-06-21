
# Ansible Role Clickhouse

ClickHouse is a fast open-source column-oriented database management system that allows generating analytical data reports in real-time using SQL queries.

## Role Variables
### Normal variable

| Variable | Default | Description |
|------|------|------|
| `datadir` | /var/lib/mysql | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `user` | mysql | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `wsrep_provider` | /usr/lib/libgalera_smm.so | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `binlog_format` | ROW | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `default_storage_engine` | InnoDB | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `innodb_autoinc_lock_mode` | 2 | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `wsrep_sst_auth_user` | NA | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `wsrep_sst_auth_pwd` | NA | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `wsrep_sst_priv` | "*.*:ALL,GRANT" | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |
| `root_pwd` | NA | [Reference](https://gist.github.com/Lathanao/35cc0384c7c8fc055814f8e28059d011) |


## Run Locall

Clone the project

```bash
  git clone https://github.com/songhanpoo/percona_xtradb_cluster.git
```

Go to the project directory

```bash
  cd percona_xtradb_cluster
```

Edit tests/inventories.yaml

```yaml
  all:
  hosts:
    node1:
      ansible_ssh_host: 172.xx.xx.xx
      ip: 172.xx.xx.xx
      ansible_ssh_user: vagrant
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
      # ansible_ssh_common_args: -o "StrictHostKeyChecking no" -o ProxyCommand="ssh -W %h:%p -q root@172.xx.xx.xx"
    node2:
      ansible_ssh_host: 172.xx.xx.xx
      ip: 172.xx.xx.xx
      ansible_ssh_user: vagrant
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
      # ansible_ssh_common_args: -o "StrictHostKeyChecking no" -o ProxyCommand="ssh -W %h:%p -q root@172.xx.xx.xx"
  children:
    master:
      hosts:
        node1:
    backup:
      hosts:
        node2:
```


Edit tests/test.yaml ( playbook )
Below default config standalone

```yaml
---
---
- hosts: all
  become: true
  become_method: sudo
  gather_facts: true
  roles:
  - {role: 'percona_xtradb_cluster',tags: 'pxc'}
  vars:
#-------PXC--------#
    pxc_datadir: /var/lib/mysql
    pxc_user: mysql
    # In order for Galera to work correctly binlog format should be ROW
    pxc_binlog_format: ROW
    # Using the MyISAM storage engine is not recommended
    pxc_default_storage_engine: InnoDB
    # This InnoDB autoincrement locking mode is a requirement for Galera
    pxc_innodb_autoinc_lock_mode: 2
    wsrep_sst_auth_user: sstuser
    wsrep_sst_auth_pwd: s3cretPass"
    wsrep_sst_priv: "*.*:ALL,GRANT"
    pxc_root_pwd: root
```

Run below command for testing connection
```bash
ansible -i tests/inventory.yml -m ping
```

Run below command for deploy
```bash
ansible-playbook -i tests/inventory.yml tests/test.yml 
```


## Support

For support, email songhanpoo@gmail.com.

## Authors

- [@songhanpoo](https://www.github.com/songhanpoo)
