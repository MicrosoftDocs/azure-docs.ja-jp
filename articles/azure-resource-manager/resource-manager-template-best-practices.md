---
title: "Resource Manager テンプレートを作成するためのベスト プラクティス | Microsoft Docs"
description: "Azure Resource Manager テンプレートを単純化するためのガイドラインです。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを作成するためのベスト プラクティス
このガイドラインは、信頼性が高く使いやすい Azure Resource Manager テンプレートを作成するのに役立ちます。 このガイドラインはあくまで提案です。 必須ではありません (必須と示されている場合を除く)。 状況によっては、以下の手法や例のバリエーションが必要になる場合があります。

## <a name="resource-names"></a>リソース名
Resource Manager では、一般に、次の 3 種類のリソース名を使用します。

* 一意である必要があるリソース名
* 一意である必要がないリソース名。ただし、コンテキストに基づいてリソースを識別するのに役立つ名前を選択します。
* 一般的なものでよいリソース名

 リソース名の制限事項については、「 [Azure リソースの推奨される名前付け規則](../guidance/guidance-naming-conventions.md)」を参照してください。

### <a name="unique-resource-names"></a>一意のリソース名
データ アクセス エンドポイントを持つリソースの種類に対しては、一意のリソース名を付ける必要があります。 一意の名前にする必要がある一般的なリソースの種類には次のようなものがあります。

* Azure Storage<sup>1</sup> 
* Azure App Service の Web Apps の機能
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure の Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> ストレージ アカウント名も、小文字で 24 文字以下にする必要があり、ハイフンを含めることはできません。

