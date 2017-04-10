---
title: "ネットワーク リソース プロバイダーの概要 | Microsoft Docs"
description: "Azure リソース マネージャーのネットワーク リソース プロバイダーについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 27df1166a23e3ed89fdc86f861353c80a4a467ad
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c


---
# <a name="network-resource-provider"></a>ネットワーク リソース プロバイダー
今日のビジネスの成功を支えるニーズとは、大規模なネットワーク対応アプリケーションを俊敏、柔軟、安全、かつ反復可能な方法でビルドおよび管理する能力です。 Azure Resource Manager では、リソース グループ内の&1; つのリソース コレクションとして、このようなアプリケーションを作成することができます。 このようなリソースは、Resource Manager の下でさまざまなリソース プロバイダーを介して管理されています。

Azure リソース マネージャーは、さまざまなリソース プロバイダーに依存して、リソースへのアクセスを提供しています。 3 つの主なリソース プロバイダーは、Network、Storage および Compute です。 このドキュメントは、次などのネットワーク リソース プロバイダーの特徴と利点について説明します。

* **メタデータ** – タグを使用してリソースに情報を追加できます。 これらのタグは、リソース グループとサブスクリプションのリソース使用率を追跡するために使用されます。
* **ネットワークの制御の拡大** – ネットワーク リソースは疎結合され、より細かく制御することができます。 つまり、ネットワーク リソースを管理する柔軟性が高まります。
* **構成の高速化** – ネットワーク リソースは疎結合されているため、ネットワーク リソースの作成と調整を並行して実行できます。 これにより、構成にかかる時間が大幅に減少しました。
* **ロール ベースのアクセス制御** – RBAC は、特定のセキュリティのスコープを持つ既定のロールを備えているだけでなく、セキュリティで保護された管理用のカスタム ロールを作成できます。
* **管理とデプロイの簡略化** – アプリケーション全体のスタックを、リソース グループ内の&1; つのリソース コレクションとして作成できるため、アプリケーションのデプロイと管理が簡単になります。 テンプレートの JSON ペイロードを用意するだけでデプロイできるため、デプロイにかかる時間が短縮されます。
* **迅速なカスタマイズ** – 宣言型形式のテンプレートを使用して、反復可能な方法で迅速にデプロイをカスタマイズできます。
* **反復可能なカスタマイズ** – 宣言型形式のテンプレートを使用して、反復可能な方法で迅速にデプロイをカスタマイズできます。
* **管理インターフェイス** - リソースの管理には、次のいずれかのインターフェイスを使用できます。
  * REST ベースの API
  * PowerShell
  * .NET SDK
  * Node.JS SDK
  * Java SDK
  * Azure CLI
  * プレビュー ポータル
  * Resource Manager テンプレートの言語

## <a name="network-resources"></a>ネットワーク リソース
ネットワーク リソースを、1 つのコンピューティング リソース (仮想マシン) を介して管理するのではなく、独立して管理できるようになりました。 これにより、リソース グループに複雑かつ大規模なインフラストラクチャを構成する際により高度な柔軟性と俊敏性が保証されます。

複数の層のアプリケーションに関連するサンプルのデプロイの概念図を次に示します。 NIC、パブリック IP アドレス、VM などの各リソースは個別に管理できます。

![ネットワーク リソース モデル](./media/resource-groups-networking/Figure2.png)

すべてのリソースには、共通のプロパティのセットとその個々のプロパティがあります。 共通のプロパティは次のとおりです。

| プロパティ | 説明 | サンプルの値 |
| --- | --- | --- |
| **name** |リソースの一意の名前。 リソースの種類ごとに、独自の名前付け制限がある。 |PIP01、VM01、NIC01 |
| **location** |リソースが存在する Azure リージョン |westus、eastus |
| **id** |URI に基づいた一意の ID |/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

リソースの個々のプロパティは以下のセクションで確認できます。

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>管理インターフェイス
Azure のネットワーク リソースは、さまざまなインターフェイスを使用して管理できます。 このドキュメントでは、これらのインターフェイスのうち REST API およびテンプレートに焦点を当てます。

### <a name="rest-api"></a>REST API
前述のように、ネットワーク リソースは、さまざまなインターフェイス (REST API、NET SDK、Node.JS SDK、Java SDK、PowerShell、CLI、Azure ポータルおよびテンプレートなど) を経由して管理できます。

