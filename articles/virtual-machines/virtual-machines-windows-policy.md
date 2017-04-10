---
title: "Azure の Windows VM にセキュリティとポリシーを適用する | Microsoft Docs"
description: "Azure Resource Manager Windows 仮想マシンにポリシーを適用する方法"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/13/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 54afb2f55ef9ff48448c63bc8ee8e745765f61e6
ms.lasthandoff: 02/22/2017


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>Azure Resource Manager を使って Windows VM にセキュリティとポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の仮想マシンの望ましい行動を定義する方法について説明します。

これを実現するための手順の概要を、以下に示します

1. Azure Resource Manager ポリシー 101
2. 仮想マシンのポリシーの定義
3. ポリシーの作成
4. ポリシーの適用

## <a name="azure-resource-manager-policy-101"></a>Azure Resource Manager ポリシー 101
Azure Resource Manager ポリシーの作業を開始するにあたって、この記事の手順を実行する前に、次の記事を読むことをお勧めします。 次の記事では、ポリシーの基本的な定義と構造や、ポリシーが評価されるしくみが説明されています。ポリシーの定義のさまざまな例も示されています。

* [ポリシーを使用したリソース管理とアクセス制御](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>仮想マシンのポリシーの定義
企業の一般的なシナリオでは、LOB アプリケーションとの互換性をテスト済みの特定のオペレーティング システムからだけ、ユーザーが仮想マシンを作成することを許可します。 Azure Resource Manager ポリシーを使用すると、このタスクをわずかな手順で完了できます。
このポリシーの例では、Windows Server 2012 R2 Datacenter 仮想マシンの作成のみを許可することにします。 ポリシー定義は、次のようになります。

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
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
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

上のポリシーは、以下のように変更することで、仮想マシンのデプロイに任意の Windows Server Datacenter イメージの使用を許可するシナリオに簡単に変更できます。

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>仮想マシンのプロパティ フィールド
次の表は、ポリシー定義内のフィールドとして使用できる仮想マシン プロパティを示しています。 ポリシー フィールドの詳細については、以下の記事を参照してください。

* [フィールドおよびソース](../azure-resource-manager/resource-manager-policy.md#conditions)

| フィールド名 | Description |
| --- | --- |
| imagePublisher |イメージの発行元を指定します |
| imageOffer |選択したイメージの発行元に対するオファーを指定します |
| imageSku |選択したオファーの SKU を指定します |
| imageVersion |選択した SKU のイメージのバージョンを指定します |

## <a name="create-the-policy"></a>ポリシーの作成
ポリシーは、REST API を直接使用するか、PowerShell コマンドレットを使用して、簡単に作成できます。 ポリシーの作成については、以下の記事を参照してください。

* [ポリシーの作成](../azure-resource-manager/resource-manager-policy.md)

## <a name="apply-the-policy"></a>ポリシーの適用
ポリシーを作成した後は、定義されたスコープに対して適用する必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポリシーの適用については、以下の記事を参照してください。

* [ポリシーの作成](../azure-resource-manager/resource-manager-policy.md)