リソース名をパラメーターで指定する場合は、リソースをデプロイするときに一意な名前を指定する必要があります。 必要に応じて、名前を生成する [uniqueString()](resource-group-template-functions-string.md#uniquestring) 関数を使用する変数を作成することができます。 

**uniqueString** の結果に、プレフィックスまたはサフィックスを追加することもできます。 一意の名前を変更すると、リソースの種類を名前から簡単に識別するのに役立ちます。 たとえば、次の変数を使用してストレージ アカウントの一意の名前を生成できます。

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>特定しやすいリソース名
リソースの種類には、名前を付けることができるが、一意である必要はないものもあります。 このようなリソースの種類には、リソースのコンテキストとリソースの種類の両方を識別する名前を指定できます。 リソースの一覧内のリソースを識別しやすくなるわかりやすい名前を指定します。 デプロイによっては別のリソース名を使用する必要がある場合、名前にパラメーターを使用することができます。

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

デプロイ時に名前を渡す必要がない場合は、次のように変数を使用できます。 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

また、ハードコーディングされた値を使用することもできます。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>一般的なリソース名
別のリソースからアクセスされることの多いリソースの種類に対しては、テンプレートでハードコーディングされた一般的な名前を使用できます。 たとえば、SQL Server のファイアウォール規則には、標準的で一般的な名前を設定できます。

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>パラメーター
パラメーターを使用する場合は、次の情報を活用してください。

* パラメーターの使用を最小限に抑えます。 可能であれば、変数またはリテラル値を使用します。 パラメーターは、次のようなシナリオに対してのみ使用します。
   
   * 環境 (SKU、サイズ、処理能力) に応じてさまざまな設定を使用する場合
   * 特定しやすいリソース名を付ける場合
   * 他のタスクを実行するために頻繁に使用する値 (管理者ユーザー名など) がある場合
   * シークレット情報 (パスワードなど)
   * リソースの種類のインスタンスを複数作成するときに多数の値または値の配列を使用する場合
* パラメーター名にキャメル ケースを使用します。
* メタデータですべてのパラメーターを説明します。

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* パラメーターの既定値を定義します (パスワードおよび SSH キーは除く)。
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* すべてのパスワードおよびシークレット情報に **SecureString** を使用します。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 可能な限り、場所を指定するパラメーターを使用しないでください。 代わりに、リソース グループの **location** プロパティを使用します。 すべてのリソースで **resourceGroup().location** 式を使用すると、テンプレート内のリソースはリソース グループと同じ場所にデプロイされます。
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   リソースの種類がサポートされる場所に限りがある場合は、有効な場所をテンプレートに直接指定することができます。 **location** パラメーターを使用する必要がある場合は、同じ場所に配置される可能性があるリソースでできる限りそのパラメーターを共有してください。 こうすることで、ユーザーが場所情報の入力を求められる回数を最小限に抑えることができます。
* リソースの種類の API バージョンにはパラメーターや変数を使用しないでください。 リソースのプロパティおよび値は、バージョン番号ごとに異なる可能性があります。 パラメーターまたは変数に API バージョンが設定されると、コード エディターの IntelliSense が適切なスキーマを決定できなくなります。 代わりに、テンプレートの API バージョンをハードコーディングしてください。

## <a name="variables"></a>variables
変数を使用する場合は、次の情報を活用してください。

* テンプレート内で複数回使用する必要のある値には、変数を使用してください。 1 回しか使用しない値は、ハードコーディングすることでテンプレートが読みやすくなります。
* テンプレートの **variables** セクションでは [reference](resource-group-template-functions-resource.md#reference) 関数は使用できません。 **reference** 関数は、リソースのランタイム状態からその値を取得します。 ただし、変数が解決されるのは、テンプレートの初期解析時です。 **reference** 関数を必要とする値は、テンプレートの **resources** セクションまたは **outputs** セクションに直接作成してください。
* 「[リソース名](#resource-names)」で説明しているように、一意である必要のあるリソース名には変数を含めてください。
* 変数は複合オブジェクトにまとめてください。 複合オブジェクトの値は、**variable.subentry** 形式を使用して参照できます。 変数をグループ化すると、関連する変数を追跡しやすくなります。 テンプレートの読みやすさも向上します。 次に例を示します。
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > 複合オブジェクトに、複合オブジェクトの値を参照する式を含めることはできません。 この場合は、別の変数を定義してください。
   > 
   > 
   
     変数としての複合オブジェクトの使用に関するより高度な例については、[Azure Resource Manager テンプレートでの状態の共有](best-practices-resource-manager-state.md)に関するページをご覧ください。

## <a name="resources"></a>リソース
リソースを使用する場合は、次の情報を活用してください。

* 他の共同作業者にリソースの用途がわかるように、テンプレート内の各リソースに **comments** を指定してください。
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* タグを使用すると、メタデータをリソースに追加できます。 メタデータを使用して、リソースに関する情報を追加します。 たとえば、リソースに課金の詳細を記録するメタデータを追加できます。 詳細については、 [タグを使用した Azure リソースの整理](resource-group-using-tags.md)に関するページを参照してください。
* テンプレートで "*パブリック エンドポイント*" (Azure Blob Storage のパブリック エンドポイントなど) を使用する場合、名前空間は "*ハードコーディングしないで*" ください。 名前空間を動的に取得するには、**reference** 関数を使用します。 そうすることで、テンプレートのエンドポイントを手作業で変更することなく、別のパブリック名前空間環境にテンプレートをデプロイできます。 API バージョンは、テンプレートのストレージ アカウントで使用するものと同じバージョンにしてください。
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   作成しているテンプレートにストレージ アカウントをデプロイする場合、リソースの参照でプロバイダーの名前空間を指定する必要はありません。 簡略化された構文は次のようになります。
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   テンプレート内にパブリック名前空間を使用するために構成されている他の値がある場合は、同じ **reference** 関数を反映するようにこれらの値を変更してください。 たとえば、仮想マシンの診断プロファイルの **storageUri** プロパティは次のように設定できます。
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   また、別のリソース グループにある既存のストレージ アカウントを参照することもできます。

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 仮想マシンにパブリック IP アドレスを割り当てるのは、アプリケーションで必要な場合のみにしてください。 デバッグや各種管理のために仮想マシン (VM) に接続するには、受信 NAT 規則、仮想ネットワーク ゲートウェイ、またはジャンプボックスを使用してください。
   
     仮想マシンへの接続の詳細については、以下の記事を参照してください。
   
   * [Azure で N 層アーキテクチャの VM を実行する](../guidance/guidance-compute-n-tier-vm.md)
   * [Azure Resource Manager の VM の WinRM アクセスを設定する](../virtual-machines/windows/winrm.md)
   * [Azure Portal を使用して VM への外部アクセスを許可する](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell を使用して VM への外部アクセスを許可する](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI を使用して Linux VM への外部アクセスを許可する](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* パブリック IP アドレスの **domainNameLabel** プロパティは一意である必要があります。 **domainNameLabel** の値は、3 文字以上 63 文字以下で、正規表現 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` で指定された規則に従う必要があります。 **uniqueString** 関数は 13 文字の文字列を生成するため、**dnsPrefixString** パラメーターは 50 文字に制限されます。

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* カスタム スクリプト拡張機能にパスワードを追加する場合は、**protectedSettings** プロパティで **commandToExecute** プロパティを使用してください。
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > シークレット情報を VM と拡張機能にパラメーターとして渡すときに暗号化されるように、関連する拡張機能の **protectedSettings** プロパティを使用する必要があります。
   > 
   > 

## <a name="outputs"></a>出力

テンプレートを使用してパブリック IP アドレスを作成する場合、テンプレートには IP アドレスの詳細と完全修飾ドメイン名 (FQDN) を返す **outputs** セクションを含める必要があります。 出力値を使用して、デプロイ後に簡単にパブリック IP アドレスと FQDN についての詳細を取得できます。 リソースを参照する場合は、リソースの作成で使用した API バージョンを使用してください。 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>単一テンプレートと入れ子になったテンプレートの比較
ソリューションをデプロイする場合、単一テンプレートと、複数のテンプレートが入れ子になったメイン テンプレートのいずれかを使用できます。 高度なシナリオでは、入れ子になったテンプレートが一般的です。 入れ子になったテンプレートを使用する利点は次のとおりです。

* ソリューションを対象コンポーネントに分解できる
* さまざまなメイン テンプレートで入れ子になったテンプレートを再利用できる

入れ子になったテンプレートを使用する場合は、次のガイドラインをテンプレートのデザインの標準化に利用できます。 以下のガイドラインは、[Azure Resource Manager テンプレートのデザイン パターン](best-practices-resource-manager-design-templates.md)に関するページに基づいています。 次のテンプレートを持つデザインが推奨されます。

* **メイン テンプレート** (azuredeploy.json)。 入力パラメーター用に使用します。
* **共有リソース テンプレート**。 他のすべてのリソースで使用する共有リソース (たとえば、仮想ネットワークや可用性セット) をデプロイするために使用します。 **dependsOn** 式を使用して、このテンプレートが他のテンプレートよりも先にデプロイされるようにします。
* **オプション リソース テンプレート**。 パラメーターに基づいてリソースを条件付きでデプロイするために使用します (たとえば、ジャンプボックス)。
* **メンバー リソース テンプレート**。 アプリケーション層内の各インスタンス タイプには、独自の構成があります。 階層内で、さまざまなインスタンスの種類を定義できます  (たとえば、最初のインスタンスでクラスターを作成し、その他のインスタンスは既存のクラスターに追加します)。各インスタンス タイプには、独自のデプロイ テンプレートがあります。
* **スクリプト**。 各インスタンス タイプでは、広範囲に再利用できるスクリプトを適用できます (たとえば、追加ディスクの初期化やフォーマット)。 特定のカスタマイズ用に作成されたカスタム スクリプトは、インスタンス タイプに基づいて異なります。

![入れ子になったテンプレート](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

詳細については、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページをご覧ください。

## <a name="conditionally-link-to-nested-templates"></a>入れ子になったテンプレートへの条件付きリンク
パラメーターを使用すると、入れ子になったテンプレートに条件付きでリンクできます。 このパラメーターは、テンプレートの URI の一部になります。

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>テンプレートの形式
JSON 検証ツールを使用してテンプレートを渡すことをお勧めします。 検証ツールを使用すれば、デプロイ中にエラーを引き起こす可能性のある余分なコンマ、かっこ、および角かっこを削除できます。 [JSONLint](http://jsonlint.com/)、または使い慣れた編集環境のリンター パッケージを使用してください (Visual Studio Code、Atom、Sublime Text、Visual Studio)。

また、読みやすくするために JSON のフォーマットを整えることもお勧めします。 ローカル エディターの JSON フォーマッター パッケージを使用できます。 Visual Studio では、ドキュメントのフォーマットを整えるには、**Ctrl+K、Ctrl+D** を押します。 Visual Studio のコードでは、**Alt+Shift+F** を押します。 ローカル エディターでドキュメントを整形できない場合は、 [オンライン フォーマッター](https://www.bing.com/search?q=json+formatter)を使用してください。

## <a name="next-steps"></a>次のステップ
* 仮想マシン用のソリューションの構築に関するガイダンスについては、「[Run a Windows VM in Azure (Azure での Windows VM の実行)](../guidance/guidance-compute-single-vm.md)」および「[Run a Linux VM in Azure (Azure での Linux VM の実行)](../guidance/guidance-compute-single-vm-linux.md)」を参照してください。
* ストレージ アカウントのセットアップのガイダンスについては、[Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage/common/storage-performance-checklist.md)に関するページをご覧ください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure エンタープライズ スキャフォールディング - 規範的なサブスクリプション ガバナンス](resource-manager-subscription-governance.md)」を参照してください。

