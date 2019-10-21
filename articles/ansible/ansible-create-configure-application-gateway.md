---
title: チュートリアル - Ansible を使用して Azure Application Gateway で Web トラフィックを管理する
description: Ansible を使用して Azure Application Gateway を作成および構成し、Web トラフィックを管理する方法について説明する
keywords: Ansible、Azure、DevOps、Bash、プレイブック、アプリケーションゲートウェイ、ロード バランサー、Web トラフィック
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1dd547fb59a41a90de18d595a392b64ef518023a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241883"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>チュートリアル:Ansible を使用して Azure Application Gateway で Web トラフィックを管理する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure アプリケーション ゲートウェイ](/azure/application-gateway/overview)は、アプリケーションに対するトラフィックを管理できる Web トラフィック ロードバランサーです。 従来のロード バランサーは、ソース IP アドレスとポートに基づいて、トラフィックを宛先 IP アドレスとポートにルーティングします。 Application Gateway では、URL を基にトラフィックのルーティング先を細かく制御できます。 たとえば、URL のパスが `images` である場合に、イメージ用に構成された特定のサーバー セット (プール) にトラフィックがルーティングされるように定義することができます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * ネットワークの設定
> * HTTPD イメージを使用して 2 つの Azure コンテナー インスタンスを作成する
> * サーバー プールで Azure コンテナー インスタンスと共に動作するアプリケーション ゲートウェイを作成する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

このセクションのプレイブック コードでは、Azure リソース グループを作成します。 リソース グループは、Azure リソースが構成される論理コンテナーです。  

次のプレイブックを `rg.yml` という名前で保存します。

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

プレイブックを実行する前に、次の注意事項を参照してください。

- リソース グループ名は `myResourceGroup` です。 この値はチュートリアル全体で使用されます。
- リソース グループが作成される場所は `eastus` です。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

このセクションのプレイブック コードでは、仮想ネットワークを作成してアプリケーション ゲートウェイが他のリソースと通信できるようにします。

次のプレイブックを `vnet_create.yml` という名前で保存します。

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

プレイブックを実行する前に、次の注意事項を参照してください。

* `vars` セクションには、ネットワーク リソースの作成に使用される値が含まれています。 
* これらの値は、ご使用の環境に合わせて変更する必要があります。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>サーバーを作成する

このセクションのプレイブック コードでは、HTTPD イメージを使用して 2 つの Azure コンテナー インスタンスを作成し、アプリケーション ゲートウェイの Web サーバーとして使用できるようにします。  

次のプレイブックを `aci_create.yml` という名前で保存します。

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

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

このセクションのプレイブック コードでは、`myAppGateway` という名前のアプリケーション ゲートウェイを作成します。  

次のプレイブックを `appgw_create.yml` という名前で保存します。

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

プレイブックを実行する前に、次の注意事項を参照してください。

* `gateway_ip_configurations` ブロックでは `appGatewayIP` が定義されます。 ゲートウェイの IP 構成には、サブネット参照が必要です。
* `backend_address_pools` ブロックでは `appGatewayBackendPool` が定義されます。 アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。
* `backend_http_settings_collection` ブロックでは `appGatewayBackendHttpSettings` が定義されます。 ここでは、通信にポート 80 と HTTP プロトコルを使用するように指定します。
* `backend_http_settings_collection` ブロックでは `appGatewayHttpListener` が定義されます。 これは、appGatewayBackendPool に関連付けられている既定のリスナーです。
* `frontend_ip_configurations` ブロックでは `appGatewayFrontendIP` が定義されます。 これは、myAGPublicIPAddress を appGatewayHttpListener に割り当てます。
* `request_routing_rules` ブロックでは `rule1` が定義されます。 これは、appGatewayHttpListener に関連付けられている既定のルーティング規則です。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook appgw_create.yml
```

アプリケーション ゲートウェイの作成には数分かかる場合があります。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. 「[リソース グループの作成](#create-a-resource-group)」セクションで場所を指定します。 その値を書き留めておいてください。

1. 「[ネットワーク リソースを作成する](#create-network-resources)」セクションでドメインを指定します。 その値を書き留めておいてください。

1. テスト URL 用に、次のパターンをこの場所とドメインで置き換えます。`http://<domain>.<location>.cloudapp.azure.com`

1. テスト URL にアクセスします。

1. 次のページが表示されたら、アプリケーション ゲートウェイは期待どおりに動作しています。

    ![動作中のアプリケーション ゲートウェイの成功したテスト](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

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

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure 上の Ansible](/azure/ansible/)