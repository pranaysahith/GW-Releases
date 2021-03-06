# GW-Releases

## Latest tag from main

![Alt text](graph/graph_latest_tag.png)

## Branch : filedrop-centos

![Alt text](graph/graph_filedrop-centos.png)

## Branch : icap-centos

![Alt text](graph/graph_icap-centos.png)

## Relevant Repos

- [k8-rebuild](https://github.com/k8-proxy/k8-rebuild/)
    - [k8-rebuild-rest-api](https://github.com/k8-proxy/k8-rebuild-rest-api)
        - [sdk-rebuild-eval](https://github.com/filetrust/sdk-rebuild-eval)
    - [k8-rebuild-file-drop](https://github.com/k8-proxy/k8-rebuild-file-drop)
- [vmware-scripts](https://github.com/k8-proxy/vmware-scripts)
- [icap-server](https://github.com/k8-proxy/icap-infrastructure)
- [proxy-server](https://github.com/k8-proxy/s-k8-proxy-rebuild)
- [load-balancer](https://github.com/k8-proxy/gp-load-balancer)
- [traffic-generator](https://github.com/k8-proxy/aws-jmeter-test-engine)
- [GW-proxy](https://github.com/k8-proxy/GW-proxy)

## File Drop
Download [File Drop OVA]()
- File Drop AMI on AWS:
   ```
   ID: ami-0e82f434cc76a48a7
   Owner: 785217600689
   Region: eu-west-1/Ireland
   ```
- Live instances running:
   ```
   AWS: http://54.246.155.1/
   ```
## Workflows (AWS)

### Workflows Brief
- GW-Releases work with 3 main workflows
    - [icap-server](https://github.com/k8-proxy/GW-Releases/actions?query=workflow%3Aicap-server)
    - [proxy-rebuild](https://github.com/k8-proxy/GW-Releases/actions?query=workflow%3Aproxy-rebuild)
    - [k8-rebuild](https://github.com/k8-proxy/GW-Releases/actions?query=workflow%3Ak8-rebuild)

- Each commit made to the main branch triggers the workflow, except for the directories under the `paths-ignore` variable in the following workflows:
    - [haproxy.yaml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/haproxy.yaml)
    - [k8-rebuild.yaml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/k8-rebuild.yaml)
    - [monitoring.yaml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/monitoring.yaml)
    - [proxy-rebuild-ova.yaml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/proxy-rebuild-ova.yaml)
    - [proxy-rebuild.yaml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/proxy-rebuild.yaml)
    - [visualization.yml](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/visualization.yml)

- There are 2 main jobs for each workflow:

    - build AMI
        - Configure AWS credentials
        - Setup [Packer](https://github.com/k8-proxy/vmware-scripts/tree/main/packer)
        - Build AMI using Packer 

        ![build-ami](imgs/build-ami.png)

    - deploy AMI
        - Get current instance ID and other instance IDs with the same name as current instance
        - Deploy the instance
        - Run [healthcheck tests](https://github.com/k8-proxy/vmware-scripts/tree/f129ec357284c61206edf36415b1b2ba403bff95/HealthCheck) on the instance
            - if tests are successful for current instance, all previous instances are terminated
            - if tests are failed, current instance is terminated and deleted

        ![deploy-ami](imgs/deploy-ami.png)



### ICAP Server Workflow
- K3s ICAP - [YAML File](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/icap-server.yaml) 

    - **Worflow Requirements**

         ![image](https://user-images.githubusercontent.com/17300331/119994330-22adcc80-bfea-11eb-9b57-15c176882277.png)


- CK8 ICAP - [YAML File](https://github.com/k8-proxy/k8s-compliant-kubernetes/actions/workflows/complaint-k8s-CloudSDK.yaml)

    - **Worflow Requirements**

        ![image](https://user-images.githubusercontent.com/17300331/119994452-3e18d780-bfea-11eb-85c5-151d1bbc4272.png)


#### K3s ICAP Server Workflow

Below inputs are to be supplied for K3s based ICAP server workflow

```
icap-infrastructe branch to be used - k8-main
Extra regions where AMI should be published. Pass multiple regions with comma separated. - eu-west-1
classic vs golang (GoLang and minio based) - Golang version uses minio based golang implementation
Management UI Required - true or false
Install GW Cloud REST API - true or false
cs-k8s-api docker image - glasswallsolutions/cs-k8s-api:latest
Install filedrop UI, this will install cs-k8s-api too - true or false
Create OVA - true or false
```

- Use workflow inputs to customize the AMI/OVA build:
  - icap_branch: Pass the k8-proxy/icap-infrastructure repository branch to be used. This repo has the helm charts for ICAP server. If not sure, use the default branch `k8-main`
  - extra_regions: If the AMI is needed in AWS region(s) other than `eu-west-1` pass the regions in comma separated format.
  - icap_flavour: `classic` deploys original icap implementation and `golang` deploys GoLang based services along with minio.
  - install_csapi: Pass `true` to install Glasswall Cloud Rebuild API.
  - cs_api_image: Docker image of Glasswall CLoud Rebuild API. Use default `glasswallsolutions/cs-k8s-api:latest` if not sure.
  - install_filedrop_ui: Pass `true` to install Filedrop website. Installing Filedrop also installs Glasswall Cloud Rebuild API
  - create_ova: Pass `true` if OVA is needed.

#### CK8 ICAP Server Workflow

Workflow [YAML File](https://github.com/k8-proxy/k8s-compliant-kubernetes/actions/workflows/complaint-k8s-CloudSDK.yaml)

```
classic vs golang (GoLang and minio based) - Golang version uses minio based golang implementation
Management UI Required - true or false
Install GW Cloud REST API - true or false
GW Cloud REST (cs-k8s-api) API docker image - glasswallsolutions/cs-k8s-api:latest
Install filedrop UI, this will install GW Cloud REST API too - true or false
Create Workload Cluster - true or false
Create Service Cluster - true or false
Create OVA - true or false
```

All secrets specfic to Service cluster will be uploaded to  s3 bucket: `glasswall-dev-sc-logs`

### K8 Rebuild Workflow
- [YAML File](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/k8-rebuild.yaml)

### Proxy Rebuild Workflow
- [YAML File](https://github.com/k8-proxy/GW-Releases/blob/main/.github/workflows/proxy-rebuild.yaml)

- deploy AMI (follows [workflow jobs](https://github.com/k8-proxy/GW-Releases/blob/main/README.md#workflows-brief) with an additional step)
    - Run tests on instance
        - Download this [PDF](https://glasswallsolutions.com/wp-content/uploads/2020/01/Glasswall-d-FIRST-Technology.pdf) file
        - Make sure it successfully has the watermark `"Glasswall Processed"`
            - if tests are successful for current instance, all previous instances are terminated
            - if tests are failed, current instance is terminated and deleted

## SOW version branches
 - ICAP server SOW version uses below repos adnd branches:
    -  GW-Releases ([icap-centos](https://githb.com/k8-proxy/GW-Releases/tree/icap-centos))
       -  vmware-scripts ([icap-centos](https://github.com/k8-proxy/vmware-scripts/tree/icap-centos))
 - File drop SOW version uses below repos and branches:
   - GW-Releases ([filedrop-centos](https://github.com/k8-proxy/GW-Releases/tree/filedrop-centos))
     - vmawre-scripts ([filedrop-centos](https://github.com/k8-proxy/vmware-scripts/tree/filedrop-centos))
     - k8-rebuild ([sow-_version](https://github.com/k8-proxy/k8-rebuild/tree/sow_version))
       - k8-rebuild-rest-api ([sow_version](https://github.com/k8-proxy/k8-rebuild-rest-api/tree/sow_version))
       - k8-rebuild-file-drop ([sow_version](https://github.com/k8-proxy/k8-rebuild-file-drop/tree/sow_version))

## ESXi-Deployment Workflow

* This workflow automatically deploys a given OVA on glasswall's ESXi server (defined in github secrets).
* Prerequisites (mandatory): 
  * Run the workflow from "ova_deployment" branch.
  * OVA's s3 download link.
  * The name of the VM created from the OVA on the ESXi server
  * choose either to run network configuration script on the VM after powering it on or not (true or false)
* Prerequisites (optional) needed if running network configuration option is set to be true:
  * IP address to be assigned to the VM after launch ex.(192.168.30.240/24) .
  * Gateway to be assigned to the VM after launch.
  * DNS server to be assigned to the VM after launch .
  * OVA username.
  * OVA password.
  * OVA base operating system (centos or ubuntu)
* Manually trigger the workflow from github **Actions**, and provide the above prerequisites.

![image](https://user-images.githubusercontent.com/58347752/111388167-7f6e3c00-86b7-11eb-863c-0fc8a1668cf4.png) ![image](https://user-images.githubusercontent.com/58347752/111388220-914fdf00-86b7-11eb-9c79-71e20c7c5e22.png)



