# Building Execution Environments for Home Lab

This README provides a quick guide on how to build and manage execution environments for your home lab using Podman.

## Prerequisites

- Podman installed on your system
- Access to a local registry (in this example, running on localhost:8444)

## Steps

1. **Login to your local registry:**
   ```
   podman login localhost:8444 -u admin -p password --tls-verify=false
   ```
   Note: Using `--tls-verify=false` is not recommended for production environments.

2. **Build your custom execution environment:**
   ```
   ansible-builder build -t my-custom-ee:v1 -f execution-environment.yml
   ```
   This command builds your custom execution environment based on the `execution-environment.yml` file.

3. **Examine the contents of your execution-environment.yml:**
   ```yaml
   ---
   version: 3

   options:
     package_manager_path: /usr/bin/microdnf

   images:
     base_image:
       name: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest

   build_arg_defaults:
    ANSIBLE_GALAXY_CLI_COLLECTION_OPTS: '--ignore-certs'

   additional_build_files:
     - src: files
       dest: configs

   additional_build_steps:
     prepend_galaxy:
       - COPY _build/configs/ansible.cfg /etc/ansible/ansible.cfg

   dependencies:
     system:
       - curl
       - git
     python:
       - requests
       - jmespath
     galaxy:
       collections:
         - name: ansible.windows
         - name: ansible.utils
         - name: community.general
         - name: containers.podman
         - name: infra.ee_utilities
         - name: servicenow.itsm
   ```
   This file defines the structure and dependencies of your execution environment.

4. **Tag your local image:**
   ```
   podman tag localhost/my-custom-ee:v1 localhost:8444/my-custom-ee:v1
   ```
   This step prepares your local image for pushing to the registry.

5. **Push the image to your local registry:**
   ```
   podman push --tls-verify=false localhost:8444/my-custom-ee:v1
   ```
   Again, `--tls-verify=false` should be used cautiously.

## Best Practices

- Always use strong passwords for your registry.
- In a production environment, ensure proper TLS verification.
- Regularly update your execution environments to include the latest security patches.

Remember to adjust the commands according to your specific setup, especially the registry URL and image names.
