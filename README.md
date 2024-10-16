# Ansible Azure Inventory

## Install Prerequisites
```
ansible-galaxy collection install -r collections/requirements.yml
ansible-galaxy role install -r roles/requirements.yml
pip3 install botocore boto3
```

## Define environment variables
```
export AWS_ACCESS_KEY_ID=accesskeyid
export AWS_SECRET_ACCESS_KEY=secretaccesskey
```

## View Inventory
```
ansible-inventory -i inventory.aws_ec2.yml --list --yaml
```

## Provision Ansible host
```
ansible-playbook -i ansible_vm.yml --limit=ansible provision/ansible_vm.yml --become
```

## Get Instance Info
```
ansible-playbook -i inventory.aws_ec2.yml --limit _running ec2/ec2_info.yml
```

## Start All Instances
```
ansible-playbook -i inventory.aws_ec2.yml --limit _running -e state=running ec2/ec2_state.yml
```

## Get Route Info
```
ansible-playbook -e region=us-east-2 route/route_info.yml
```

## Get Transit Gateway Info
```
ansible-playbook -e region=us-east-2 transit_gateway/transit_gateway_info.yml
```

## Provision ODB Storage
```
ansible-playbook -i ansible_vm.yml --limit=tstodb ec2/storage.yml
```

## Activate/Deactivate CRO
```
ansible-playbook -i inventory.aws_ec2.yml --limit '_stopped:&EPIC-AWS-HSW*' -e @vars.yml cro/state.yml
```

ebsnvme-id -v /dev/nvme0n1

### References
https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html