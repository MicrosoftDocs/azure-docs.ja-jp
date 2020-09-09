---
title: ポータルを使用した Azure スポット VM のデプロイ
description: Azure PowerShell を使用してスポット VM をデプロイし、コストを節約する方法。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: ef5fa0aafca1312480f51614a1ba1692c09a13b8
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816577"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Azure portal を使用してスポット VM をデプロイする

[スポット VM](../spot-vms.md) を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット VM を削除します。 したがって、スポット VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

スポット VM の価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 最大価格の設定の詳細については、[スポット VM の価格](../spot-vms.md#pricing)に関するページを参照してください。

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 スポット VM の最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.05701` の値は、1 時間あたり $0.05701 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。

VM の削除時には、VM とその基になっているディスクを削除するオプションと、後で再開できるように VM の割り当てを解除するオプションが用意されています。


## <a name="create-the-vm"></a>VM の作成

スポット VM を使用する VM を作成するプロセスは、[クイックスタート](quick-create-portal.md)で詳しく説明されているプロセスと同じです。 VM をデプロイするときに、Azure スポット インスタンスを使用するように選択できます。


**[基本]** タブの **[インスタンスの詳細]** セクションでは、Azure スポット インスタンスを使用する場合の既定は **[いいえ]** です。

![[no, don't use an Azure spot instance]\(いいえ、Azure スポット インスタンスを使用しません\) を選択する画面のキャプチャ](media/spot-portal/no.png)

**[はい]** を選択すると、セクションが展開され、[削除の種類と削除ポリシー](../spot-vms.md#eviction-policy)を選択できます。 

![[no, don't use an Azure spot instance]\(はい、Azure スポット インスタンスを使用します\) を選択する画面のキャプチャ](media/spot-portal/yes.png)


## <a name="next-steps"></a>次のステップ

[PowerShell](spot-powershell.md) を使用してスポット VM を作成することもできます。