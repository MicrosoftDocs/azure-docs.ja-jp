---
title: Azure Kubernetes Service (AKS) で Application Gateway イングレス コントローラーを作成する
description: Azure Kubernetes Service を使用してイングレス コントローラーとしての Application Gateway を備えた Kubernetes クラスターを作成する方法を示すチュートリアル
services: terraform
ms.service: azure
keywords: terraform, devops, 仮想マシン, azure, kubernetes, イングレス, アプリケーション ゲートウェイ
author: tomarcher
manager: gwallace
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: b156169e7202319366e337cc7081e02f5de3acad
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244799"
---
# <a name="create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Azure Kubernetes Service で Application Gateway イングレス コントローラーを作成する

[Azure Kubernetes Service (AKS)](/azure/aks/) では、ホストされている Kubernetes 環境を管理します。 AKS では、コンテナー オーケストレーションの専門知識がなくても、コンテナー化されたアプリケーションを迅速かつ簡単にデプロイして管理できます。 また、AKS を使用すると、運用タスクや保守タスクのためにアプリケーションをオフラインにする負担もなくなります。 AKS を使用すると、リソースのプロビジョニング、アップグレード、スケーリングなどのタスクをオンデマンドで実行できます。

イングレス コントローラーは、Kubernetes サービスのさまざまな機能を提供します。 これらの機能には、リバース プロキシ、構成可能なトラフィック ルーティング、TLS などがあります。 個別の Kubernetes サービスのイングレス ルールを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールによって、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。 この機能はすべて [Azure Application Gateway](/azure/Application-Gateway/) で提供され、Azure 上の Kubernetes に最適なイングレス コントローラーになります。 

このチュートリアルでは、次のタスクを実施する方法について説明します。

> [!div class="checklist"]
> * AKS を使用してイングレス コントローラーとしての Application Gateway を備えた [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) クラスターを作成する。
> * HCL (HashiCorp 言語) を使用して Kubernetes クラスターを定義する。
> * Terraform を使用して Application Gateway リソースを作成する。
> * AKS と Terraform を使用して Kubernetes クラスターを作成する。
> * kubectl ツールを使用して Kubernetes クラスターの可用性をテストする。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

- **Terraform の構成**:[Terraform および Azure へのアクセスの構成](/azure/virtual-machines/linux/terraform-install-configure)に関する記事の指示に従ってください

- **Azure サービス プリンシパル**:「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)」の「**サービス プリンシパルを作成する**」セクションの指示に従ってください。 appId、displayName、および password の値を書き留めます。

- **サービス プリンシパル オブジェクト ID を取得する**:Cloud Shell で次のコマンドを実行します。`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

最初の手順では、演習のために、Terraform 構成ファイルを保持するディレクトリを作成します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. [Azure Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. ディレクトリを `clouddrive` ディレクトリに変更します。

    ```bash
    cd clouddrive
    ```

1. `terraform-aks-k8s` という名前のディレクトリを作成します。

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. 新しいディレクトリに移動します。

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Azure プロバイダーを宣言する

Azure プロバイダーを宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`main.tf` という名前のファイルを作成します。

    ```bash
    code main.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="define-input-variables"></a>入力変数を定義する

このデプロイに必要なすべての変数をリストした Terraform 構成ファイルを作成します。

1. Cloud Shell で、`variables.tf` という名前のファイルを作成します。

    ```bash
    code variables.tf
    ```

1. 以下のコードをエディターに貼り付けます。
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="define-the-resources"></a>リソースを定義する 
すべてのリソースを作成する Terraform 構成ファイルを作成します。 

1. Cloud Shell で、`resources.tf` という名前のファイルを作成します。

    ```bash
    code resources.tf
    ```

1. 次のコード ブロックを貼り付けて、計算された変数を再利用するローカル ブロックを作成します。

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. 次のコード ブロックを貼り付けて、リソース グループと新しいユーザー ID のデータ ソースを作成します。

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"

      name = "identity1"

      tags = "${var.tags}"
    }
    ```

1. 次のコード ブロックを貼り付けて、基本となるネットワーク リソースを作成します。

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]

      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }

      tags = "${var.tags}"
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"

      tags = "${var.tags}"
    }
    ```

