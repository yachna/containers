---

copyright: 
  years: 2014, 2021
lastupdated: "2021-10-13"

keywords: kubernetes, iks

subcollection: containers
content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}



# Why do pods remain in pending state?
{: #ts-app-pod-pending}

**Infrastructure provider**:
* ![Classic infrastructure provider icon.](images/icon-classic-2.svg) Classic
* ![VPC infrastructure provider icon.](images/icon-vpc-2.svg) VPC


When you run `kubectl get pods`, you can see pods that remain in a **Pending** state.
{: tsSymptoms}


If you just created the Kubernetes cluster, the worker nodes might still be configuring.
{: tsCauses}

If this cluster is an existing one:
*  You might not have enough capacity in your cluster to deploy the pod.
*  The pod might have exceeded a resource request or limit.


This task requires the {{site.data.keyword.cloud_notm}} IAM [**Administrator** platform access role](/docs/containers?topic=containers-users#checking-perms) for the cluster and the [**Manager** service access role](/docs/containers?topic=containers-users#checking-perms) for all namespaces.
{: tsResolve}

If you just created the Kubernetes cluster, run the following command and wait for the worker nodes to initialize.

```sh
kubectl get nodes
```
{: pre}

If this cluster is an existing one, check your cluster capacity.


1. Set the proxy with the default port number.

    ```sh
    kubectl proxy
    ```
    {: pre}

2. Open the Kubernetes dashboard.

    ```
    http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
    ```
    {: pre}

3. Check if you have enough capacity in your cluster to deploy your pod.

4. If you don't have enough capacity in your cluster, resize your worker pool to add more nodes.

    1. Review the current sizes and flavors of your worker pools to decide which one to resize.

        ```sh
        ibmcloud ks worker-pool ls
        ```
        {: pre}

    2. Resize your worker pools to add more nodes to each zone that the pool spans.

        ```sh
        ibmcloud ks worker-pool resize --worker-pool <worker_pool> --cluster <cluster_name_or_ID> --size-per-zone <workers_per_zone>
        ```
        {: pre}

5. Optional: Check your pod resource requests.

    1. Confirm that the `resources.requests` values are not larger than the worker node's capacity. For example, if the pod request `cpu: 4000m`, or 4 cores, but the worker node size is only 2 cores, the pod cannot be deployed.

        ```sh
        kubectl get pod <pod_name> -o yaml
        ```
        {: pre}

    2. If the request exceeds the available capacity, [add a new worker pool](/docs/containers?topic=containers-add_workers#add_pool) with worker nodes that can fulfill the request.

6. If your pods still stay in a **pending** state after the worker node is fully deployed, review the [Kubernetes documentation](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-pod-replication-controller/#my-pod-stays-pending){: external} to further troubleshoot the pending state of your pod.






