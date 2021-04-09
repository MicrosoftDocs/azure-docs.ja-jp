---
title: 作成または更新操作の PUT 呼び出し
description: コンピューティング リソースに対する作成または更新操作の PUT 呼び出し
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978562"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>コンピューティング リソースに対する作成または更新の PUT 呼び出し

`Microsoft.Compute` リソースでは *HTTP PUT* セマンティクスの従来の定義はサポートされていません。 代わりに、これらのリソースでは、PUT 動詞と PATCH 動詞の両方で PATCH セマンティクスが使用されます。

**作成** 操作では、必要に応じて既定値が適用されます。 しかし、PUT または PATCH によって実行されるリソースの **更新** では、既定のプロパティは適用されません。 **更新** 操作では、厳密な PATCH セマンティクスが適用されます。

たとえば、仮想マシンのディスク `caching` プロパティは、リソースが OS ディスクの場合、既定では `ReadWrite` になります。

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

しかし、プロパティが含まれていない場合や *null* 値が渡された場合の **更新** 操作時には、それは変更されず、既定値とはなりません。

これは、関連付けを削除する目的でリソースに更新操作を送信するときに重要になります。 対象のリソースが `Microsoft.Compute` リソースの場合は、削除対象のプロパティを明示的に指定し、値を割り当てる必要があります。 これを実現するために、ユーザーは **" "** のような空の文字列を渡すことができます。 こうすることで、対象の関連付けを削除するようプラットフォームに指示されます。

> [!IMPORTANT]
> 配列の 1 つの要素に対する "パッチ適用" はサポートされていません。 代わりにクライアントによって、更新された配列の内容全体を含めて PUT または PATCH 要求が実行される必要があります。 たとえば、VM からデータ ディスクをデタッチするには、GET 要求を実行して現在の VM モデルを取得し、`properties.storageProfile.dataDisks` からデタッチするディスクを削除して、更新された VM エンティティを使用して PUT 要求を実行します。

## <a name="examples"></a>例

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>近接配置グループの関連付けを削除するための正しいペイロード

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>近接配置グループの関連付けを削除するための誤ったペイロード

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>次の手順
[仮想マシン](/rest/api/compute/virtualmachines/createorupdate)および[仮想マシン スケール セット](/rest/api/compute/virtualmachinescalesets/createorupdate)の作成または更新の呼び出しの詳細を確認する