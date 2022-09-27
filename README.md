## CloudEndure to DRS Automation

It is now possible to upgrade replicating servers from CloudEndure Disaster Recovery(CEDR) to AWS Elastic Disaster Recovery(DRS). This is accomplished via the [CEDR Server Upgrade Tool](https://docs.cloudendure.com/#Configuring_and_Running_Disaster_Recovery/Upgrade_CEDR_to_DRS/Upgrade_CEDR_to_DRS.htm#Upgrading_from_CEDR_to_AWS%C2%A0DRS%3FTocPath%3DNavigation%7CConfiguring%2520and%2520Running%2520Disaster%2520Recovery%7CUpgrading%2520from%2520CEDR%2520to%2520AWS%25C2%25A0DRS%7C_____0). The process is composed of three steps:

- Running an assessment
  - The assessment validates that replicating servers in CEDR can successfully be upgraded to DRS.

- Start Upgrade
  - This step is ran on each replicating node. It imports a launchable CEDR snapshot into DRS to allow a drill to be performed before completeting the upgrade.
  
- Finalize Upgrade
  - Finaliziung the upgrade completes the process, replication will start happening in DRS and the CEDR agent will be removed.
  
This repository contains SSM command documents that perform each step of the upgrade process. This allows unattended upgrades on any replicating server.

![upgrade_from_cedr_to_drs_smaller](https://user-images.githubusercontent.com/97046295/189938050-44fe81e8-77cd-4490-90a9-69d856b7b473.png)


## Prerequisites

- All prerequesites from the [CEDR Server Upgrade Tool Documentation](https://docs.cloudendure.com/#Configuring_and_Running_Disaster_Recovery/Upgrade_CEDR_to_DRS/Upgrade_CEDR_to_DRS.htm#Upgrading_from_CEDR_to_AWS%C2%A0DRS%3FTocPath%3DNavigation%7CConfiguring%2520and%2520Running%2520Disaster%2520Recovery%7CUpgrading%2520from%2520CEDR%2520to%2520AWS%25C2%25A0DRS%7C_____0) must be met.

- All replicating instances must have the [SSM (AWS Systems Manager)](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) agent installed and running. The AWS Systems Manager Agent (SSM Agent) is Amazon software that runs on Amazon Elastic Compute Cloud (Amazon EC2) instances, edge devices, and on-premises servers and virtual machines (VMs). SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources.

## Usage

- Clone the repository.
```
git clone https://github.com/aws-samples/cloudendure-to-drs-automation.git
```
- Create the command documents.
```
cd cloudendure-to-drs-automation/cmd
```
```
aws ssm create-document \
    --content file://assessment.yaml \
    --name "cedr-to-drs-assessment" \
    --document-type "Command" \
    --document-format YAML
```
```
aws ssm create-document \
    --content file://start_upgrade.yaml \
    --name "cedr-to-drs-start-upgrade" \
    --document-type "Command" \
    --document-format YAML
```
```
aws ssm create-document \
    --content file://finalize_upgrade.yaml \
    --name "cedr-to-drs-finalize-upgrade" \
    --document-type "Command" \
    --document-format YAML
```

- Run the assessment document on any SSM managed instance, this will provide a report output that will tell you if any servers need attention before migration to DRS. Be sure to replace all $VARIABLES with the values from your environment.

```
aws ssm send-command  --targets Key="InstanceIds",Values="$INSTANCEID" --document-name "cedr-to-drs-assessment" --parameters "apiToken=$APITOKEN","projectId=$PROJECTID" --cloud-watch-output-config "CloudWatchOutputEnabled=true,CloudWatchLogGroupName=cedr-upgrade" --region $REGION
```

- Run the start-upgrade document on all SSM instances to be migrated, this will begin the migration process by importing the most recent completed snapshot for testing. Be sure to replace all $VARIABLES with the values from your environment. In this example I am targeting all instances with a tag key of "cedr-attribute" and a tag value of "migrate" . This would need to be set on all instances you would like to upgrade. You could also select instances manually or by resource group.

```
aws ssm send-command --targets Key="tag:cedr-attribute",Values="migrate" --document-name "cedr-to-drs-start-upgrade" --parameters "apiToken=$APITOKEN","projectId=$PROJECTID","awsAccessKey=$AWSACCESSKEY","awsSecretAccessKey=$AWSSECRETACCESSKEY" --cloud-watch-output-config "CloudWatchOutputEnabled=true,CloudWatchLogGroupName=cedr-upgrade" --region $REGION
```

- Run the finalize-upgrade document on all SSM instances to be migrated, this will finalize the migration process and remove the CloudEndure agent from the source server. Be sure to replace all $VARIABLES with the values from your environment. In this example I am targeting all instances with a tag key of "cedr-attribute" and a tag value of "migrate" . This would need to be set on all instances you would like to upgrade. You could also select instances manually or by resource group.

```
aws ssm send-command --targets Key="tag:cedr-attribute",Values="migrate" --document-name "cedr-to-drs-finalize-upgrade" --parameters "apiToken=$APITOKEN","projectId=$PROJECTID","awsAccessKey=$AWSACCESSKEY","awsSecretAccessKey=$AWSSECRETACCESSKEY" --cloud-watch-output-config "CloudWatchOutputEnabled=true,CloudWatchLogGroupName=cedr-upgrade" --region $REGION
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

