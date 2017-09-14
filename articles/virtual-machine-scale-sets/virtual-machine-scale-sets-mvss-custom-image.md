---
title: "Azure スケール セット テンプレートでのカスタム イメージの参照 | Microsoft Docs"
description: "カスタム イメージを既存の Azure Virtual Machine Scale Set テンプレートに追加する方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017

---

# Azure スケール セット テンプレートにカスタム イメージを追加する
<a id="add-a-custom-image-to-an-azure-scale-set-template" class="xliff"></a>

この記事では、[実行可能な最小のスケール セット テンプレート](./virtual-machine-scale-sets-mvss-start.md)を変更してカスタム イメージをデプロイする方法を説明します。

## テンプレートの定義を変更する
<a id="change-the-template-definition" class="xliff"></a>

実行可能な最小のスケール セット テンプレートは[こちら](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)で確認できます。また、スケール セットをカスタム イメージからデプロイするためのテンプレートは[こちら](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json)で確認できます。 このテンプレートを作成する際に使用する diff (`git diff minimum-viable-scale-set custom-image`) を項目ごとに確認しましょう。

### 管理ディスク イメージを作成する
<a id="creating-a-managed-disk-image" class="xliff"></a>

カスタムの管理ディスク イメージ (`Microsoft.Compute/images` 型のリソース) が既にある場合は、このセクションを省略できます。

最初に、`sourceImageVhdUri` パラメーターを追加します。これは、デプロイ元のカスタム イメージを含む Azure Storage 内の汎用化された blob への URI です。


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

次に、`Microsoft.Compute/images` 型のリソースを追加します。これは、URI `sourceImageVhdUri` にある汎用化された blob に基づく管理ディスク イメージです。 このイメージは、イメージを使用するスケール セットと同じリージョンにある必要があります。 イメージのプロパティで、OS のタイプ、(`sourceImageVhdUri` パラメーターからの) blob の場所、およびストレージ アカウントのタイプを指定します。

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

スケール セット リソースで、カスタム イメージを参照する `dependsOn` 句を追加して、イメージが作成されてから、スケール セットがそのイメージからデプロイを試行するようにします。

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### 管理ディスク イメージを使用するようにスケール セット プロパティを変更する
<a id="changing-scale-set-properties-to-use-the-managed-disk-image" class="xliff"></a>

スケール セット `storageProfile` の `imageReference` で、発行元、プラン、SKU、およびプラットフォーム イメージのバージョンを指定する代わりに、`Microsoft.Compute/images` リソースの `id` を指定します。

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

この例では、`resourceId` 関数を使用して、同じテンプレートで作成したイメージのリソース ID を取得します。 管理ディスク イメージを事前に作成している場合は、代わりにそのイメージの ID を指定する必要があります。 この ID は、`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` の形式で指定する必要があります。


## 次のステップ
<a id="next-steps" class="xliff"></a>

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