1. 次のコード ブロックを貼り付けて、Application Gateway リソースを作成します。

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"

      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }

      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }

      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }

      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }

      tags = "${var.tags}"

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. 次のコード ブロックを貼り付けて、ロールの割り当てを作成します。

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. 次のコード ブロックを貼り付けて、Kubernetes クラスターを作成します。

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"

      resource_group_name = "${data.azurerm_resource_group.rg.name}"

      linux_profile {
        admin_username = "${var.vm_user_name}"

        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }

      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }

    ```

1. ファイルを保存し、エディターを終了します。

このセクションに示されているコードでは、クラスター、場所、および resource_group_name を設定します。 クラスターへのアクセスに使用される完全修飾ドメイン名 (FQDN) の一部を形成する `dns_prefix` 値が設定されます。

`linux_profile` レコードを使用すると、SSH を使用してワーカー ノードにサインインできる設定を構成できます。

AKS では、ワーカー ノードのみについて課金されます。 `agent_pool_profile` レコードでは、これらのワーカー ノードの詳細を構成します。 `agent_pool_profile record` には、作成するワーカー ノードの数とワーカー ノードの種類が含まれます。 将来、クラスターをスケールアップまたはスケールダウンする必要がある場合は、このレコードの `count` 値を変更します。

## <a name="create-a-terraform-output-file"></a>Terraform 出力ファイルを作成する

[Terraform 出力](https://www.terraform.io/docs/configuration/outputs.html)によって、Terraform がプランを適用するときユーザーに対して強調表示される値を定義できます。これは、`terraform output` コマンドを使用してクエリできます。 このセクションでは、[kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) を使用したクラスターへのアクセスを許可する出力ファイルを作成します。

1. Cloud Shell で、`output.tf` という名前のファイルを作成します。

    ```bash
    code output.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }

    output "identity_resource_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.id}"
    }

    output "identity_client_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.client_id}"
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="configure-azure-storage-to-store-terraform-state"></a>Terraform 状態を保存する Azure Storage を構成する

Terraform は `terraform.tfstate` ファイルを介して状態をローカルで追跡します。 このパターンは 1 名の環境に適しています。 ただし、より実用的な複数名の環境では、[Azure ストレージ](/azure/storage/)を利用してサーバー上で状態を追跡する必要があります。 このセクションでは、必要なストレージ アカウント情報を取得し、ストレージ コンテナーを作成する方法について説明します。 そのコンテナーに Terraform の状態情報が格納されます。

1. Azure Portal の左側のメニューで **[すべてのサービス]** を選択します。

1. **[ストレージ アカウント]** を選択します。

1. **[ストレージ アカウント]** タブで、Terraform が状態を格納するストレージ アカウントの名前を選択します。 たとえば、最初に Cloud Shell を開いたときに作成したストレージ アカウントを使用できます。  通常、Cloud Shell によって作成されたストレージ アカウント名は、`cs` の後に数字と文字のランダムな文字列が続きます。 

    後で必要になるため、選択したストレージ アカウントをメモしておきます。

