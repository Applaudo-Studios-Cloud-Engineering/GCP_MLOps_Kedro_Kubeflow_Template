# Kubeflow in Google Kubernetes Engine (GKE)

## Overview

This is your new Kedro project, which was generated using `Kedro 0.18.2`.

Take a look at the [Kedro documentation](https://kedro.readthedocs.io) to get started.

## How to install Kubeflow Pipelines in GKE

### Guide to install Kubeflow Pipelines on GKE using Cloud Shell on GCP

1. Set up a Google Cloud project and activate Cloud Shell in the top right bar.

![1](https://user-images.githubusercontent.com/93058462/190222666-72a099e2-8195-4d37-828a-3ce8fbc6e098.png)

2. Enable de APIs running the following command in Cloud Shell.

        $ gcloud services enable \
            serviceusage.googleapis.com \
            compute.googleapis.com \
            container.googleapis.com \
            iam.googleapis.com \
            servicemanagement.googleapis.com \
            cloudresourcemanager.googleapis.com \
            ml.googleapis.com \
            iap.googleapis.com

![2-kubeflow](https://user-images.githubusercontent.com/93058462/190232238-42296060-c72d-4150-be58-1b760c1d0262.png)

Note: If you see the Authorize Cloud Shell prompt, please select *Authorize* option.

3. Create a Kubernetes cluster on GCP running the following commands in Cloud Shell. Remember customize the parameters on your needs.

        $ export CLUSTER_NAME="test-cluster"

        $ export ZONE="us-central1-a"

        $ export MACHINE_TYPE="e2-standard-2" 

        $ export SCOPES="cloud-platform"                                    # This scope grants all GCP permissions to the cluster

        $ gcloud container clusters create $CLUSTER_NAME \
            --zone $ZONE \
            --machine-type $MACHINE_TYPE \
            --scopes $SCOPES

![3](https://user-images.githubusercontent.com/93058462/190243579-0a20370b-596f-438f-9ab3-8da13368bec8.png)

4. Install kubectl to talk to the cluster. In Cloud Shell, run the following commands to install kubectl on Linux.

- Download the latest release.

        $ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

- Download the kubectl checksum file.

        $ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

- Validate the kubectl binary against the checksum file.

        $ echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check         # If valid, the output is OK

- Install kubectl.

        $ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

![4-kube](https://user-images.githubusercontent.com/93058462/190247172-ef7b8015-6674-4b89-bbce-ebae5e9813bd.png)

5. Deploy Kubeflow Pipelines running the following commands (version 0.4.0 and higher) in Cloud Shell.

        $ export PIPELINE_VERSION=1.8.5

        $ kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"

        $ kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io

        $ kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/dev?ref=$PIPELINE_VERSION"

![5-kube](https://user-images.githubusercontent.com/93058462/190250726-13de6065-73a3-4870-9746-594256466b7b.png)

6. Get the public URL for the Kubeflow Pipelines UI with the following command.

        $ kubectl describe configmap inverse-proxy-config -n kubeflow | grep googleusercontent.com

![6-kube](https://user-images.githubusercontent.com/93058462/190251320-39d6cb6c-34a7-4cf1-b367-ccdd1b84ef1e.png)

7. Copy the before output in your browser to see Kubeflow Pipelines UI.

![7-kube](https://user-images.githubusercontent.com/93058462/190254375-e82692ee-4a98-4bf5-be0e-f0e62f2d8b79.png)

> **Summary**
> At the end of this section, the Kubeflow Pipeline UI was exposed at a public URL.

### Guide to install Kubeflow Pipelines in GKE accessing the cluster from local

1. Set up a Google Cloud project and enable de APIs using Cloud Shell on GCP or directly from the console.

2. Create a Kubernetes cluster on GCP. For this section, it will be shown how to create the cluster using the console.

- Write kubernetes engine in the top search bar and select this resource.

![10-kubeflow](https://user-images.githubusercontent.com/93058462/190281969-520d549f-09f1-4313-9932-f6632cd9d123.png)

- Select *create* option in the top.

![11-kubeflow](https://user-images.githubusercontent.com/93058462/190298987-1afdbc96-401a-4ab5-b6af-3f2ea0869300.png)

- In the *Create cluster* window, select *configure* in the *standard* option.

- Create a zonal cluster with a representative name in the *Cluster basics* section. For the configuration of the nodes, select the type of machine that suits your needs and finally, in the *Node security* section, choose *allow full access to all Cloud APIs*.

![13-kubeflow](https://user-images.githubusercontent.com/93058462/190302357-be65c7b0-94a9-4b4e-9efe-037915a172a4.png)

- Once the cluster is created, enter in the cluster name to see the configuration details. In the top bar, select the *connect* option.

![14-kubeflow](https://user-images.githubusercontent.com/93058462/190304863-e5849df2-3936-4724-a4f3-35352b2b2837.png)

- Copy the command to connect to the cluster.

![15-kubeflow](https://user-images.githubusercontent.com/93058462/190305348-76063494-d918-48da-83e9-dccc465872c1.png)

3. From local, in terminal paste the above command. It is important to have kubectl previously installed (see item 4 of the previous section).

![16-kubeflow](https://user-images.githubusercontent.com/93058462/190305926-df0afafe-72e3-40c3-ac2e-0ec51718e5f2.png)

4. Deploy Kubeflow Pipelines running the following commands (version 0.4.0 and higher) in your terminal.

        $ export PIPELINE_VERSION=1.8.5

        $ kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"

        $ kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io

        $ kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/dev?ref=$PIPELINE_VERSION"

5. To see all namespaces of the cluster, run the following command. If the Kubeflow Pipelines installation was successful, you will see a namespace named *kubeflow*.

        $ kubectl get namespaces

![17-kubeflow](https://user-images.githubusercontent.com/93058462/190310988-0085ed02-3a5e-4758-9cc2-0756e76e9354.png)

6. To see the services of the *kubeflow* namespace, run the following command in your terminal.

        $ kubectl get svc -n kubeflow

![18-kubeflow](https://user-images.githubusercontent.com/93058462/190311891-45713004-9094-4904-82b5-bd19fc2b8594.png)

7. To expose the Kubeflow Pipelines UI service from localhost, run the following command.

        $ kubectl port-forward svc/ml-pipeline-ui 8000:80 -n kubeflow       # Choose a different host port (8000)

![19-kubeflow](https://user-images.githubusercontent.com/93058462/190313112-807732f6-2369-4b63-bc04-b441094a091d.png)

8. Copy localhost:port in your browser to see Kubeflow Pipelines UI.

![20-kubeflow](https://user-images.githubusercontent.com/93058462/190313889-7c169796-ea5e-4092-8f3a-2184ce01b41b.png)

> **Summary**
> After numeral 8, the Kubeflow Pipeline UI was exposed in localhost.

9. However, it is necessary to expose Kubeflow Pipeline UI in a public URL to use the Kedro Kubeflow plugin. For this reason, reserve a regional IP on GCP.

- Write ip adress in the top search bar and select this resource.

![21-dominio](https://user-images.githubusercontent.com/93058462/190503489-24bf5c59-288d-4505-8246-eade33b6471c.png)

- Select *reserve external static address* option in the top.

![22-dominio](https://user-images.githubusercontent.com/93058462/190504482-79d42a82-bd7f-48eb-8f09-ba37364b5734.png)

- Create a regional IPv4 with a representative name. Choose the region according to the cluster zone.

![23-dominio](https://user-images.githubusercontent.com/93058462/190505387-8a6ca151-01c9-46a4-b610-8098865d6c47.png)

- The IP address will be used for the DNS zone.

10. Create a Cloud DNS zone on GCP.

- Write dns in the top search bar and select this resource.

![24-dominio](https://user-images.githubusercontent.com/93058462/190509056-cf37c47a-84c7-488a-97bd-4cd48d9f0d31.png)

- Select *create zone* option in the top.

![25-dominio](https://user-images.githubusercontent.com/93058462/190509839-17a6c7a2-f470-403f-91d8-3df58a891381.png)

- Create a public DNS zone with a representative name. Write the domain in *DNS name* that you will use to expose Kubeflow Pipeline UI.

![26-dominio](https://user-images.githubusercontent.com/93058462/190510653-9589e63a-6abf-492b-8cbe-23bc261516fb.png)

Note: For this experiment, we use *crdemo.tk* free domain.

- Once the DNS zone is created, enter in the zone name to see the configuration details. In the top bar, select the *add record set* option.

![27-dominio](https://user-images.githubusercontent.com/93058462/190511728-119a7367-c317-4f17-959f-1998efdb4d61.png)

- Add the reserved regional IP address in the *IPv4 Address* field and the rest with the default values.

![28-dominio](https://user-images.githubusercontent.com/93058462/190515307-db069f19-f760-4dd3-bf0e-d24fa5700c73.png)

- Once the type A recordset is added, select *registrar setup* and copy the records to configure with your registrar.

![29-dominio](https://user-images.githubusercontent.com/93058462/190516235-895f7d0e-d3fb-449b-840a-ed298022aa44.png)

11. In the cluster, install the ingress NGINX controller using Helm and set the reserved IP address.

- Add repo information. 

        $ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

        $ helm repo update

- Create a namespace in the cluster for the ingress NGINX controller.

        $ kubectl create namespace ingress-nginx
        Usage: kubectl create namespace [NAMESPACE_NAME]

- Install the ingress NGINX controller helm chart.

        $ helm install ingress-nginx ingress-nginx/ingress-nginx --set controller.service.loadBalancerIP=35.232.48.115 -n ingress-nginx
        Usage: helm install [RELEASE_NAME] ingress-nginx/ingress-nginx -n [NAMESPACE_NAME]

![30-dominio](https://user-images.githubusercontent.com/93058462/190522244-9e250641-6a76-4994-9328-e0681005ac91.png)

12. In the cluster, install the ingress NGINX controller using Helm and set the reserved IP address.

- Install the cert-manager CustomResourceDefinition resources.

        $ kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.1/cert-manager.crds.yaml

- Add the Jetstack Helm repository.

        $ helm repo add jetstack https://charts.jetstack.io

- Create a namespace in the cluster for the cert-manager.

        $ kubectl create namespace cert-manager
        Usage: kubectl create namespace [NAMESPACE_NAME]

- Install the cert-manager helm chart

        $ helm install cert-manager --namespace cert-manager --version v1.8.1 jetstack/cert-manager
        Usage: helm install [RELEASE_NAME] --namespace [NAMESPACE_NAME] --version v1.8.1 jetstack/cert-manager

- Create a ClusterIssuer. This is a Kubernetes resource that represent certificate authorities that are able to generate signed certificates by honoring certificate signing requests. After, apply the ClusterIssuer file running the following command.

        $ kubectl apply -f cluster-issuer.yaml -n cert-manager
        Usage: kubectl apply -f [FILE_NAME] -n [NAMESPACE_NAME]

- Check the status of the ClusterIssuer by running the command.

        $ kubectl get clusterissuer -n cert-manager
        Usage: kubectl get clusterissuer -n [NAMESPACE_NAME]

![31-dominio](https://user-images.githubusercontent.com/93058462/190534501-23e26faf-8f92-4597-abb6-ffb993ed62d2.png)

13. Finally, create an ingress to expose the Kubeflow Pipelines UI on your domain. After, apply the ingress file in the kubeflow namespace by running the following command.

        $ kubectl apply -f ingress-kubeflow.yaml -n kubeflow
        Usage: kubectl apply -f [FILE_NAME] -n [NAMESPACE_NAME]

- Check the status of the certificate by running the command.

        $ kubectl get certificate -n kubeflow
        Usage: kubectl get certificate -n [NAMESPACE_NAME]

![32-dominio](https://user-images.githubusercontent.com/93058462/190548754-c4d388b5-2d78-49ea-8743-133684161894.png)

- Check the events of the certificate to know if the certificate was applied successfully.

        $ kubectl describe certificate kubeflow-secret -n kubeflow
        Usage: kubectl describe certificate [CERTIFICATE_NAME] -n [NAMESPACE_NAME]

![33-dominio](https://user-images.githubusercontent.com/93058462/190549457-9bc73c8b-ff9b-4b2e-b877-06503fe11775.png)

14. Write the domain in browser to see Kubeflow Pipelines UI.

![34-kubeflow](https://user-images.githubusercontent.com/93058462/190549973-6f3a0ff1-d943-4bb2-85c1-6cb7a9fd7168.png)

> **Summary**
> At the end of this section, the Kubeflow Pipeline UI was exposed in a secure domain.

## Modifications to ML workflow

1. Install the Kedro Kubeflow plugin usinf the following command.

        $ pip install --upgrade kedro-kubeflow

2. In the root of the Kedro project, use the *init* command with the URL argument to generate a kubeflow file (kubeflow.yaml) in conf/base.

        $ kedro kubeflow init https://crdemo.tk/#/pipelines
        Usage: kedro kubeflow init [URL]

## Documentation 

| Tool | LINK |
| ------ | ------ |
| Ingress NGINX controller | https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx |
| Cert-manager | https://artifacthub.io/packages/helm/cert-manager/cert-manager |
