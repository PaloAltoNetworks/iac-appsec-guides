# PRISMA CLOUD IAC SECURITY BOOTCAMP


### Lab Introduction

Thank you for joining today’s Infrastructure as Code (IaC) camp. The lab will focus on the ways
that Prisma Cloud can help you secure IaC from the code to runtime.

Before we begin the lab let's start with a brief overview of the scenario to help frame the
context.

### Scenario

The Exampli Corp, a mock corporation, is rushing to finish a banking app for
their customer Bank of Anthos in time before the fiscal year ends.
Up against challenging deadlines, the development and infrastructure teams are working
around the clock to get their app built, tested, and released to hit their goals.

Fortunately, Exampli Corp recently integrated Prisma Cloud into their development lifecycle
adopting shift left security from code to cloud. Once in production Exampli Corps operations
and security teams continue to leverage Prisma Cloud to monitor and protect runtime
resources, reduce the attack surface, and enforce least privilege.

Will The Exampli Corp team take the time to build a secure app? Or will the stress of rushing to complete the Bank of Anthos app in time lead to mistakes?

### Resources

Vulnerable IaC :

In this lab we leverage some intentionally vulnerable IaC. To learn more about the project and
its contributors visit the link below:

- https://github.com/bridgecrewio/terragoat

Bank of Anthos Application :

In this lab there is a mock banking application that is used as the application the Exampli Corp
development team is building. To learn more about the project and its contributors visit the link
below :

- https://github.com/GoogleCloudPlatform/bank-of-anthos

### Exercise

Let's begin by exploring the power of shifting security left with Infrastructure as Code scanning. As infrastructure is being defined as code, security must be integrated with the tools developers use.

Fortunately, Prisma Cloud can be integrated into the development pipeline and tools developers use to secure IaC. Prisma Cloud IaC security is built on the open source project Checkov. Checkov is a policy-as-code tool with millions of downloads that checks for misconfigurations in IaC templates such as Terraform, CloudFormation, Kubernetes, Helm, ARM Templates and Serverless framework.

To learn more visit: https://www.paloaltonetworks.com/prisma/cloud/infrastructure-as-code-security

In this exercise, we will take a look at some IaC templates within Exampli Corp’s repository.

#### Find and Fix Insecure Infrastructure Code

1. Login to [Prisma Cloud](https://app3.prismacloud.io/login).

2. Use the credentials provided by your Instructor to authenticate.

3. Use the dropdown window in the upper left corner to select the three different security focuses within Prisma Cloud. Select **Application Security**.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-3.png "Optional title")

4. Next, use the navigation pane on the left to select **Projects** under the **Code** section.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-4.png "Optional title")

5. Click on the **IaC Misconfiguration** tab and in the **Repositories** drop down, select the **c-haisten/Exampli** repository

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-5.png "Optional title")

6. Once you have selected the correct repository, filter by "High" severity and let's take a look at some troubling misconfigurations found in the GCP Terraform templates. Click on the search icon ![Alt text for image](/iac_screenshots/search-icon.png "Optional title") and search for **SQL**

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-6a.png "Optional title")

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-6b.png "Optional title")

7. Now that we have filtered by severity and search term, explore the different misconfigurations and
click on the Resource associated with the **_GCP SQL database is publicly accessible_** policy.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-7.png "Optional title")

8. Note the side panel window after clicking the policy. Click on the **See policy documentation** hyperlink to see guidelines on how to resolve the
misconfiguration. Below is an example of how the policy box will look and the type of information you will see.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-8.png "Optional title")

9. Reviewing the guidelines we can see the description of this misconfiguration, its potential risk, and how to remediate at build time and runtime.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-9.png "Optional title")

10. Now navigate back to the Prisma Cloud platform. Click on the associated SQL database resource and view the helpful information about the misconfiguration such as its history, errors, details, and traceability. It also will give you an option to view the resource in its VCS.

![Alt text for image](/iac_screenshots/find-and-fix-insecure-infrastructure-code-10.png "Optional title")

11. This misconfiguration makes the database available to the internet. This database contains Personally Identifiable Information such as user credentials and financial information. By using Prisma Cloud code security, the Exampli Corp security team can avoid costly mistakes and protect the integrity of the database and application. 

**There are many other examples to explore in the Exampli repo, feel free to use the filters and search bar to explore additional resources and findings.**

#### Generating Pull Requests

1. Depending on the nature of a misconfiguration, Prisma Cloud can provide single click remediation. This capability creates a pull request that is sent to the version control system where the IaC template is stored.

Looking at the same **big_data.tf** template, there is another HIGH severity misconfiguration for lack of SSL on SQL database connections.

![Alt text for image](/iac_screenshots/generating-pull-requests-1.png "Optional title")

2. This resource is a fully managed relational database service for MySQL, PostgreSQL and SQL Server. It is recommended to enable SSL but we can see it is not configured in this resource's template.

**The Suppress and Fix capability requires increased RBAC capabilities and is not available to read-only users**

