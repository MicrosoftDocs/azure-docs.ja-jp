---
title: Azure VM 用の Chef 拡張機能 | Microsoft Docs
description: Chef VM 拡張機能を使用して、仮想マシンに Chef クライアントをデプロイします。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: e72536cc6f9ec3b94016d16de8502e70bc7107aa
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706081"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Linux および Windows 用の Chef VM 拡張機能

Chef ソフトウェアは、Linux と Windows 向けの DevOps オートメーション プラットフォームで、物理サーバーと仮想サーバー両方の構成を管理できます。 Chef VM 拡張機能は、仮想マシンで Chef を有効にする拡張機能です。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Chef VM 拡張機能は、Azure の[拡張機能がサポートされている OS](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) のすべてでサポートされます。

### <a name="internet-connectivity"></a>インターネット接続

Chef VM 拡張機能では、コンテンツ配信ネットワーク (CDN) から Chef クライアント ペイロードを取得するために、ターゲット仮想マシンがインターネットに接続されている必要があります。  

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Chef VM 拡張機能のスキーマを示しています。 この拡張機能では、少なくとも Chef Server の Chef Server URL、検証クライアント名、および検証キーが必要です。これらの値は、[Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) またはスタンドアロン [Chef Server](https://downloads.chef.io/chef-server) のインストール時にダウンロードされる starter-kit.zip 内の `knife.rb` ファイルで見つけることができます。 検証キーは、機微なデータとして扱う必要があり、**protectedSettings** 要素の下に構成する必要があります。これはターゲット仮想マシンでのみ復号化されることを意味します。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>コア プロパティ値

| Name | 値/例 | データ型
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | string |
| type | `LinuxChefClient` (Linux)、`ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>設定

| Name | 値/例 | データ型 | 必須
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | Y |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| settings/runlist | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>保護された設定

| Name | 例 | データ型 | 必須
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートを使用して、1 つまたは複数の仮想マシンをデプロイし、Chef Client をインストールし、Chef Server に接続し、[実行リスト](https://docs.chef.io/run_lists.html)に定義されているようにサーバー上で初期構成を実行できます。

Chef VM 拡張機能を含む Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)にあります。

仮想マシン拡張機能の JSON 構成は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON 構成の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/resource-manager-template-child-resource.md)に関する記事を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Chef VM 拡張機能を既存の仮想マシンにデプロイできます。 **validation_key** を自分の検証キーの内容と置き換えてください (このファイルでは `.pem` 拡張機能として)。  **validation_client_name**、**chef_server_url**、および **run_list** の値を、Starter Kit の `knife.rb` ファイルの値に置き換えてください。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>エラー コードとその意味

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 51 | VM のオペレーティング システムでは、この拡張機能はサポートされていません | |

トラブルシューティングに関する追加情報は、[Chef VM 拡張機能の readme](https://github.com/chef-partners/azure-chef-extension) に記載されています。

## <a name="next-steps"></a>次の手順

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
