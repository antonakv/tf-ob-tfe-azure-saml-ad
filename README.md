# tf-ob-tfe-azure-saml-ad
Install TFE in Azure with SAML Azure Active Directory

This manual is dedicated to Install Terraform Enterprise with SSO authentication SAML Azure Active Directory

## Requirements

- Hashicorp terraform recent version installed
[Terraform installation manual](https://learn.hashicorp.com/tutorials/terraform/install-cli)

- git installed
[Git installation manual](https://git-scm.com/download/mac)

- Amazon AWS account credentials saved in .aws/credentials file
[Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

- Configured CloudFlare DNS zone for domain `my-domain-here.com`
[Cloudflare DNS zone setup](https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/)

- SSL certificate and SSL key files for the corresponding domain name
[Certbot manual](https://certbot.eff.org/instructions)

- Created Amazon EC2 key pair for Linux instance
[Creating a public hosted zone](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)

- Azure portal access
[Get started Azure Portal](https://azure.microsoft.com/en-us/get-started/azure-portal/)

## Preparation 

- Clone git repository `antonakv/tf-aws-activeactive-agents`

```bash
git clone https://github.com/antonakv/tf-aws-activeactive-agents.git
```

```bash
Cloning into 'tf-aws-activeactive-agents'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 12 (delta 1), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (12/12), done.
Resolving deltas: 100% (1/1), done.
```

- Change folder to tf-aws-activeactive-agents

```bash
cd tf-aws-activeactive-agents
```

- Create file terraform.tfvars with following contents

```
region                  = "eu-central-1"
tfe_license_path        = "upload/license.rli"
cidr_vpc                = "10.5.0.0/16"
cidr_subnet_private_1   = "10.5.1.0/24"
cidr_subnet_private_2   = "10.5.2.0/24"
cidr_subnet_public_1    = "10.5.3.0/24"
cidr_subnet_public_2    = "10.5.4.0/24"
instance_type_jump      = "t3.medium"
instance_type_redis     = "cache.t3.medium"
key_name                = "aakulov"
jump_ami                = "ami-09c30cef71e11c0b6-your-ami"
aws_ami                 = "ami-09c30cef71e11c0b6-your-ami"
agent_ami               = "ami-0fb41c1d2901bb55c-your-ami"
db_instance_type        = "db.t3.medium"
instance_type           = "t3.2xlarge"
instance_type_agent     = "t3.medium"
release_sequence        = 654
tfe_hostname            = "tfeaa.domain.cc"
tfe_hostname_jump       = "tfeaajump.domain.cc"
postgres_db_name        = "mydbtfe"
postgres_engine_version = "14.4"
postgres_username       = "postgres"
ssl_cert_path           = "/letsencrypt-ssl-cert/config/live/domain.cc/cert.pem"
ssl_key_path            = "/letsencrypt-ssl-cert/config/live/domain.cc/privkey.pem"
ssl_chain_path          = "/letsencrypt-ssl-cert/config/live/domain.cc/chain.pem"
ssl_fullchain_cert_path = "/letsencrypt-ssl-cert/config/live/domain.cc/fullchain.pem"
domain_name             = "domain.cc"
cloudflare_zone_id      = "xxxxxxxxxxxxxxxx"
cloudflare_api_token    = "xxxxxxxxxxxxxxxx"
asg_min_nodes           = 2
asg_max_nodes           = 2
asg_desired_nodes       = 2
lb_ssl_policy           = "ELBSecurityPolicy-2016-08"
agent_token             = "Veiiaf1VYL6Jdg.atlasv1.qAQPJjC9sSkZkdmbmE99SyzoiyzJabC7nY7gWoHNzmnIva0U4jyY7GIz2w8kgBPjxCs"
asg_min_agents          = 0
asg_max_agents          = 0
asg_desired_agents      = 0
```

## Run terraform code

- In the same folder you were before, run 

```bash
terraform init
```

Sample result

```
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 3.52"...
- Finding hashicorp/tls versions matching "~> 3.1.0"...
- Finding hashicorp/template versions matching "~> 2.2.0"...
- Installing hashicorp/aws v3.66.0...
- Installed hashicorp/aws v3.66.0 (signed by HashiCorp)
- Installing hashicorp/tls v3.1.0...
- Installed hashicorp/tls v3.1.0 (signed by HashiCorp)
- Installing hashicorp/template v2.2.0...
- Installed hashicorp/template v2.2.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

```

- Run the `terraform apply`

Expected result:

```
% terraform apply --auto-approve
data.local_sensitive_file.sslkey: Reading...
data.local_sensitive_file.sslcert: Reading...
data.local_sensitive_file.sslchain: Reading...
data.local_sensitive_file.sslcert: Read complete after 0s [id=03a1061535e45b575f310a070f77ab6ba7c314f0]
data.local_sensitive_file.sslkey: Read complete after 0s [id=c55e3e91058bd74118c719cdb13ae552d5b3347c]
data.local_sensitive_file.sslchain: Read complete after 0s [id=35bea03aecd55ca4d525c6b0a45908a19c6986f9]
data.aws_iam_policy_document.instance_role: Reading...
data.aws_iam_policy_document.tfe_asg_discovery: Reading...
data.aws_iam_policy_document.instance_role: Read complete after 0s [id=1903849331]
data.aws_iam_policy_document.tfe_asg_discovery: Read complete after 0s [id=139118870]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

[ Part of the output was removed ]

Plan: 66 to add, 0 to change, 0 to destroy.

[ Part of the output was removed ]

Apply complete! Resources: 66 added, 0 changed, 0 destroyed.

Outputs:

aws_active_agents_ips = ""
aws_jump_hostname = "oxsqtfeaajump.akulov.cc"
aws_jump_public_ip = "3.72.235.5"
aws_lb_active_target_group_ips = ""
ssh_key_name = "aakulov"
url = "https://oxsqtfeaa.akulov.cc/admin/account/new?token=xxxxxxxxxxxxxxxxx"

```

- Login to the Terraform Enterprise instance using output URL and create admin user

- Login as admin user to the TFE instance

![Login as admin](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img1.png)

- Open SAML settings page

![SAML](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img2.png)

- Login to Azure portal and click to the Azure Active Directory

![Azure Active Directory](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img3.png)

- Click to the Enterprise Applications

![Enterprise applications](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img4.png)

- In the Enterprise Applications click New application

![New Application](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img5.png)

- Click Create your own application

![Create your own application](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img6.png)

- Fill the app name and click Create

![Create](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img7.png)

- On the app page click Single sign-on

![Single sign-on](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img8.png)

- In the Select a single sign-on method section click SAML

![SAML](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img9.png)

- In the Basic SAML configuration section click Edit

![Basic SAML Configuration](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img10.png)

- Copy from the TFE Admin - SAML setting page following urls and save in the text document: 

Identifier (Entity ID): https://<TFE HOSTNAME>/users/saml/metadata (listed as "Metadata (audience) URL" in TFE's SAML settings).

Reply URL (Assertion Consumer Service URL): https://<TFE HOSTNAME>/users/saml/auth (listed as "ACS consumer (recipient) URL" in TFE's SAML settings).

Sign on URL: https://<TFE HOSTNAME>/

![SAML Configuration](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img11.png)

- In the Attributes & Claims section click Edit

![Edit attributes and claims](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img12.png)

- Click on the Unique User Identifier (Name ID) claim value

![Click claim](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img13.png)

- Select user.mail in the Source attribute field and click Save

![Select user.mail](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img14.png)

- On the same Attributes & Claims page click Add new claim

![Click add new claim](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img15.png)

- Add claim called MemberOf with Source attribute: user.assignedroles and click Save

![Add claim](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img16.png)

- Go back and in the Single sign-on section SAML Certificates download Certificate (Base64)

![Download](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img17.png)

- Under the 4 Set up app header copy Login URL and Logout URL and save them in the text document

![Copy URLs](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img18.png)

- Open the TFE url https://<TFE_HOSTNAME>/app/admin/saml

![SAML config](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img19.png)

- On the SAML TFE page Click checkbox Enable SAML single sign-on 

![Enable SAML single sign-on](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img20.png)

- In the `Identity Provider Settings` section of the TFE Fill Single Sign-On URL with previously copied `Login URL`. Fill Single Log-out URL with  previously copied `Logout URL`. Copy contents of previously downloaded Certificate (Base64) file and paste to the `IDP Certificate` field.

![Copy paste](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img21.png)

- Scroll to the end of the page and click `Save SAML settings`

![Save SAML settings](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img22.png)

- Create team in the Terraform Enterprise organisation settings

![Create team](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img23.png)

- Open App registrations on the Azure portal home page

![App registrations](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img24.png)

- Search your application name

![Search your app](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img25.png)

- Click on the app name

![Click on the app](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img26.png)

- In the app registration page click Manifest

![Click manifest](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img27.png)

- Generate unique GUID with some UUID generator

```
% uuid
43c1d5e4-3a53-11ed-928c-a75cc6df3e43
```

- Add appRoles data block with generated uuid and previously created TFE group name to the manifest and click Save

```
		{
			"allowedMemberTypes": [
				"User"
			],
			"description": "ptfegroup",
			"displayName": "ptfegroup",
			"id": "43c1d5e4-3a53-11ed-928c-a75cc6df3e43",
			"isEnabled": true,
			"lang": null,
			"origin": "Application",
			"value": "ptfegroup"
		},
```

![Click save](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img28.png)

- Go to the Azure Active directory Enterprise applications and search for your application

![Search for app](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img29.png)

- Click on the application name and then on the Users and groups

![Click on the app](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img30.png)

- Click Add user/group

![Click add user/group](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img31.png)

- Click to `Users and groups: None Selected `, search for previously created Active directory account email

![Click select](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img32.png)

- Click to `Select a role: None Selected` and click on previously created Active directory group

![Click select and select group](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img33.png)

- Click `Assign`

![Assign](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img34.png)

- Login to the TFE with SAML

![Login with SAML](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img35.png)

- Enter email of the Azure AD user created on previous steps and click Next

![Enter email](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img36.png)

- Enter password of the Azure AD user created on previous steps and click Next

![Enter password](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img37.png)

- Click Yes on the Stay signed in dialog

![Stay signed in](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img38.png)

- Expected result

![Logged in to the TFE](https://github.com/antonakv/tf-ob-tfe-azure-saml-ad/raw/main/images/img39.png)
