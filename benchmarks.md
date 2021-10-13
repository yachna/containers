---

copyright: 
  years: 2014, 2021
lastupdated: "2021-10-13"

keywords: kubernetes, iks, containers

subcollection: containers

---

{{site.data.keyword.attribute-definition-list}}



# CIS Kubernetes Benchmark
{: #cis-benchmark}

The Center for Internet Security (CIS) publishes the [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/){: external} as a framework of specific steps to configure Kubernetes more securely and with standards that are commensurate to various industry regulations.
{: shortdesc}

When a new [Kubernetes version](/docs/containers?topic=containers-cs_versions) is released, IBM engineers compare the default configuration of a cluster that runs that Kubernetes version against the benchmark and publishes the results in this documentation. You can review how specific versions of your {{site.data.keyword.containerlong}} clusters meet the CIS Kubernetes Benchmark.

## Using the benchmark
{: #cis-benchmark-use}

As a security administrator or auditor, you might want to compare your company's internal standards and external regulatory requirements with the CIS Kubernetes Benchmark. The benchmark recommendations are provided by the Center for Internet Security, not by IBM. IBM might not configure default settings in a way that meets every recommendation, but documents whether the recommendation is met to help you in your review. For example, you might use the benchmark in an audit to confirm that basic security measures are in place, and to identify areas where you might enhance your security.
{: shortdesc}

**What does the benchmark cover?**

The benchmark covers recommendations for master components, etcd, control plane configurations, worker nodes, and policies such as for users, network, and pod security. 

**What do the benchmark recommendations mean?**

The benchmark recommendations have scoring, levels, result status, and responsibilities as follows.

* **Scoring**
    * Scored: The overall benchmark score increases or decreases depending on whether or not the  recommendation is met.
    * Not scored: The overall benchmark score is not impacted, whether or not the recommendation is met.
* **Levels**
    * Level 1: Practical security measures that can be configured without inhibiting the service.
    * Level 2: More in-depth security measures that might reduce the performance or functionality of a service.
* **Result**
    * Pass: The service complies with the benchmark recommendation.
    * Fail: The service does not comply with the benchmark recommendation by default. Refer to the remediation section for an explanation and possible actions that you can take to comply with the benchmark recommendation.
* **Responsibility**
    * IBM: IBM is responsible for configuring the setting that the benchmark recommends.
    * Shared: You and IBM share responsibility for configuring the setting that the benchmark recommends.

**What parts of the benchmark am I responsible for?**

Because {{site.data.keyword.containerlong_notm}} is a managed offering, IBM already configures many security settings for you. For example, IBM manages and automatically applies updates to your cluster master. For your worker nodes, IBM provides security and version updates, but you must apply the updates. You are also responsible for your workload applications and data. For more information, see [Your responsibilities while using {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-responsibilities_iks).

**What if some part of the service fails to comply with a recommendation?**

First, check the explanation of the failure for any remediation steps.

Then, determine whether the failure is acceptable according to your security requirements. For example, some recommendations might be more in-depth configuration requirements than your particular processes or standards require. Also, some recommendations are not scored, and do not impact the overall benchmark score.

Next, decide whether the component falls within your responsibility. If so, you might need to change how you configure that component. For example, you might configure pod security policies for all your app deployments. For components that are not directly within your responsibility, assess whether you can use another {{site.data.keyword.cloud_notm}} service to meet the recommendation.

**What else can I do to increase the security and compliance of my cluster?**

See [Security for {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-security).



## Running the worker node CIS Kubernetes benchmark
{: #cis-worker-test}

To review the results of the CIS Kubernetes benchmark for [Section 4: Worker node security configuration](#cis-benchmark-15-4), you can run the test yourself. Because you own the worker nodes and are partially [responsible](/docs/containers?topic=containers-responsibilities_iks) for their compliance, you might make configuration changes that you want to validate on your own.
{: shortdesc}



Before you begin: [Log in to your account. If applicable, target the appropriate resource group. Set the context for your cluster.](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)

1. Create a namespace for the resources to run the benchmark.
    ```sh
    kubectl create ns ibm-kube-bench-test
    ```
    {: pre}

2. Create a configmap with the `config` and `node` configuration files from the [kube-samples](https://github.com/IBM-Cloud/kube-samples/tree/master/cis-kube-benchmark/cis-1.5/ibm){: external} GitHub repository.
    1. Download the the `config` and `node` configuration files into a local directory that is called `ibm`. You can also clone the repository and navigate into the `ibm` directory.
        * [`config` file](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/cis-kube-benchmark/cis-1.5/ibm/config.yaml){: external}
        * [`node` file](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/cis-kube-benchmark/cis-1.5/ibm/node.yaml){: external}
    2. Create the configmap by using the `--from-file` flag to specify the `ibm` directory where your downloaded the configuration files.
        ```sh
        kubectl create cm kube-bench-node -n ibm-kube-bench-test --from-file ibm
        ```
        {: pre}

3. Create a job to run the benchmark test based on the configurations that you previously created.
    ```sh
    kubectl apply -n ibm-kube-bench-test -f https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/cis-kube-benchmark/cis-1.5/ibm/job-node.yaml
    ```
    {: pre}

4. Check that the job successfully completed.
    ```sh
    kubectl get pods -n ibm-kube-bench-test -l job-name=kube-bench-node
    ```
    {: pre}

    Example output

    ```sh
    NAME                    READY   STATUS      RESTARTS   AGE
    kube-bench-node-hlvhc   0/1     Completed   0          23s
    ```
    {: screen}

5. Review the CIS Kubernetes benchmark results for your worker nodes by checking the pod logs.
    ```sh
    kubectl logs -n ibm-kube-bench-test -l job-name=kube-bench-node --tail=-1
    ```
    {: pre}

    Example output

    ```sh
    == Summary ==
    20 checks PASS
    2 checks FAIL
    1 checks WARN
    0 checks INFO
    ```
    {: screen}

6. Optional: When you are done reviewing results, delete the resources that you created.
    ```sh
    kubectl delete ns ibm-kube-bench-test
    ```
    {: pre}



## Benchmark 1.5 results for Kubernetes versions 1.18 - 1.21
{: #cis-benchmark-15}

Review how {{site.data.keyword.containerlong_notm}} complies with the version 1.5 CIS Kubernetes benchmark for clusters that run Kubernetes versions 1.18 - 1.21. For help understanding the benchmark, see [Using the benchmark](#cis-benchmark-use).
{: shortdesc}




### 1.1 Master Node Configuration Files
{: #cis-benchmark-15-1-1}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 1.1.1 | Ensure that the API server pod specification file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.2 | Ensure that the API server pod specification file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.3 | Ensure that the controller manager pod specification file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.4 | Ensure that the controller manager pod specification file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.5 | Ensure that the scheduler pod specification file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.6 | Ensure that the scheduler pod specification file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.7 | Ensure that the etcd pod specification file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.8 | Ensure that the etcd pod specification file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.9 | Ensure that the Container Network Interface file permissions are set to 644 or more restrictive | Not Scored | 1 | Pass | IBM |
| 1.1.10 | Ensure that the Container Network Interface file ownership is set to root:root | Not Scored | 1 | Pass | IBM |
| 1.1.11 | Ensure that the etcd data directory permissions are set to 700 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.12 | Ensure that the etcd data directory ownership is set to etcd:etcd | Scored | 1 | Pass | IBM |
| 1.1.13 | Ensure that the admin.conf file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.14 | Ensure that the admin.conf file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.15 | Ensure that the scheduler.conf file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.16 | Ensure that the scheduler.conf file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.17 | Ensure that the controller-manager.conf file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.18 | Ensure that the controller-manager.conf file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.19 | Ensure that the Kubernetes PKI directory and file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 1.1.20 | Ensure that the Kubernetes PKI certificate file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 1.1.21 | Ensure that the Kubernetes PKI key file permissions are set to 600 | Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 1.1 Master node benchmark results" caption-side="top"}

### 1.2 API Server
{: #cis-benchmark-15-1-2}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 1.2.1 | Ensure that the `--anonymous-auth` option is set to false | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 1.2.2 | Ensure that the `--basic-auth-file` option is not set | Scored | 1 | Pass | IBM |
| 1.2.3 | Ensure that the `--token-auth-file` parameter is not set | Scored | 1 | Pass | IBM |
| 1.2.4 | Ensure that the `--kubelet-https` option is set to true | Scored | 1 | Pass | IBM |
| 1.2.5 | Ensure that the `--kubelet-client-certificate` and `--kubelet-client-key` options are set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.6 | Ensure that the `--kubelet-certificate-authority` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.7 | Ensure that the `--authorization-mode` option is not set to AlwaysAllow | Scored | 1 | Pass | IBM |
| 1.2.8 | Ensure that the `--authorization-mode` option includes Node | Scored | 1 | Pass | IBM |
| 1.2.9 | Ensure that the `--authorization-mode` option includes RBAC | Scored | 1 | Pass | IBM |
| 1.2.10 | Ensure that the admission control plugin EventRateLimit is set | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 1.2.11 | Ensure that the admission control plugin AlwaysAdmit is not set | Scored | 1 | Pass | IBM |
| 1.2.12 | Ensure that the admission control plugin AlwaysPullImages is set | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 1.2.13 | Ensure that the admission control plugin SecurityContextDeny is set if PodSecurityPolicy is not used | Not Scored | 1 | Pass | IBM |
| 1.2.14 | Ensure that the admission control plugin ServiceAccount is set | Scored | 1 | Pass | IBM |
| 1.2.15 | Ensure that the admission control plugin NamespaceLifecycle is set | Scored | 1 | Pass | IBM |
| 1.2.16 | Ensure that the admission control plugin PodSecurityPolicy is set | Scored | 1 | Pass | IBM |
| 1.2.17 | Ensure that the admission control plugin NodeRestriction is set | Scored | 1 | Pass | IBM |
| 1.2.18 | Ensure that the `--insecure-bind-address` option is not set | Scored | 1 | Pass | IBM |
| 1.2.19 | Ensure that the `--insecure-port` option is set to 0 | Scored | 1 | Pass | IBM |
| 1.2.20 | Ensure that the `--secure-port` option is not set to 0 | Scored | 1 | Pass | IBM |
| 1.2.21 | Ensure that the `--profiling` option is set to false | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 1.2.22 | Ensure that the `--audit-log-path` option is set | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.23 | Ensure that the `--audit-log-maxage` option is set to 30 or as appropriate | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.24 | Ensure that the `--audit-log-maxbackup` option is set to 10 or as appropriate | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.25 | Ensure that the `--audit-log-maxsize` option is set to 100 or as appropriate | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.26 | Ensure that the `--request-timeout` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.27 | Ensure that the `--service-account-lookup` option is set to true | Scored | 1 | Pass | IBM |
| 1.2.28 | Ensure that the `--service-account-key-file` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.29 | Ensure that the `--etcd-certfile` and `--etcd-keyfile` options are set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.30 | Ensure that the `--tls-cert-file` and `--tls-private-key-file` options are set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.31 | Ensure that the `--client-ca-file` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.32 | Ensure that the `--etcd-cafile` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.2.33 | Ensure that the `--encryption-provider-config` option is set as appropriate | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.34 | Ensure that encryption providers are appropriately configured | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 1.2.35 | Ensure that the API Server only makes use of Strong Cryptographic Ciphers | Not Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 1.2 API server benchmark results" caption-side="top"}

### 1.3 Controller Manager
{: #cis-benchmark-15-1-3}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 1.3.1 | Ensure that the `--terminated-pod-gc-threshold` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.3.2 | Ensure that the `--profiling` option is set to false | Scored | 1 | Pass | IBM |
| 1.3.3 | Ensure that the `--use-service-account-credentials` option is set to true | Scored | 1 | Pass | IBM |
| 1.3.4 | Ensure that the `--service-account-private-key-file` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.3.5 | Ensure that the `--root-ca-file` option is set as appropriate | Scored | 1 | Pass | IBM |
| 1.3.6 | Ensure that the RotateKubeletServerCertificate option is set to true | Scored | 2 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 1.3.7 | Ensure that the `--bind-address` option is set to 127.0.0.1 | Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 1.3 Controller manager benchmark results" caption-side="top"}

### 1.4 Scheduler
{: #cis-benchmark-15-1-4}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 1.4.1 | Ensure that the `--profiling option` is set to false | Scored | 1 | Pass | IBM |
| 1.4.2 | Ensure that the `--bind-address` option is set to 127.0.0.1 | Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 1.4 Scheduler benchmark results" caption-side="top"}

## 2 `etcd` Node Configuration
{: #cis-benchmark-15-2}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 2.1 | Ensure that the `--cert-file` and `--key-file` options are set as appropriate | Scored | 1 | Pass | IBM |
| 2.2 | Ensure that the `--client-cert-auth` option is set to true | Scored | 1 | Pass | IBM |
| 2.3 | Ensure that the `--auto-tls option` is not set to true | Scored | 1 | Pass | IBM |
| 2.4 | Ensure that the `--peer-cert-file` and `--peer-key-file` options are set as appropriate | Scored | 1 | Pass | IBM |
| 2.5 | Ensure that the `--peer-client-cert-auth` option is set to true | Scored | 1 | Pass | IBM |
| 2.6 | Ensure that the `--peer-auto-tls` option is not set to true | Scored | 1 | Pass | IBM |
| 2.7 | Ensure that a unique Certificate Authority is used for etcd | Not Scored | 2 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 2 etcd benchmark results" caption-side="top"}

## 3 Control Plane Configuration
{: #cis-benchmark-15-3}

### 3.1 Authentication and Authorization
{: #cis-benchmark-15-3-1}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 3.1.1 | Client certificate authentication should not be used for users | Not Scored | 2 | Pass | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 3.1 Authorization benchmark results" caption-side="top"}

### 3.2 Logging
{: #cis-benchmark-15-3-2}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 3.2.1 | Ensure that a minimal audit policy is created | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 3.2.2 | Ensure that the audit policy covers key security concerns | Not Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 3.2 Logging benchmark results" caption-side="top"}

## 4 Worker Node Security Configuration
{: #cis-benchmark-15-4}

### 4.1 Worker Node Configuration Files
{: #cis-benchmark-15-4-1}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 4.1.1 | Ensure that the kubelet service file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 4.1.2 | Ensure that the kubelet service file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 4.1.3 | Ensure that the proxy kubeconfig file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 4.1.4 | Ensure that the proxy kubeconfig file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 4.1.5 | Ensure that the kubelet.conf file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 4.1.6 | Ensure that the kubelet.conf file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 4.1.7 | Ensure that the certificate authorities file permissions are set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 4.1.8 | Ensure that the client certificate authorities file ownership is set to root:root | Scored | 1 | Pass | IBM |
| 4.1.9 | Ensure that the kubelet configuration file has permissions set to 644 or more restrictive | Scored | 1 | Pass | IBM |
| 4.1.10 | Ensure that the kubelet configuration file ownership is set to root:root | Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 4.1 Worker node benchmark results" caption-side="top"}

### 4.2 Kubelet
{: #cis-benchmark-15-4-2}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 4.2.1 | Ensure that the `--anonymous-auth` option is set to false | Scored | 1 | Pass | IBM |
| 4.2.2 | Ensure that the `--authorization-mode` option is not set to AlwaysAllow | Scored | 1 | Pass | IBM |
| 4.2.3 | Ensure that the `--client-ca-file` option is set as appropriate | Scored | 1 | Pass | IBM |
| 4.2.4 | Ensure that the `--read-only-port` option is set to 0 | Scored | 1 | Pass | IBM |
| 4.2.5 | Ensure that the `--streaming-connection-idle-timeout` option is not set to 0 | Scored | 1 | Pass | IBM |
| 4.2.6 | Ensure that the `--protect-kernel-defaults` option is set to true | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 4.2.7 | Ensure that the `--make-iptables-util-chains` option is set to true | Scored | 1 | Pass | IBM |
| 4.2.8 | Ensure that the `--hostname-override` option is not set | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 4.2.9 | Ensure that the `--event-qps` option is set to 0 or a level which ensures appropriate event capture | Not Scored | 2 | Pass | IBM |
| 4.2.10 | Ensure that the `--tls-cert-file` and `--tls-private-key-file` options are set as appropriate | Scored | 1 | Pass | IBM |
| 4.2.11 | Ensure that the `--rotate-certificates` option is not set to false | Scored | 1 | [Pass](#ibm-remediations-and-explanations) | IBM |
| 4.2.12 | Ensure that the RotateKubeletServerCertificate option is set to true | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | IBM |
| 4.2.13 | Ensure that the Kubelet only makes use of Strong Cryptographic Ciphers | Not Scored | 1 | Pass | IBM |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 4.2 Kubelet benchmark results" caption-side="top"}

## 5 Kubernetes Policies
{: #cis-benchmark-15-5}

### 5.1 RBAC and Service Accounts
{: #cis-benchmark-15-5-1}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.1.1 | Ensure that the cluster-admin role is only used where required | Not Scored | 1 | Pass | Shared |
| 5.1.2 | Minimize access to secrets | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 5.1.3 | Minimize wildcard use in Roles and ClusterRoles | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 5.1.4 | Minimize access to create pods | Not Scored | 1 | Pass | Shared |
| 5.1.5 | Ensure that default service accounts are not actively used. | Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 5.1.6 | Ensure that Service Account Tokens are only mounted where necessary | Not Scored | 1 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.1 RBAC benchmark results" caption-side="top"}

### 5.2 Pod Security Policies
{: #cis-benchmark-15-5-2}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.2.1 | Minimize the admission of privileged containers | Not Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.2 | Minimize the admission of containers wishing to share the host process ID namespace | Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.3 | Minimize the admission of containers wishing to share the host IPC namespace | Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.4 | Minimize the admission of containers wishing to share the host network namespace | Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.5 | Minimize the admission of containers with allowPrivilegeEscalation | Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.6 | Minimize the admission of root containers | Not Scored | 2 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.7 | Minimize the admission of containers with the NET_RAW capability | Not Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.8 | Minimize the admission of containers with added capabilities | Not Scored | 1 | [Pass](#ibm-remediations-and-explanations) | Shared |
| 5.2.9 | Minimize the admission of containers with capabilities assigned | Not Scored | 2 | [Pass](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.2 Pod security benchmark results" caption-side="top"}

### 5.3 Network Policies and CNI
{: #cis-benchmark-15-5-3}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.3.1 | Ensure that the CNI in use supports Network Policies | Not Scored | 1 | Pass | IBM |
| 5.3.2 | Ensure that all Namespaces have Network Policies defined | Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.3 Network policies benchmark results" caption-side="top"}

### 5.4 Secrets Management
{: #cis-benchmark-15-5-4}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.4.1 | Prefer using secrets as files over secrets as environment variables | Not Scored | 1 | Pass | Shared |
| 5.4.2 | Consider external secret storage | Not Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.4 Secrets manager benchmark results" caption-side="top"}

### 5.5 Extensible Admission Control
{: #cis-benchmark-15-5-5}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.5.1 | Configure Image Provenance using ImagePolicyWebhook admission controller | Not Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.6 Extensible admission control benchmark results" caption-side="top"}

### 5.6 General Policies
{: #cis-benchmark-15-5-6}

| # | Recommendation | Scored/Not Scored | Level | Result | Responsibility |
| --- | --- | --- | --- | --- | --- |
| 5.6.1 | Create administrative boundaries between resources using namespaces | Not Scored | 1 | Pass | Shared |
| 5.6.2 | Ensure that the seccomp profile is set to docker/default in your pod definitions | Not Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 5.6.3 | Apply Security Context to Your Pods and Containers | Not Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
| 5.6.4 | The default namespace should not be used | Scored | 2 | [Fail](#ibm-remediations-and-explanations) | Shared |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Section 5.6 General policies benchmark results" caption-side="top"}

### Explanations and remediation
{: #ibm-remediations-and-explanations}

Review the following explanations and possible remediation actions that you can take to address recommendations that are not met in the default configuration for {{site.data.keyword.containerlong_notm}} version 1.18 clusters.
{: shortdesc}

| # | Remediation/Explanation |
| --- | --- |
| 1.2.1 | IKS utilizes RBAC for cluster protection, but allows anonymous discovery, which is considered reasonable per [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/). |
| 1.2.10 | IKS does not enable the [*EventRateLimit*](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#eventratelimit) admission controller since it is a Kubernetes alpha feature. |
| 1.2.12 | IKS does not enable the [*AlwaysPullImages*](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#alwayspullimages) admission controller since it overrides a container's *imagePullPolicy* and may impact performance. |
| 1.2.21 | IKS enables [profiling](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) for cluster administrator troubleshooting purposes. |
| 1.2.22 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 1.2.23 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 1.2.24 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 1.2.25 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 1.2.33 | IKS can optionally [enable a Kubernetes Key Management Service (KMS) provider](/docs/containers?topic=containers-encryption#kms). |
| 1.2.34 | IKS can optionally [enable a Kubernetes Key Management Service (KMS) provider](/docs/containers?topic=containers-encryption#kms). |
| 1.3.6 | IKS rotates certificates on every worker node reload or update. |
| 3.2.1 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 3.2.2 | IKS can optionally [enable Kubernetes API server auditing](/docs/containers?topic=containers-health-audit#audit-api-server). |
| 4.2.6 | IKS does not protect kernel defaults in order to allow customers to [tune kernel parameters](/docs/containers?topic=containers-kernel). |
| 4.2.8 | IKS ensures that the hostname matches the name issued by the infrastructure. |
| 4.2.11 | IKS rotates certificates on every worker node reload or update. |
| 4.2.12 | IKS rotates certificates on every worker node reload or update. |
| 5.1.2 | IKS deploys some system components that could have their Kubernetes secret access further restricted. |
| 5.1.3 | IKS deploys some system components that could have their Kubernetes resource access further restricted. |
| 5.1.5 | IKS does not set [*automountServiceAccountToken: false*](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#use-the-default-service-account-to-access-the-api-server) for each default service account. |
| 5.1.6 | IKS deploys some system components that could set [*automountServiceAccountToken: false*](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#use-the-default-service-account-to-access-the-api-server). |
| 5.2.1 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.2 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.3 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.4 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.5 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.6 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.7 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.8 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.2.9 | IKS can optionally [configure pod security policies](/docs/containers?topic=containers-psp). |
| 5.3.2 | IKS has a set of [default Calico and Kubernetes network policies defined](/docs/containers?topic=containers-network_policies#default_policy) and [additional network policies can optionally be added](/docs/containers?topic=containers-network_policies#adding_network_policies). |
| 5.4.2 | IKS can optionally [enable a Kubernetes Key Management Service (KMS) provider](/docs/containers?topic=containers-encryption#kms). |
| 5.5.1 | IKS can optionally [enable image security enforcement](/docs/containers?topic=containers-images#portieris-image-sec). |
| 5.6.2 | IKS does not annotate all pods with [seccomp profiles](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp). |
| 5.6.3 | IKS deploys some system components that do not set a [pod or container *securityContext*](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/). |
| 5.6.4 | IKS deploys some Kubernetes resources to the default names. |
{: summary="The rows are read from left to right. The first column is the section number for the benchmark recommendation. The second column is the benchmark recommendation. The third column is the scoring of the recommendation, either scored or not scored. The fourth column is the level of the recommendation, either 1 for basic or 2 for more advanced and performance-impacting. The fifth column contains the result of whether the service passes or fails the recommendation. The sixth column designates the responsibility of passing the recommendation, either IBM or shared between IBM and you."}
{: caption="Explanation and remediation" caption-side="top"}