3. Pull requests have been created previously for various findings. To access the VCS associated with this IaC resource click the git link under the Details tab. We can also see the developer who committed the last change associated with this IaC resource.

![Alt text for image](/iac_screenshots/generating-pull-requests-3a.png "Optional title")

![Alt text for image](/iac_screenshots/generating-pull-requests-3b.png "Optional title")

4. After clicking the git link we see the last commit associated with the **big_data.tf** IaC template. Highlighted is the **ip_configuration** parameter which does not include the SSL enablement.

![Alt text for image](/iac_screenshots/generating-pull-requests-4.png "Optional title")

5. To view the fix generated by Prisma Cloud let's take a look at the pull requests tab at the top of the github UI.

![Alt text for image](/iac_screenshots/generating-pull-requests-5.png "Optional title")

6. Once on the Pull Requests view take a look at the [PR](https://github.com/c-haisten/Exampli/pull/1) and its associated [commit](https://github.com/c-haisten/Exampli/pull/1/commits/a79745d0a19acc3418db6fcd7ce40e11659de75a). Examining the changes, the ‘require_ssl = true’ setting has been added to the big_data.tf template.

![Alt text for image](/iac_screenshots/generating-pull-requests-6.png "Optional title")

Now that you have discovered the power and ease of remediating code fixes with Code security, let's explore how to detect and remediate exposed secrets embedded within Infrastructure as Code. 

#### Identify Exposed Secrets

According to the [2023 Verizon Data Breach Report](https://www.verizon.com/business/resources/T34b/reports/2023-data-breach-investigations-report-dbir.pdf), **74%** of all breaches include the human element, with people being involved either via Error, Privilege Misuse, Use of stolen credentials or Social Engineering.

Fortunately, Prisma Cloud makes it easy for anyone to quickly determine how the exposed secret can harm your organization and what steps must be taken. 

1. From the Projects page, click on the **Secrets** tab and click on the **AWS Access Keys** secret type for the **providers.tf** file. Make sure the SQL search from the previous exercise is cleared. 

![Alt text for image](/iac_screenshots/identify-exposed-secrets-1.png "Optional title")

2. Take a closer look at the **providers.tf** template in the side panel window. Here we can see an access key hardcoded in the template. Click on the **Manual Fix** button to be taken directly to the file on github. From there you can see the full template and make necessary changes. 

![Alt text for image](/iac_screenshots/identify-exposed-secrets-2a.png "Optional title")

![Alt text for image](/iac_screenshots/identify-exposed-secrets-2b-v2.png "Optional title")

3. When accessing AWS programmatically, users can select to use an access key to verify their identity, and the identity of their applications. An access key consists of an access key ID and a secret access key. Anyone with an access key has the same level of access to AWS resources. These exposed AWS access credentials could let an unauthorized attacker access the Exampli Corp AWS account.

**We recommend you protect access keys and keep them private. Specifically, do not store hard coded keys and secrets in infrastructure such as code, or other version-controlled configuration settings.**

#### Cloud Security Posture Management

Cloud-native applications allow organizations to build and run scalable applications with great agility and resilience. However, they also present unique security challenges. Ensuring applications and services are secure at runtime is a core responsibility for security teams. CSPM tools can reduce the burden on cloud security teams by automating routine security monitoring, audits and remediations, allowing security teams to focus on high-priority items and prevent configuration drift. 

In this section, you will explore some use cases for securing GKE resources and services.

1. Let's start with taking a look at the **Asset Inventory** page to get an idea of the scale of resources that are being monitored by this particular Prisma Cloud tenant.

2. Begin by clicking on the **Inventory** tab at the top and click on **Assets**.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-2.png "Optional title")

3. The **Asset Inventory** can be a helpful place to get a high level view on cloud assets and their compliance with defined policies. Without constant visibility of what is being deployed in your cloud footprint, you cannot begin to secure it.

4. Your screen should look similar to the one below:

![Alt text for image](/iac_screenshots/cloud-security-posture-management-4.png "Optional title")

5. We know the Exampli Corp team is using Kubernetes. Let's investigate further by clicking on **GCP** under the **Cloud** column. Here we can see all GCP services in use.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-5.png "Optional title")

6. We can see several issues associated with **Google Compute Engine**. Click on this service name to drill down further. On the next page we can see issues associated with **Google Compute Engine VM Instance**. Click on the Total assets to investigate.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-6.png "Optional title")

7. Click on the **gke-bank-of-anthos-default-pool-681c740d-ob6o** Asset Name. A side panel will open on the right side to quickly see an Overview, Attack Paths, Alerts, Vulnerabilities and more for this GKE cluster.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-7.png "Optional title")

8. Let's take a look at the raw config for this resource by clicking on the **View Config** button

![Alt text for image](/iac_screenshots/cloud-security-posture-management-8.png "Optional title")

9. Here we can see the raw configuration for this GKE cluster.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-9.png "Optional title")

10. To learn more let's investigate the findings associated with this GKE cluster. Close out the resource config and click on the **Attack Paths** tab.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-10.png "Optional title")

