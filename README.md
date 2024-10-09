# Ansible Azure Inventory

## Install Prerequisites
```
ansible-galaxy collection install -r collections/requirements.yml
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

## Get Instance Info
```
ansible-playbook -i inventory.aws_ec2.yml --limit _running ec2/ec2_info.yml
```

## Start All Instances
```
ansible-playbook -i inventory.aws_ec2.yml --limit _running -e state=running ec2/ec2_state.yml
```

### References
https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html