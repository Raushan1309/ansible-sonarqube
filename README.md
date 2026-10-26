# SonarQube Ansible (active instance only)

Ubuntu 22.04, EC2 tags `env=sonarqube` and `role=sonarqube-active`.

## Bastion setup

Install `ansible`, `boto3`, `botocore`, `amazon.aws`, `community.docker`, and `ansible.posix` on the Ansible control host. Configure an EC2 instance IAM role with read-only EC2 Describe permissions. Ensure verified SSH host keys and authorized SSH access to the active private EC2.

## Run

```sh
ansible-inventory -i aws_ec2.yml --graph
ansible -i aws_ec2.yml sonarqube_active -m ping
ansible-playbook -i aws_ec2.yml site.yml --ask-vault-pass -e @secrets.yml
```

Create `secrets.yml` locally with `sonar_db_password: "a-long-random-password"`, then encrypt it using `ansible-vault encrypt secrets.yml`. Do not commit it. Use a Jenkins-managed secret in automation.

## Notes

The Compose images are mutable tags; pin tested image digests for reproducible deployments. SonarQube may need additional kernel and container limits based on version and instance size. Verify the Docker Compose v2 package availability on the selected Ubuntu AMI. Run `docker compose version` before deployment. The database is local to the active EC2 and does not provide automatic failover. Restrict ALB ingress and configure HTTPS before production use.