11. Note the **Findings Types** and see how they correlate with the Attack Path graph. Click on the **Internet Exposure** icon within the graph. Here we can see all the services associated with the host that make it vulnerable to outside attacks.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-11.png "Optional title")

12. Pan over to the right of the Attack Path graph and click on the **Privilege Escalation** icon within the graph. Here we can see that elevated privileges are assigned to the host. An adversary can destroy sensitive information stored in the cloud resources, making irreversible damage to Exampli's organization.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-12.png "Optional title")

13. Let's revisit the Internet Exposure finding by reviewing the associated alert and finding out the proper remediation. Click on the **Alerts** tab.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-13.png "Optional title")

14. Next, click on the Alerts ID for the **GCP VM instance that is internet reachable with unrestricted access (0.0.0.0/0)** policy. Here we can see an Overview as well as a Recommendation to resolve the issue.

![Alt text for image](/iac_screenshots/cloud-security-posture-management-14.png "Optional title")

15. Click on the **Recommendation** tab. Here we can see the necessary steps to fix the exposed asset. 

![Alt text for image](/iac_screenshots/cloud-security-posture-management-15.png "Optional title")

In addition to the asset inventory and leading cloud management capabilities, Prisma Cloud makes compliance incredibly easy. Let's move on to the next exercise on compliance.

#### Compliance in the Cloud

Many organizations struggle maintaining a grasp on compliance in the cloud. So far we have inspected Exampli's IaC and found some troubling trends. Additionally, at runtime many assets are still vulnerable due to risky configurations and careless development practices.

With multiple upcoming compliance audits bearing down on Exampli's CISO's calendar it's time to begin reporting on what is in a failed state and what needs to be done to get the organization's cloud footprint compliant.

1. Start by clicking the **Compliance** tab at the top of the UI. This page provides a high level view of all the unique assets and their associated adherence to various compliance frameworks and policies. When Prisma Cloud detects a violation of a policy it generates an alert.

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-1.png "Optional title")

2. Leverage the filters to adjust the results and select **GCP** for the **Cloud Type**. Refine the filters even more and select the compliance framework **CIS v1.1.0 (GCP)**.

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-2.png "Optional title")

3. Click on the Compliance Standard Name from the results to see which services have policies applied for that standard. Prisma Cloud provides a nice breakdown of each service type for easy referencing. 

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-3.png "Optional title")

4. This provides a focused view of Exampli's GCP footprint and its adherence to this CIS Standard.

It looks like the **Networking** service has a policy failure. Click on the red "i" icon under the **Failed** column. This will give us a view of the assets associated with the CIS policy failure. Your screen should look similar to the screen capture below :

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-4.png "Optional title")

5. Click on the **Alert ID** associated with the **default** Asset name. A side window opens that provides an Overview, Recommendation, Asset Config and Alert Rules. Additionally, the alert can be sent to a third party integration, like JIRA, for automated ticket creation. Reports can also be generated so you'll be ready for the next assessment and audit. 

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-5a.png "Optional title")

![Alt text for image](/iac_screenshots/compliance-in-the-cloud-5b.png "Optional title")

In this lab we covered various topics around IaC security and CSPM but there are many additional features and capabilities within Prisma Cloud. Feel free to explore the UI and investigate different asset types.

## Summary \ Resources

The Prisma Cloud team here at Palo Alto sincerely hopes you enjoyed this workshop. Today, organizations need a growing set of capabilities to secure modern cloud applications. Implementing DevSecOps into your development and security workflows with Prisma Cloud can provide the increased speed and agility your organization needs to implement zero trust throughout the entire development lifecycle. Check out the resources below for more information!

[Live Workshops](https://bridgecrew.io/resource/workshops/)

[DevSecTalks Podcast](https://www.paloaltonetworks.com/devsectalks/)

[Supply Chain Security](https://bridgecrew.io/software-supply-chain-security/)

[Cloud DevSecOps](https://bridgecrew.io/cloud-devsecops/)

[Learn More](https://www.paloaltonetworks.com/prisma/cloud)

##### Sample Git Repositories

[TerraGoat - Vulnerable by design Terraform Infrastructure](https://github.com/bridgecrewio/terragoat)

[Cfngoat - Vulnerable by design Cloudformation Template](https://github.com/bridgecrewio/cfngoat)

[CdkGoat - Vulnerable by design AWS CDK Infrastructure](https://github.com/bridgecrewio/cdkgoat)

[BicepGoat - Vulnerable by design Bicep and ARM Infrastructure](https://github.com/bridgecrewio/bicepgoat)

[KubernetesGoat-Vulnerable by design KubernetesCluster](https://github.com/bridgecrewio/kubernetes-goattest)

[KustomizeGoat - Vulnerable by design Kustomize deployment](https://github.com/bridgecrewio/kustomizegoat)

[SupplyGoat- Vulnerable by design SCA](https://github.com/bridgecrewio/supplygoat)



