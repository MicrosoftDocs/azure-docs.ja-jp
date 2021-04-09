---
title: ポータルを使用した Azure Spot Virtual Machines のデプロイ
description: Azure PowerShell を使用して Spot Virtual Machines をデプロイし、コストを節約する方法。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098572"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Azure portal を使用して Azure Spot Virtual Machines をデプロイする

[Azure Spot Virtual Machines](spot-vms.md) を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャによって Azure スポット仮想マシンが削除されます。 したがって、Azure スポット仮想マシンは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

Azure スポット仮想マシンの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 最大価格の設定の詳細については、[Azure スポット仮想マシンの価格](spot-vms.md#pricing)に関するページを参照してください。

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 Azure Spot Virtual Machine の最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.05701` の値は、1 時間あたり $0.05701 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。

VM の削除時には、VM とその基になっているディスクを削除するオプションと、後で再開できるように VM の割り当てを解除するオプションが用意されています。


## <a name="create-the-vm"></a>VM の作成

VM をデプロイするときに、Azure スポット インスタンスを使用するように選択できます。


**[基本]** タブの **[インスタンスの詳細]** セクションでは、Azure スポット インスタンスを使用する場合の既定は **[いいえ]** です。

![[no, don't use an Azure spot instance]\(いいえ、Azure スポット インスタンスを使用しません\) を選択する画面のキャプチャ](./media/spot-portal/no.png)

**[はい]** を選択すると、セクションが展開され、[削除の種類と削除ポリシー](spot-vms.md#eviction-policy)を選択できます。 

![[no, don't use an Azure spot instance]\(はい、Azure スポット インスタンスを使用します\) を選択する画面のキャプチャ](./media/spot-portal/yes.png)

また、 **[View pricing history and compare prices in nearby regions]\(価格履歴を表示し、近くのリージョンの価格を比較する\)** を選択することで、価格率や削除率を比較できます。

この例では、カナダ中部リージョンは米国東部リージョンに比べ、価格と削除率が低くなっています。

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="リージョン オプションのスクリーンショット。価格と削除率に違いがあります。":::

最適なリージョンを選択して **[OK]** を選択することでリージョンを変更できます。

## <a name="simulate-an-eviction"></a>削除をシミュレートする

Azure Spot Virtual Machine の[削除をシミュレート](/rest/api/compute/virtualmachines/simulateeviction)して、突然の削除に対してアプリケーションがどの程度適切に対応するかをテストすることができます。 

次の情報をお客様の情報に置き換えてください。 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` は、シミュレートされた削除が成功したことを意味します。 

## <a name="next-steps"></a>次のステップ

[PowerShell](./windows/spot-powershell.md)、[CLI](./linux/spot-cli.md)、[テンプレート](./linux/spot-template.md)を使用して Azure Spot Virtual Machines を作成することもできます。
