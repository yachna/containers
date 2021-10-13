---

copyright: 
  years: 2014, 2021
lastupdated: "2021-10-13"

keywords: kubernetes, iks, help, network, connectivity

subcollection: containers
content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}



# Classic: Why do I see a timeout error when I try to log in to a pod on a new worker node?
{: #cs_nodes_duplicate_ip}

**Infrastructure provider**: ![Classic infrastructure provider icon.](images/icon-classic-2.svg) Classic


You deleted a worker node in your cluster and then added a worker node. When you deployed a pod or Kubernetes service, the resource cannot access the newly created worker node, and the connection times out.
{: tsSymptoms}


If you delete a worker node from your cluster and then add a worker node, the new worker node might be assigned the private IP address of the deleted worker node. Calico uses this private IP address as a tag and continues to try to reach the deleted node.
{: tsCauses}


Manually update the reference of the private IP address to point to the correct node.
{: tsResolve}

1. Confirm that you have two worker nodes with the same **Private IP** address. Note the **Private IP** and **ID** of the deleted worker.

    ```sh
    ibmcloud ks worker ls --cluster <cluster_name_or_id>
    ```
    {: pre}

    ```
    ID                                                 Public IP       Private IP       Machine Type   State     Status   Zone   Version
    kube-dal10-cr9b7371a7fcbe46d08e04f046d5e6d8b4-w1   169.xx.xxx.xxx  10.xxx.xx.xxx    b3c.4x16       normal    Ready    dal10      1.20.11
    kube-dal10-cr9b7371a7fcbe46d08e04f046d5e6d8b4-w2   169.xx.xxx.xxx  10.xxx.xx.xxx    b3c.4x16       deleted    -       dal10      1.20.11
    ```
    {: screen}

2. Install the [Calico CLI](/docs/containers?topic=containers-network_policies#cli_install).
3. List the available worker nodes in Calico. Replace <path_to_file> with the local path to the Calico configuration file.

    ```sh
    calicoctl get nodes --config=filepath/calicoctl.cfg
    ```
    {: pre}

    ```sh
    NAME
    kube-dal10-cr9b7371a7faaa46d08e04f046d5e6d8b4-w1
    kube-dal10-cr9b7371a7faaa46d08e04f046d5e6d8b4-w2
    ```
    {: screen}

4. Delete the duplicate worker node in Calico. Replace NODE_ID with the worker node ID.

    ```sh
    calicoctl delete node NODE_ID --config=<path_to_file>/calicoctl.cfg
    ```
    {: pre}

5. Reboot the worker node that was not deleted.

    ```sh
    ibmcloud ks worker reboot --cluster <cluster_name_or_id> --worker <worker_id>
    ```
    {: pre}


The deleted node is no longer listed in Calico.


