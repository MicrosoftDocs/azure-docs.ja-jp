---
title: Azure REST API を使用して Linux 仮想マシンを作成する | Microsoft Docs
description: マネージド ディスクと SSH 認証を使用する Linux 仮想マシンを Azure REST API を使用して Azure 内に作成する方法を説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: a7f624bc85d35048a8f9afa0f527ae592a24fbf1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667946"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>SSH 認証を使用する Linux 仮想マシンを REST API で作成する

Azure 内の Linux 仮想マシン (VM) は、ディスクやネットワーク インターフェイスなどのさまざまなリソースで構成され、場所、サイズおよびオペレーティング システムのイメージや認証の設定などのパラメーターが定義されます。

Azure portal、Azure CLI 2.0、多くの Azure SDK、Azure Resource Manager テンプレートおよび Ansible や Terraform などの多くのサードパーティ製ツールを使用して、Linux VM を作成することができます。 これらのすべてのツールでは、最終的に REST API を使用して Lunux VM が作成されます。

この記事では、REST API を使用して、マネージド ディスクと SSH 認証で Ubuntu 18.04-LTS を実行する Linux VM を作成する方法について説明します。

## <a name="before-you-start"></a>開始する前に

要求を作成して送信する前に、次のものが必要になります。

* ご自分のサブスクリプションの `{subscription-id}`
  * 複数のサブスクリプションをお持ちの場合は、[複数のサブスクリプションの操作](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)に関するページを参照してください。
* 事前に作成した `{resourceGroupName}`
* 同じリソース グループ内の[仮想ネットワーク インターフェイス](../../virtual-network/virtual-network-network-interface.md)
* SSH キー ペア (お持ちでない場合は、[新しいものを作成](mac-create-ssh-keys.md)できます)

## <a name="request-basics"></a>要求の基本

仮想マシンを作成または更新するには、次の *PUT* 操作を使用します。

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

`{subscription-id}` および `{resourceGroupName}` パラメーターに加え、`{vmName}` を指定する必要があります (`api-version` は省略可能ですが、この記事は `api-version=2017-12-01` でテストされました)

次のヘッダーは必須です｡

| 要求ヘッダー   | 説明 |
|------------------|-----------------|
| *Content-Type:*  | 必須。 `application/json` を設定します。 |
| *Authorization:* | 必須。 有効な `Bearer` [ アクセス トークン](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します｡ |

REST API 要求の操作の概要については、「[Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse)」(REST API 要求/応答のコンポーネント) を参照してください。

## <a name="create-the-request-body"></a>要求本文を作成する

要求本文を作成するには、以下の一般的な定義が使用されます。

| Name                       | 必須 | Type                                                                                | 説明  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | string                                                                              | リソースの場所。 |
| 名前                       |          | string                                                                              | 仮想マシンの名前。 |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 仮想マシンのハードウェア設定を指定します。 |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 仮想マシンのストレージ設定を指定します。 |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 仮想マシンのオペレーティング システム設定を指定します。 |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 仮想マシンのネットワーク インターフェイスを指定します。 |

要求本文の例を以下に示します。 `{computerName}` および `{name}` パラメーターの VM 名、`networkInterfaces` の下の作成したネットワーク インターフェイスの名前、`adminUsername` と `path` のユーザー名、`keyData` の (`~/.ssh/id_rsa.pub` などに配置されている) SSH キーペアの *public* 部分が指定されていることを確認してください。 変更できるその他のパラメーターは `location` と `vmSize` です。  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

要求本文で使用可能な定義の完全な一覧については、[仮想マシンの作成または更新要求の本文の定義](/rest/api/compute/virtualmachines/createorupdate#definitions)に関するセクションを参照してください。

## <a name="sending-the-request"></a>要求の送信

この HTTP 要求を送信するために任意のクライアントを使用することができます。 また、 **[試してみる]** ボタンをクリックして、[ブラウザー内ツール](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate)を使用することもできます。

### <a name="responses"></a>Responses

仮想マシンの作成または更新操作には、2 種類の成功応答があります。

| Name        | Type                                                                              | 説明 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 作成されました     |

MV を作成する要求本文の例の圧縮された *201 Created* 応答は、*vmId* が割り当てられ、*provisioningState* が *Creating* であることを示します。

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

REST API の応答の詳細については、「[Process the response message](/rest/api/azure/#process-the-response-message)」(応答メッセージを処理する) を参照してください。

## <a name="next-steps"></a>次の手順

Azure REST API や他の管理ツール (Azure CLI や Azure PowerShell など) の詳細については、以下を参照してください。

- [Azure Compute プロバイダー REST API](/rest/api/compute/)
- [Azure Rest API の開始](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell モジュール](/powershell/azure/overview)
