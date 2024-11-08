# Ansible Azure Inventory

## Install Prerequisites
```
ansible-galaxy collection install -r collections/requirements.yml
ansible-galaxy role install -r roles/requirements.yml
pip3 install botocore boto3
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
sudo dpkg -i session-manager-plugin.deb
sudo apt install awscli
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

## Ping Linux Hosts to Check Connectivity
```
#without SSM
ansible -m ping -e @vars/linux.yml -i inventory.aws_ec2.yml --limit='!_Windows' all
#with SSM
ansible -m ping -i inventory.aws_ec2.yml --limit='!_Windows' all
```

## Ping Windows Hosts to Check Connectivity
```
kinit admin@sapphire.dev
#without SSM
ansible -m win_ping -e @vars/windows.yml -i inventory.aws_ec2.yml --limit='_Windows' all
#with SSM
ansible -m win_ping -i inventory.aws_ec2.yml --limit='_Windows' all
```

## Provision Ansible host
```
ansible-playbook -i inventory.aws_ec2.yml --limit=ansible01* provision/rhel_ansible_vm.yml
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
#without SSM
ansible-playbook -i inventory.aws_ec2.yml -e @vars/linux.yml --limit=tstodb*,prdodb* ec2/linux.yml
#with SSM
ansible-playbook -i inventory.aws_ec2.yml --limit=tstodb*,prdodb* ec2/linux.yml
```

## Provision Windows Storage
```
#without SSM
ansible-playbook -i inventory.aws_ec2.yml -e @vars/windows.yml --limit='_Windows' ec2/windows.yml
#with SSM
ansible-playbook -i inventory.aws_ec2.yml --limit='_Windows' ec2/windows.yml
```

## Activate/Deactivate CRO
```
ansible-playbook -i inventory.aws_ec2.yml --limit '_stopped:&EPIC-AWS-HSW*' -e @vars.yml cro/state.yml
```

ebsnvme-id -v /dev/nvme0n1

### References
https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html

## SSH Examples
```
ssh ec2-user@54.245.75.136 -i ~/.ssh/lyas_id_rsa
ssh -o ProxyCommand="ssh -i ~/.ssh/lyas_id_rsa -W %h:%p ec2-user@52.10.124.182" ec2-user@10.197.0.11 -i ~/.ssh/lyas_id_rsa
ssh -o ProxyCommand="ssh -i ~/.ssh/lyas_id_rsa -W %h:%p ec2-user@52.10.124.182" ec2-user@10.197.0.4 -i ~/.ssh/lyas_id_rsa
ssh -o ProxyCommand="ssh -i ~/.ssh/lyas_id_rsa -W %h:%p ec2-user@52.10.124.182" ec2-user@10.197.0.36 -i ~/.ssh/lyas_id_rsa
```

## SSM Examples
```
aws ssm start-session --target i-064ba27a514ed5130 --region us-west-2

ssh -o ProxyCommand="aws ssm start-session --region us-west-2 --target $(aws ec2 describe-instances --region us-west-2 --filter "Name=tag:Name,Values=ansible01" --query "Reservations[].Instances[?State.Name == 'running'].InstanceId[]" --output text) --document-name AWS-StartSSHSession --parameters portNumber=22" lyasspiehler@ansible01 -i ~/.ssh/lyas_id_rsa
```