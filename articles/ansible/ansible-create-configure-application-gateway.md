---
title: Ansible を使用して Azure Application Gateway で Web トラフィックを管理する
description: Ansible を使用して Azure Application Gateway を作成および構成し、Web トラフィックを管理する方法について説明する
ms.service: azure
keywords: Ansible、Azure、DevOps、Bash、プレイブック、アプリケーションゲートウェイ、ロード バランサー、Web トラフィック
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 83f21573af7ec523acc376c4b3364cdcfb47f96f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792142"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Ansible を使用して Azure Application Gateway で Web トラフィックを管理する

[Azure アプリケーション ゲートウェイ](https://docs.microsoft.com/azure/application-gateway/)は、アプリケーションに対するトラフィックを管理できる Web トラフィック ロードバランサーです。

Ansible は、環境内のリソースのデプロイと構成を自動化するために役立ちます。 この記事では、Ansible を使用してアプリケーション ゲートウェイを作成する方法を示します。 ゲートウェイを使用して、Azure コンテナー インスタンスで実行されている 2 つの Web サーバーへのトラフィックを管理する方法も説明します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * HTTPD イメージを使用して 2 つの Azure コンテナー インスタンスを作成する
> * サーバー プールで Azure コンテナー インスタンスと共に動作するアプリケーション ゲートウェイを作成する

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。 

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次の例では、**myResourceGroup** という名前のリソース グループを **eastus** に作成します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

このプレイブックを *rg.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

まず、アプリケーション ゲートウェイが他のリソースと通信できるように、仮想ネットワークを作成します。

次の例では、**myVNet** という名前の仮想ネットワーク、**myAGSubnet** という名前のサブネット、**myAGPublicIPAddress** という名前のパブリック IP アドレス、および **mydomain** という名前のドメインを作成します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

このプレイブックを *vnet_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>サーバーを作成する

次の例では、HTTPD イメージを使用して 2 つの Azure コンテナー インスタンスを作成し、アプリケーション ゲートウェイの Web サーバーとして使用する方法を示しています。  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

このプレイブックを *aci_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

次の例では、バックエンド、フロントエンド、および HTTP を構成した、**myAppGateway** という名前アプリケーション ゲートウェイを作成します。  

* **appGatewayIP** は、**gateway_ip_configurations** ブロックで定義されています。 ゲートウェイの IP 構成には、サブネット参照が必要です。
* **appGatewayBackendPool** は、**backend_address_pools** ブロックで定義されています。 アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。
* **appGatewayBackendHttpSettings** は、**backend_http_settings_collection** ブロックで定義されています。 これは、通信にポート 80 と HTTP プロトコルを使用することを指定しています。
* **appGatewayHttpListener** は、**backend_http_settings_collection** ブロックで定義されています。 これは、appGatewayBackendPool に関連付けられている既定のリスナーです。
* **appGatewayFrontendIP** は、**frontend_ip_configurations** ブロックで定義されています。 これは、myAGPublicIPAddress を appGatewayHttpListener に割り当てます。
* **rule1** は、**request_routing_rules** ブロックで定義されています。 これは、appGatewayHttpListener に関連付けられている既定のルーティング規則です。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

このプレイブックを *appgw_create.yml* として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook appgw_create.yml
```

アプリケーション ゲートウェイの作成には数分かかる場合があります。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

ネットワーク リソースのサンプル プレイブックで、**eastus** にドメイン **mydomain** を作成しました。 ブラウザーで `http://mydomain.eastus.cloudapp.azure.com` にアクセスします。 次のページが表示されたら、アプリケーション ゲートウェイは期待どおりに動作しています。

![動作中のアプリケーション ゲートウェイの成功したテスト](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これらのリソースが不要な場合は、次のコードを実行することでこれらを削除できます。 これは **myResourceGroup** という名前のリソース グループを削除します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

このプレイブックを *rg_delete*.yml として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)
