Date: 06/05/2026

Task:9

Terraform State Migration to Scale Instance Using count Without Destroying Existing Instance

🧩 Problem Statement
An infrastructure instance is currently provisioned using Terraform as a single resource (without count).
Now we need to scale it to 5 instances using the count meta-argument, without destroying or recreating the existing instance.
Terraform must be told that the existing instance should become:
•	count.index = 0

•	Address change: aws_instance.web_server ➜ aws_instance.web_server[0]
________________________________________
🎯 Objectives
•	Modify Terraform configuration to use count = 5

•	Preserve the existing instance as the first instance ([0])

•	Prevent Terraform from destroying and recreating the existing resource

•	Perform state migration using Terraform state commands (or moved block)
________________________________________
✅ Why State Migration is Required
If you simply add count = 5 and run terraform apply, Terraform will interpret:
•	Old resource: aws_instance.web_server as missing ➜ destroy

•	New resources: aws_instance.web_server[0..4] as new ➜ create

That would cause downtime and replacement of the existing server.
So, we must migrate state so Terraform understands:
The existing instance is now aws_instance.web_server[0].
________________________________________
🛠️ Phase 1: Code Refactoring

Before (Current State)

resource "aws_instance" "web_server" {

  ami           = "ami-07ff62358b87c7116"

  instance_type = "t3.small"

  tags = {

    Name = "web-server"

  }
}

After (New State: Add count = 5)
resource "aws_instance" "web_server" {

  count         = 5

  ami           = "ami-07ff62358b87c7116"
  instance_type = "t3.small"

  tags = {

    Name = "web-server-${count.index}"
  }
}
✅ Best practice: Use count.index in the Name tag so each instance has a unique name.
________________________________________
🔁 Phase 2: State Migration (CLI Method)

⚠️ Do NOT run terraform apply yet.

Step 1: Check what Terraform currently tracks
terraform state list
Expected:
aws_instance.web_server

Step 2: Move the state to index [0]

terraform state mv aws_instance.web_server aws_instance.web_server[0]
What this does

•	Finds the existing state entry: aws_instance.web_server

•	Renames it inside the state file to: aws_instance.web_server[0]

•	Keeps the existing EC2 instance intact
________________________________________

🔍 Phase 3: Verification
Run:

terraform plan

Expected plan (typical):

Plan: 4 to add, 0 to change, 0 to destroy.

You may see 1 to change if tags were updated for the existing instance — this is safe.

✅ Most important: No resources should be destroyed.
________________________________________
🚀 Phase 4: Apply Changes

terraform apply

Terraform will:
•	Keep the existing instance as [0]

•	Create 4 additional instances: [1], [2], [3], [4]
________________________________________
⭐ Alternative (Recommended): Using moved Block (Terraform v1.1+)

Instead of running the CLI command manually, you can document the migration in code.
Add this to your Terraform configuration:
moved {

  from = aws_instance.web_server
  
  to   = aws_instance.web_server[0]
}
Then run:

terraform plan

terraform apply

Terraform will automatically understand the resource address change and preserve the existing instance.
________________________________________
🧯 Safety Best Practices (Highly Recommended)

Backup local state (if using local backend)
cp terraform.tfstate terraform.tfstate.backup
Backup state even for remote backends (S3, etc.)
terraform state pull > backup.tfstate
________________________________________
✅ Expected Final State

Terraform state should contain:

•	aws_instance.web_server[0] (existing instance)

•	aws_instance.web_server[1]

•	aws_instance.web_server[2]

•	aws_instance.web_server[3]

•	aws_instance.web_server[4]
