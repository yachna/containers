---

copyright: 
  years: 2014, 2021
lastupdated: "2021-10-13"

keywords: kubernetes, iks

subcollection: containers
content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}



# Why does my cluster stay in a pending state?
{: #cs_cluster_pending}

**Infrastructure provider**:
    * ![Classic infrastructure provider icon.](images/icon-classic-2.svg) Classic
    * ![VPC infrastructure provider icon.](images/icon-vpc-2.svg) VPC


When you deploy your cluster, it remains in a pending state and doesn't start.
{: tsSymptoms}


If you just created the cluster, the worker nodes might still be configuring. If you already wait for a while, you might have an invalid VLAN.
{: tsCauses}



You can try one of the following solutions:
{: tsResolve}

    - Check the status of your cluster by running `ibmcloud ks cluster ls`. Then, check to be sure that your worker nodes are deployed by running `ibmcloud ks worker ls --cluster <cluster_name>`.
    - Check to see whether your VLAN is valid. To be valid, a VLAN must be associated with infrastructure that can host a worker with local disk storage. You can [list your VLANs](/docs/containers?topic=containers-kubernetes-service-cli#cs_vlans) by running `ibmcloud ks vlan ls --zone <zone>` if the VLAN does not show in the list, then it is not valid. Choose a different VLAN.