REST API は、HTTP 1.1 プロトコル仕様に準拠しています。 API の一般的な URI の構造は次のとおりです。

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

中かっこ付きのパラメーターは、次の要素を表します。

* **subscription-id** – Azure のサブスクリプション ID。
* **resource-provider-namespace** – 使用中のプロバイダーの名前空間。 ネットワーク リソース プロバイダーの値は *Microsoft.Network*です。
* **region-name** – Azure リージョン名。

REST API を呼び出す際に、次の HTTP メソッドがサポートされます。

* **PUT** – 指定された型のリソースの作成、リソース プロパティの変更、またはリソース間の関連付けの変更に使用されます。
* **GET** – プロビジョニングされているリソースの情報の取得に使用されます。
* **DELETE** – 既存のリソースの削除に使用されます。

要求と応答はいずれも JSON ペイロードの形式に準拠しています。 詳細については、「 [Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)」を参照してください。

### <a name="resource-manager-template-language"></a>Resource Manager テンプレートの言語
(API または SDK を使って) 命令型でリソースを管理するだけでなく、Resource Manager テンプレート言語を使用することで、宣言型のプログラミング形式によってネットワーク リソースをビルドおよび管理することもできます。

テンプレートのサンプル表現を次に示します –

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

テンプレートとは基本的に言ってリソースおよびインスタンス値をパラメーターで挿入した JSON による記述です。 次の例を使用すると、2 つのサブネットを含む仮想ネットワークを作成できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

テンプレートを使用する場合は、パラメーター値を手動で提供するオプションか、パラメーター ファイルを使用するオプションがあります。 次の例は、上記のテンプレートで使用可能なパラメーター値のセットを示しています。

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


テンプレートを使用する主な利点は、次のとおりです。

* 宣言型形式でリソース グループに複雑なインフラストラクチャをビルドできます。 依存関係の管理など、リソースを作成するオーケストレーションは、Resource Manager によって処理されます。
* インフラストラクチャは、パラメーターを変更するだけで、さまざまなリージョンで、あるいは同一リージョン内で、繰り返し作成することができます。
* 宣言型形式では、テンプレートの作成とインフラストラクチャの展開におけるリード タイムを短縮できます。

テンプレートのサンプルについては、「 [Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates)」を参照してください。

ARM テンプレート言語の詳細については、「[Azure Resource Manager のテンプレート言語](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

上記のサンプル テンプレートでは、仮想ネットワークとサブネットのリソースを使用します。 その他の使用可能なネットワーク リソースは、次の一覧のとおりです。

### <a name="using-a-template"></a>テンプレートの使用
テンプレートから Azure にサービスをデプロイするには、PowerShell、AzureCLI を使用するか、または GitHub からクリック操作によってデプロイします。 GitHub でテンプレートからサービスをデプロイするには、次の手順を実行します。

1. GitHub で template3 ファイルを開きます。 たとえば、 [2 つのサブネットを含む仮想ネットワーク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)を開きます。
2. **[Azure にデプロイ]**をクリックし、資格情報を使用して Azure ポータルにサインインします。
3. テンプレートを確認し、 **[保存]**をクリックします。
4. **[パラメーターの編集]** をクリックし、VNET およびサブネットの場所 ( *[米国西部]*など) を選択します。
5. 必要に応じて、**ADDRESSPREFIX** および **SUBNETPREFIX** パラメーターを変更し、**[OK]** をクリックします。
6. **[リソース グループの選択]** をクリックし、VNET とサブネットの追加先となるリソース グループをクリックします。 あるいは、 **[新規作成]**をクリックして、新しいリソース グループを作成することもできます。
7. **[作成]**をクリックします。 **[テンプレートのデプロイをプロビジョニング中]**というタイルが表示されます。 デプロイが完了すると、画面の表示は以下のようになります。

![サンプル テンプレートのデプロイ](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>次のステップ
[Azure リソース マネージャー テンプレートの言語](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure のネットワーク – 一般的に使用されているテンプレート](https://github.com/Azure/azure-quickstart-templates)

[Azure Resource Manager とクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)

[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)




<!--HONumber=Dec16_HO4-->


