---
title: Azure スポット VM を使用する
description: Azure スポット VM を使用してコストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/20/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: c0b8f395dde1d94c4c1efa32a2f78707d1456d88
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817286"
---
# <a name="use-spot-vms-in-azure"></a>Azure でスポット VM を使用する

スポット VM を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット VM を削除します。 したがって、スポット VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

利用可能な容量は、サイズ、リージョン、時刻などによって異なります。 スポット VM を展開すると、利用可能な容量がある場合は Azure は VM を割り当てますが、このような VM には SLA はありません。 スポット VM は、高可用性の保証を提供しません。 Azure インフラストラクチャは、Azure の容量が必要になった時点で、30 秒前通知の後スポット VM が削除されます。 


## <a name="eviction-policy"></a>削除ポリシー

VM は、容量または設定した最大価格に基づいて削除できます。 スポット VM を作成するときは､この排除ポリシーを *[割り当てを解除する]* (既定値) または *[削除]* に設定できます｡ 

*[割り当てを解除する]* ポリシーでは､VM が停止済み/割り当て解除済み状態に移行され､後でもう一度デプロイすることができます｡ ただし､割り当てが成功する保証はありません｡ 割り当てを解除された VM はクォータに影響を及ぼし、基になるディスクのストレージ コストが課金されます。 

VM が排除されるときに、VM を削除したい場合は、排除ポリシーを *[削除]* に設定できます。 排除された VM は基になるディスクと共に削除されるので、ストレージの料金が引き続き発生することはありません。 

[Azure Scheduled Events](./linux/scheduled-events.md) を通じて VM 内通知を受け取ることができます。 これにより、VM が排除されつつある場合には通知が送られ、排除される前にジョブを完了し、タスクのシャットダウンを実行するために 30 秒が与えられます。 


| オプション | 結果 |
|--------|---------|
| 最大価格は >= 現在の価格に設定されています。 | 容量とクォータが使用可能な場合は、VM がデプロイされます。 |
| 最大価格は < 現在の価格に設定されています。 | VM がデプロイされていません。 最大価格が >= 現在の価格である必要があることを示すエラー メッセージが表示されます。 |
| 最大価格が >= 現在の価格の場合は VM の停止/割り当て解除を再起動する | 容量とクォータがある場合は、VM がデプロイされます。 |
| 最大価格が < 現在の価格の場合は VM の停止/割り当て解除を再起動する | 最大価格が >= 現在の価格である必要があることを示すエラー メッセージが表示されます。 | 
| VM の価格が上がり、現在価格が > 最大価格になりました。 | VM が削除されます。 実際に削除する前に、30 秒前通知を受け取ります。 | 
| 削除した後、VM の料金が < 最大価格に戻ります。 | VM は自動的に再起動されません。 自分で VM を再起動することができ、現在の価格で課金されます。 |
| 最大価格が `-1` に設定されている場合 | VM は価格の理由で削除されません。 最大価格は、Standard VM の価格を上限にした現在の価格になります。 標準価格を超えて課金されることはありません。| 
| 最大価格を変更する | 最大価格を変更するには、VM の割り当てを解除する必要があります。 VM の割り当てを解除し、新しい最大価格を設定して、VM を更新します。 |


## <a name="limitations"></a>制限事項

次の VM サイズは、スポット VM ではサポートされていません。
 - B シリーズ
 - 任意のサイズのキャンペーン バージョン (Dv2、NV、NC、H キャンペーン サイズなど)

スポット VM は、Microsoft Azure China 21Vianet を除き、任意のリージョンにデプロイできます。

<a name="channel"></a>

現在サポートされている[オファーの種類](https://azure.microsoft.com/support/legal/offer-details/)は次のとおりです。

-   Enterprise Agreement
-   従量課金制
-   スポンサー
- クラウド サービス プロバイダー (CSP) の場合、パートナーにお問い合わせください


## <a name="pricing"></a>価格

スポット VM の価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 


可変する価格に対して、最大 5 桁の小数点以下を使用して、最大価格を米ドル (USD) で設定することができます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。


##  <a name="frequently-asked-questions"></a>よく寄せられる質問

**質問:** 作成後、スポット VM は通常の標準の VM と同じになりますか。

**A:** はい。ただし、スポット VM に対する SLA はなく、いつでも排除される可能性があります。


**質問:** 排除された後も容量が必要な場合はどうすればよいですか?

**A:** 容量がすぐに必要な場合は、スポット VM ではなく、標準の VM を使用することをお勧めします。


**質問:** スポット VM のクォータはどのように管理されますか?

**A:** スポット VM には、個別のクォータ プールがあります。 スポット クォータは、VM とスケール セット インスタンスの間で共有されます。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)」をご覧ください。


**質問:** スポットに追加のクォータを要求することはできますか。

**A:** はい。[標準のクォータ要求プロセス](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)を通じて、スポット VM のクォータを増やす要求を送信することができます。


**質問:** どこで質問を投稿できますか。

**A:** [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html) で質問を投稿し、`azure-spot` のタグを付けることができます。 

## <a name="next-steps"></a>次のステップ
[CLI](./linux/spot-cli.md)、[ポータル](./windows/spot-portal.md)、[ARM テンプレート](./linux/spot-template.md)、または [PowerShell](./windows/spot-powershell.md) を使用して、スポット VM をデプロイします。

また、[スポット VM インスタンスを使用したスケール セット](../virtual-machine-scale-sets/use-spot.md)をデプロイすることもできます。

エラーが発生した場合は、[エラー コード](./error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。
