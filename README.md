# AWS_EC2_stop
## Youtube Channel: https://www.youtube.com/c/TamilTutEra
### Video Link: https://youtu.be/7_OXSGB3ipo
## Tamiltutera Ansible in Tamil - How to stop the AWS EC2 Instance using AWS CLI in Ansible Playbook 
hosts
```
localhost
```

ansible.cfg
```
[defaults]
inventory = hosts
```

ansible-playbook main.yml -e "instance_id=<aws_instance_id> region=us-east-1"

```
---
- hosts: localhost
  gather_facts: false
  connection: local
  tasks:
    - name: check current state of the EC2 Instance
      command: aws ec2 describe-instances --instance-ids {{ instance_id }} --region {{ region }} --output text --query 'Reservations[*].Instances[*].State.Name'
      register: instance_state
      delegate_to: localhost
    - debug:
        msg: "{{ instance_state }}"
    - fail:
        msg: "Instance is already stopped"
      when: instance_state.stdout == "stopped"
      delegate_to: localhost
    
    # AWS Instances stopping
    - name: Stopping the AWS EC2 Instance
      command: aws ec2 stop-instances --instance-ids {{ instance_id }} --region {{region}}
      when: instance_state.stdout != "stopped"
      register: stop_instance_state
      delegate_to: localhost
    - name: wait to stopping the instance
      wait_for:
        timeout: 60
    - name: checking the status of the instances
      debug:
        msg: "Instance stopped successfully"
      when: stop_instance_state.changed == true
```
