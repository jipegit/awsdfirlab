# awsdfirlab
DFIR Lab in AWS

The stack is 100% free-tier eligible.

# AWS account Setup
 * Create an [AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)
 * Set-up [MFA authentication](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html)
 * Create [a keypair](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-keypairs.html) (eg. `lab-01`)
 * Set-up [AWS Systems Manager Quick Setup](AWS https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-quick-setup.html) (optional if you don't want to use RDP)

# AWS stack setup

Use the `aws cloudformation` CLI command to create the stack.

In this example the STS token is store in the `mfa` profile of our ~/.aws/credential file. The KeyPair name created above is `lab-01`.

`aws sts get-session-token --serial-number arn:aws:iam::XXXXXXXXXXXX:mfa/root-account-mfa-device --token-code XXXXXX`
Edit your `~/.aws/credential`
`aws --profile mfa cloudformation create-stack --stack-name dfir-lab-01 --parameters ParameterKey=RDPLocation,ParameterValue=YOUR_IP_ADDRESS/32 ParameterKey=KeyPair,ParameterValue=lab-01 --template-body file://dfirlab.yml`

# Windows environement Setup
## Windows 2019 Server Instance

The DC can be created on the Windows Server 2019 instance which has a static IP address.
 * RDP (or connect using SSM agent) to the Windows Server 2019 instance 
 * Change its name to *DC-01* in an **elevated** PowerShell `PS C:\> Rename-Computer -NewName "DC-01" -Restart`
 * [Add the AD DS (and DNS) role and promote it to DC](https://www.windowscrush.com/promote-windows-server-2019-to-domain-controller.html) (eg. use **dfirlab.local** as domain).
 * Add a domain administrator user (*adm-one*) using *dsa.msc*
 * Add a standard user (*user-one*) and add it to the *Remote Desktop Users*
 
 ### RDS Access
 * Allow users to connect using RDS [via GPO](https://softwarekeep.com/help-center/how-to-enable-remote-desktop-on-windows)
 * Add "Domain users" to the "Remote Desktop Users" local group [via GPO](https://www.urtech.ca/2013/03/solved-how-to-add-users-to-remote-desktop-using-group-policy/)

 ## Windows 2016 Server Instance
 * RDP (or connect using SSM agent) to the Windows Server 2019 instance 
 * Change its name to *SERVER-01* in an **elevated PowerShell** `PS C:\> Rename-Computer -NewName "SERVER-01" -Restart`
 * Change its DNS server to *DC-01* (ie. 10.42.0.42) in the Network Adapter / IPv4 settings
 * (Join the *dfirlab.local* domain)[https://www.dtonias.com/windows-server-2016-join-domain/] using the domain admin account (*adm-one*) so that it creates the computer account in the AD and restart

 Your lab environement is ready 🥳