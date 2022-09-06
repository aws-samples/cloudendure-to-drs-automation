## CloudEndure to DRS Automation

It is now possible to upgrade replicating servers from CloudEndure Disaster Recovery(CEDR) to AWS Elastic Disaster Recovery(DRS). This is accomplished via the [CEDR Server Upgrade Tool](https://docs.cloudendure.com/#Configuring_and_Running_Disaster_Recovery/Upgrade_CEDR_to_DRS/Upgrade_CEDR_to_DRS.htm#Upgrading_from_CEDR_to_AWS%C2%A0DRS%3FTocPath%3DNavigation%7CConfiguring%2520and%2520Running%2520Disaster%2520Recovery%7CUpgrading%2520from%2520CEDR%2520to%2520AWS%25C2%25A0DRS%7C_____0). The process is composed of three steps:

- Running an assessment
  - The assessment validates that replicating servers in CEDR can successfully be upgraded to DRS.

- Start Upgrade
  - This step is ran on each replicating node. It imports a launchable CEDR snapshot into DRS to allow a drill to be performed before completeting the upgrade.
  
- Finalize Upgrade
  - Finaliziung the upgrade completes the process, replication will start happening in DRS and the CEDR agent will be removed.
  
This repository contains SSM command documents that perform each step of the upgrade process. This allows unattended upgrades on any replicating server.

## Prerequisites

- All prerequesites from the [CEDR Server Upgrade Tool Documentation](https://docs.cloudendure.com/#Configuring_and_Running_Disaster_Recovery/Upgrade_CEDR_to_DRS/Upgrade_CEDR_to_DRS.htm#Upgrading_from_CEDR_to_AWS%C2%A0DRS%3FTocPath%3DNavigation%7CConfiguring%2520and%2520Running%2520Disaster%2520Recovery%7CUpgrading%2520from%2520CEDR%2520to%2520AWS%25C2%25A0DRS%7C_____0) must be met.

- All replicating instances must have the [SSM (AWS Systems Manager)](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) agent installed and running. The AWS Systems Manager Agent (SSM Agent) is Amazon software that runs on Amazon Elastic Compute Cloud (Amazon EC2) instances, edge devices, and on-premises servers and virtual machines (VMs). SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

