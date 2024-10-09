---
copyright:
  years: 2024
lastupdated: "2024-02-27"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Overview

{: #overview}

This pattern describes Veeam for disaster recovery for VMware workloads where the protected and recovery sites are both hosted in {{site.data.keyword.Bluemix_notm}}. While this pattern describes a Veeam deployment that uses the add-on additional services available only with the {{site.data.keyword.Bluemix_notm}} vCenter Server platform, the pattern can be extrapolated for use with the VMware Cloud Foundation (VCF) in IBM Virtual Private Cloud (VPC) platform. The deployment of the Veeam server must be deployed manually by using {{site.data.keyword.Bluemix_notm}} licenses that are ordered through the {{site.data.keyword.Bluemix_notm}} for VMware Solutions console.

This pattern does not cover backup design and deployment pattern. {: note}

This pattern is cross-region, which assumes that the recovery site is in a different geographic region than the protected location, for example, the protected site is Frankfurt and the recovery location is Madrid. However, the pattern can use a recovery site in the same geographic region if it's required, for example, Frankfurt AZ1 and Frankfurt AZ3.

Check to ensure that the minimum distance between the protected and recovery sites meets your requirement. {: important}

The following image is the decision tree and is used to select Veeam as the disaster recovery product.

![A decision tree for disaster recovery for VMware workloads on IBM Cloud](image/decision_tree-Veeam.drawio.svg){: caption="Decision tree for disaster recovery for VMware workloads on IBM Cloud" caption-side="bottom"}
