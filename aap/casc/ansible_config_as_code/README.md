Ensure to run the following commands prior to running the playbooks

1. sudo pip3 install --upgrade pip
sudo pip3.9 install pre-commit

2. pre-commit install

3. sudo dnf install ansible-core ansible-lint ansible-builder podman

4. ansible-galaxy collection install infra.ee_utilities:2.0.8 containers.podman community.general


Run the EE playbook with 

- ansible-playbook -i inventory.yml -l builder playbooks/build_ee.yml

Pull the newly created EE with

- podman login --tls-verify=false hub-student#.rh####.example.opentlc.com

- podman pull --tls-verify=false hub-student#.rh####.example.opentlc.com/config_as_code:latest

Run the Automation Hub Config playbook with

- ansible-navigator run playbooks/hub_config.yml --eei hub-student#.rh####.example.opentlc.com/config_as_code -i inventory.yml -l automationhub --pa='--tls-verify=false' -m stdout --playbook-artifact-enable False


Run the Controller Config playbook with

- ansible-navigator run playbooks/controller_config.yml --eei hub-student#.rh####.example.opentlc.com/config_as_code -i inventory.yml -l automationcontroller --pa='--tls-verify=false' -m stdout --playbook-artifact-enable False


