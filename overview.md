---
copyright:
  years: 2023
lastupdated: "2024-1-30"

subcollection: <vmware-cross-region-dr>

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Deploy IBM Cloud resiliency design for Veeam on VMWare

{: \#overview}

Overview

This pattern describes Veeam for disaster recovery for VMware workloads where both the protected and recovery sites are in IBM Cloud. While this pattern describes a Veeam deployment using the add-on additional services only available with the IBM Cloud vCenter Server platform, the pattern can be extrapolated for use with the VCF in VPC platform, however, the deployment of the Veeam server will need to be done manually using IBM Cloud licenses ordered through the IBM Cloud for VMware Solutions console.

This pattern is cross-region, which assumes that the recovery site is in a different geographic region than the protected location e.g. protected site is Frankfurt and the recovery location is Madrid. However, if required the pattern can use a recovery site in the same geographic region if required e.g. Frankfurt AZ1 and Frankfurt AZ3.

**This pattern does not cover backup design and deployment pattern.**{: note}

Check to ensure that the minimum distance between the protected and recovery sites meets your requirement. {: important}

The decision tree, shown below, has been used to select Veeam as the disaster recover product.

![A diagram of a workflow Description automatically generated](image/decision_tree-Veeam.drawio.svg)