1. [ストレージ アカウント] タブで **[アクセス キー]** を選択します。

    ![ストレージ アカウント メニュー](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. **key1** の **[キー]** 値をメモします。 (キーの右側にあるアイコンを選択すると、値がクリップボードにコピーされます。)

    ![ストレージ アカウントのアクセス キー](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Cloud Shell で、Azure ストレージ アカウントにコンテナーを作成します (&lt;YourAzureStorageAccountName> および &lt;YourAzureStorageAccountAccessKey> プレースホルダーをご使用の Azure ストレージ アカウントの適切な値で置き換えます)。

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes クラスターを作成する
このセクションでは、`terraform init` コマンドを使用して、前のセクションで作成した構成ファイルに定義されているリソースを作成する方法を学びます。

1. Cloud Shell で、Terraform を初期化します (&lt;YourAzureStorageAccountName> および &lt;YourAzureStorageAccountAccessKey> プレースホルダーをご使用の Azure ストレージ アカウントの適切な値で置き換えます)。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    `terraform init` コマンドによって、バックエンドおよびプロバイダー プラグインの初期化が成功したことが表示されます。

    !["terraform init" の結果例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Cloud Shell で、`main.tf` という名前のファイルを作成します。

    ```bash
    code terraform.tfvars
    ```

1. 以前に作成した次の変数をエディターに貼り付けます。

    ```hcl
    resource_group_name = <Name of the Resource Group already created>

    location = <Location of the Resource Group>
      
    aks_service_principal_app_id = <Service Principal AppId>
      
    aks_service_principal_client_secret = <Service Principal Client Secret>
      
    aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

1. `terraform plan` コマンドを実行して、インフラストラクチャ要素を定義する Terraform プランを作成します。 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` コマンドによって、`terraform apply` コマンドを実行したときに作成されるリソースが表示されます。

    !["terraform plan" の結果例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. `terraform apply` コマンドを実行して、プランを適用し、Kubernetes クラスターを作成します。 Kubernetes クラスターを作成するプロセスに数分間かかり、Cloud Shell セッションがタイムアウトになる場合があります。Cloud Shell セッションがタイムアウトした場合は、「Cloud Shell タイムアウトから復旧する」セクションの手順に従ってチュートリアルを完了できます。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` コマンドによって、構成ファイルに定義されたリソースの作成結果が表示されます。

    !["terraform apply" の結果例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Azure portal 上で、左側のメニューの **[リソース グループ]** を選択すると、選択されたリソース グループ内の新しい Kubernetese クラスターに対して作成されたリソースが表示されます。

    ![Cloud Shell のプロンプト](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Cloud Shell タイムアウトから復旧する

Cloud Shell セッションがタイムアウトした場合は、次の手順を使用して復旧できます。

1. Cloud Shell セッションを開始します。

1. Terraform 構成ファイルを含むディレクトリに移動します。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 次のコマンドを実行します。

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Kubernetes クラスターをテストする
Kubernetes ツールを使用して、新しく作成したクラスターを確認できます。

1. Terraform 状態から Kubernetes 構成を取得し、kubectl が読み取れるファイルに格納します。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. kubectl が正しい構成を選択できるように環境変数を設定します。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. クラスターの正常性を確認します。

    ```bash
    kubectl get nodes
    ```

    ワーカー ノードの詳細を確認してください。次の図のように、すべてのステータスが **Ready** になっている必要があります。

    ![kubectl ツールを使用すると Kubernetes クラスターの正常性を確認できる](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Azure AD ポッド ID をインストールする

Azure Active Directory ポッド ID は、[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) へのトークンベースのアクセスを提供します。

[Azure AD ポッド ID](https://github.com/Azure/aad-pod-identity) によって、次のコンポーネントが Kubernetes クラスターに追加されます。

  - Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`、`AzureAssignedIdentity`、`AzureIdentityBinding`
  - [Managed Identity Controller (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) コンポーネント
  - [Node Managed Identity (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) コンポーネント

RBAC が**有効**な場合は、次のコマンドを実行して Azure AD ポッド ID をクラスターにインストールします。

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

RBAC が**無効**な場合は、次のコマンドを実行して Azure AD ポッド ID をクラスターにインストールします。

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

## <a name="install-helm"></a>Helm のインストール

このセクションのコードでは、[Helm](/azure/aks/kubernetes-helm) (Kubernetes パッケージ マネージャー) を使用して `application-gateway-kubernetes-ingress` パッケージをインストールします。

1. RBAC が**有効**な場合は、次の一連のコマンドを実行して、Helm のインストールと構成を行います。

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. RBAC が**無効**な場合は、次のコマンドを実行して、Helm のインストールと構成を行います。

    ```bash
    helm init
    ```

1. AGIC Helm リポジトリを追加します。

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>イングレス コントローラーの Helm Chart をインストールする

1. `helm-config.yaml` をダウンロードして AGIC を構成します。

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. `helm-config.yaml` を編集し、`appgw` セクションと `armAuth` セクションに適した値を入力します。

    ```bash
    nano helm-config.yaml
    ```

    次のように値を記述します。

    - `verbosityLevel`:AGIC ログ インフラストラクチャの詳細レベルを設定します。 使用できる値については、「[ログ レベル](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)」を参照してください。
    - `appgw.subscriptionId`:App Gateway の Azure サブスクリプション ID。 例: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`:App Gateway が作成された Azure リソース グループの名前。 
    - `appgw.name`:Application Gateway の名前。 例: `applicationgateway1`.
    - `appgw.shared`:このブール型のフラグは、既定で `false` に設定する必要があります。 [Shared App Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway) が必要な場合は、`true` に設定します。
    - `kubernetes.watchNamespace`:AGIC で監視する名前空間を指定します。 名前空間には、単一の文字列値、または名前空間のコンマ区切り一覧を指定できます。
    - `armAuth.type`:`aadPodIdentity` または `servicePrincipal` のいずれかの値です。
    - `armAuth.identityResourceID`:マネージド ID のリソース ID。
    - `armAuth.identityClientId`:ID のクライアント ID。
    - `armAuth.secretJSON`:サービス プリンシパル シークレットの種類を選択した場合にのみ必要です (`armAuth.type` が `servicePrincipal` に設定されている場合)。

    重要事項:
    - `identityResourceID` の値は terraform スクリプトで作成され、`echo "$(terraform output identity_client_id)"` を実行して確認できます。
    - `identityClientID` の値は terraform スクリプトで作成され、`echo "$(terraform output identity_resource_id)"` を実行して確認できます。
    - `<resource-group>` の値は App Gateway のリソース グループです。
    - `<identity-name>` の値は作成された ID の名前です。
    - 特定のサブスクリプションのすべての ID は、`az identity list` を使用して一覧表示できます。

1. Application Gateway イングレス コントローラー パッケージをインストールします。

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>サンプル アプリをインストールする

App Gateway、AKS、AGIC をインストールしたら、[Azure Cloud Shell](https://shell.azure.com/) を使用してサンプル アプリをインストールできます。

1. curl コマンドを使用して YAML ファイルをダウンロードします。

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. YAML ファイルを適用します。

    ```bash
    kubectl apply -f apsnetapp.yaml
    ```

## <a name="next-steps"></a>次の手順

この記事では、Terraform と AKS を使用して Kubernetes クラスターを作成する方法を学習しました。 Azure 上の Terraform について詳しくは、次のリソースもご覧ください。
 
 > [!div class="nextstepaction"] 
  > [Application Gateway イングレス コントローラー](https://azure.github.io/application-gateway-kubernetes-ingress/)