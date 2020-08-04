# How to connect to an EC2 instance in the AWS engineering sandbox

We deploy EC2 instances (like the ones required to run Stream Enrich) in a private subnet, which means they don't have a public IP that can be used to connect to the box. Instead, we need to go through a Bastion instance.

First, you'll need an SSH key pair for authentication. You can generate one like so:

```bash
ssh-keygen -t rsa -f my_key
```

This will give you two files: `my_key` and `my_key.pub`. The latter is your public key, which you must add to the Bastion instance. The former is your secret key, which you must keep so.

All pipelines in the AWS sandbox are deployed off the same `aws_setup` stack. At the time of writing, the stack we use is called `aws_setup_josh`. You need to replace the value found in Consul under `.../kv/customer/aws_sandbox/aws_setup_josh/input/ssh_public_key` with the contents of your `my_key.pub` file. (You will need to be on the VPN and have access to Consul.) Once the value is saved and the stack applied with Terraform, your public key will be on the Bastion box.

When the Bastion box is launched, its auto scaling group is started with 0 capacity. You will need to change that. From the AWS console, go to "EC2 >> Auto Scaling >> Auto Scaling Groups" and find the ASG for Bastion (in this case it should be called `sp-aws-sandbox-josh-bastion`). Click on it and chose to "EDIT" the "Group Details". Set desired capacity to 1, minimum capacity to 0 or 1, and maximum capacity to 1.

Now, you need to reboot the enrichment ASG, so the EC2 instances can pick up the key from Bastion. In the AWS EC2 console, you can just terminate all running Stream Enrich instances. The enrichment ASG will ensure that new ones are launched to replace them. Alternatively, you can do the reboot via [a @Snowdroid deployment](https://snplow.atlassian.net/wiki/spaces/TEC/pages/778862615/Deploy+infrastructure).

You should now be ready to connect to the Bastion instance, using its public IP and your private key (the `my_key` file).

Before you do that, it's worth checking what your `~/.ssh/config` file looks like and whether you have one in the first place. If you don't, create one and ensure its contents look like this:

```
Host *
 AddKeysToAgent yes
 ForwardAgent yes
 IdentitiesOnly yes
```

Now you can log into the Bastion box:

```bash
ssh -A -i ~/path/to/my_key ec2-user@{BASTION_PUBLIC_IP}
```

Once you're in the box, you can connect to any running EC2 instance using its private IP address:

```bash
ssh ec2-user@{EC2_INSTANCE_IP}
```

## Setting up port forwarding between and EC2 instance and localhost via Bastion

This is useful if you want to run some profiling app on the EC2 machine.

First, activate port forwarding between your local machine and Bastion:

```bash
ssh -A -X -i ~/path/to/my_key ec2-user@{BASTION_PUBLIC_IP} -4NL 5555:localhost:5555
```

This will open a connection that will stay open, so you'll need a new shell for the next command.

Next up, set up port forwarding between the Bastion host and the EC2 box:

```bash
ssh ubuntu@{EC2_INSTANCE_IP} -4NL 5555:localhost:5555
```

You can now connect to `localhost:5555` from a profiling tool, such as visualvm or jconsole.
