---
title: "管理ディスクを使用するために Azure Resource Manager のスケール セット テンプレートを変換する | Microsoft Docs"
description: "スケール セット テンプレートを管理ディスク スケール セット テンプレートに変換します。"
keywords: "仮想マシン スケール セット"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/25/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 01983ac7b60f7fdb237fba27f22cb31fcb3c1b7b
ms.openlocfilehash: cd1e67ce89a856f325b66087f003b1a9a1ac6f6a


---


# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>スケール セット テンプレートを管理ディスク スケール セット テンプレートに変換する

Resource Manager テンプレートを基に管理ディスクを使用しないスケール セットを作成している場合、管理ディスクを使用するようにそのテンプレートを変更することができます。 この記事では、「[Azure Quickstart Templates (Azure クイック スタート テンプレート)](https://github.com/Azure/azure-quickstart-templates)」ページ (コミュニティにより運営されている、サンプルの Resource Manager テンプレートを集めたリポジトリ) に登録されているプル要求の例を使用してこの操作を行う方法を示します。 完全なプル要求は [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) で確認できます。以降では、該当する差分とその説明を示していきます。

## <a name="making-the-os-disks-managed"></a>OS ディスクを管理ディスクにする

次の差分では、ストレージ アカウントとディスクのプロパティに関連するいくつかの変数が削除されていることがわかります。 ストレージ アカウントの種類はもう必要ありません (Standard_LRS が既定です)。ただし、必要に応じて指定することはできます。 管理ディスクでは、Standard_LRS と Premium_LRS のみがサポートされます。 古いテンプレートでは、新しいストレージ アカウントのサフィックス、一意の文字列配列、および sa カウントが、ストレージ アカウント名を生成するために使用されていました。 管理ディスクでは自動的にストレージ アカウントが作成されるため、新しいテンプレートではこれらの変数が不要になりました。 同様に、管理ディスクでは基になるストレージ BLOB コンテナーとディスクの名前が自動的に付けられるため、VHD コンテナー名と OS ディスク名も不要になりました。

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


次の差分では、コンピューティング API のバージョンが 2016-04-30-preview に更新されていることがわかります。これは、スケール セットでの管理ディスクのサポートに必要な最も初期のバージョンです。 ただし、必要に応じて古い構文を使用して、管理されないディスクを新しい API バージョンで使用することも引き続き可能です。 つまり、コンピューティング API バージョンのみを更新し、それ以外のものを変更しない場合、テンプレートは以前と同じように動作します。

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

次の差分では、リソース配列からストレージ アカウント リソースが完全に削除されていることがわかります。 管理ディスクではこれらのリソースは自動的に作成されるため、必要ありません。

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

次の差分では、ストレージ アカウントを作成したループをスケール セットから参照する depends on 句が削除されていることがわかります。 古いテンプレートでは、これによって、スケール セットの作成を開始する前にストレージ アカウントが作成されていることが保証されていました。しかし、管理ディスクでは、この句はもう必要ありません。 また、VHD コンテナー プロパティと OS ディスク名プロパティも削除されています。管理ディスクでは、これらのプロパティが自動的に処理されます。 Premium OS ディスクが必要な場合は、"osDisk" 構成に `"managedDisk": { "storageAccountType": "Premium_LRS" }` を追加することができます。 Premium ディスクを使用できるのは、VM SKU に大文字または小文字の "s" が含まれる VM に限られます。

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

スケール セット構成には、管理ディスクと管理されないディスクのどちらを使用するかについての明示的なプロパティはありません。 スケール セットは、ストレージ プロファイルに存在するプロパティに基づいて、使用するディスクを判断します。 したがって、テンプレートを変更する場合は、適切なプロパティがスケール セットのストレージ プロファイルに存在していることを確認することが重要です。


## <a name="data-disks"></a>データ ディスク

上記の変更により、スケール セットで OS ディスクに管理ディスクが使用されるようになります。それでは、データ ディスクの場合はどうでしょうか。 データ ディスクを追加するには、"storageProfile" の下の "osDisk" と同じレベルに "dataDisks" プロパティを追加します。 このプロパティの値には、オブジェクトの JSON リストを指定します。それぞれのオブジェクトには、次の例に示すとおり、"lun" (VM 上のデータ ディスクごとに一意である必要があります)、"createOption" (現在、"empty" のみがサポートされています)、および "diskSizeGB" (ギガバイト単位のディスク サイズ。0 より大きく 1,024 未満の値である必要があります) の各プロパティがあります。 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

この配列に `n` 個のディスクを指定した場合、スケール セット内の各 VM は `n` 個のデータ ディスクを取得します。 ただし、これらのデータ ディスクは未加工デバイスであることに注意してください。 これらのデータ ディスクはフォーマットされていません。 ユーザーは、ディスクを使用する前に、ディスクの接続、パーティション分割、フォーマットを行う必要があります。 必要に応じて、各データ ディスク オブジェクトに `"managedDisk": { "storageAccountType": "Premium_LRS" }` を指定して、Premium データ ディスクを使用することを指定できます。 Premium ディスクを使用できるのは、VM SKU に大文字または小文字の "s" が含まれる VM に限られます。

スケール セットでデータ ディスクを使用する方法の詳細については、[この記事](./virtual-machine-scale-sets-attached-disks.md)を参照してください。


## <a name="next-steps"></a>次のステップ
スケール セットを使用した Resource Manager テンプレートの例については、 [Azure クイックスタート テンプレートの GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)で "vmss" を検索してください。

全般的な情報については、 [VM スケール セットのメイン ランディング ページ](https://azure.microsoft.com/services/virtual-machine-scale-sets/)をご覧ください。




<!--HONumber=Feb17_HO2-->


