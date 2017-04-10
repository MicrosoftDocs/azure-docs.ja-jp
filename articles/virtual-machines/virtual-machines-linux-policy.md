---
title: "Azure の Linux VM にセキュリティとポリシーを適用する | Microsoft Docs"
description: "Azure Resource Manager Linux 仮想マシンにポリシーを適用する方法"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 1887b777fbe113d59cffb6ba5409e33ca1380880
ms.lasthandoff: 02/22/2017


---
# <a name="apply-security-and-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager を使用して Linux VM にセキュリティとポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の仮想マシンの望ましい行動を定義する方法について説明します。

これを実現するための手順の概要を、以下に示します

1. Azure Resource Manager ポリシー 101
2. 仮想マシンのポリシーの定義
3. ポリシーの作成
4. ポリシーの適用

## <a name="azure-resource-manager-policy-101"></a>Azure Resource Manager ポリシー 101
Azure Resource Manager ポリシーの作業を開始するにあたって、この記事の手順を実行する前に、次の記事を読むことをお勧めします。 次の記事では、ポリシーの基本的な定義と構造や、ポリシーが評価されるしくみが説明されています。ポリシーの定義のさまざまな例も示されています。

* [ポリシーを使用したリソース管理とアクセス制御](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>仮想マシンのポリシーの定義
企業の一般的なシナリオでは、LOB アプリケーションとの互換性をテスト済みの特定のオペレーティング システムからだけ、ユーザーが仮想マシンを作成することを許可します。 Azure Resource Manager ポリシーを使用すると、このタスクをわずかな手順で完了できます。
このポリシーの例では、Ubuntu 14.04.2-LTS 仮想マシンの作成のみを許可することにします。 ポリシー定義は、次のようになります。

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

上のポリシーは、以下のように変更することで、仮想マシンのデプロイに任意の Ubuntu LTS イメージの使用を許可するシナリオに簡単に変更できます。

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>仮想マシンのプロパティ フィールド
次の表は、ポリシー定義内のフィールドとして使用できる仮想マシン プロパティを示しています。 ポリシーについては、「[ポリシーを使用したリソース管理とアクセス制御](../azure-resource-manager/resource-manager-policy.md#conditions)」をご覧ください。

| フィールド名 | Description |
| --- | --- |
| imagePublisher |イメージの発行元を指定します |
| imageOffer |選択したイメージの発行元に対するオファーを指定します |
| imageSku |選択したオファーの SKU を指定します |
| imageVersion |選択した SKU のイメージのバージョンを指定します |

## <a name="create-the-policy"></a>ポリシーの作成
ポリシーは、REST API を直接使用するか、PowerShell コマンドレットを使用して、簡単に作成できます。 [ポリシーの作成と割り当て](../azure-resource-manager/resource-manager-policy.md)の詳細を参照できます。

## <a name="apply-the-policy"></a>ポリシーの適用
ポリシーを作成した後は、定義されたスコープに対して適用する必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 [ポリシーの作成と割り当て](../azure-resource-manager/resource-manager-policy.md)の詳細を参照できます。

