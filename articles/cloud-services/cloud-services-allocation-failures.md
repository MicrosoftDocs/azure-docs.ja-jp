---
title: Cloud Services (クラシック) の割り当てエラーのトラブルシューティング | Microsoft Docs
description: Azure Cloud Services をデプロイするときの割り当てエラーのトラブルシューティング。 割り当てのしくみと、なぜ割り当てが失敗することがあるかについて説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738312"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Azure で Cloud Services (クラシック) をデプロイするときの割り当てエラーのトラブルシューティング

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。


## <a name="summary"></a>まとめ

インスタンスをクラウド サービスにデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。 これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。 この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。 この情報は、サービスのデプロイを計画する場合にも役立ちます。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>背景情報 – 割り当てのしくみ

Azure データセンターのサーバーは、クラスターにパーティション分割されています。 複数のクラスターで、新しいクラウド サービスの割り当て要求が試みられます。 最初のインスタンスを (ステージング環境または運用環境の) クラウド サービスにデプロイすると、このクラウド サービスは 1 つのクラスターに固定されます。 このクラウド サービスでさらにデプロイすると、すべて同じクラスターで処理されます。 この記事では、このことを "クラスターに固定されている" と言います。 次の図 1 は、複数のクラスターで試行される通常の割り当てを示しています。図 2 は、クラスター 2 に固定されている割り当てを示しています。これは、既存のクラウド サービス CS_1 がクラスター 2 でホストされているためです。

![Allocation Diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>割り当てエラーが発生する理由

割り当て要求がクラスターに固定されている場合、利用可能なリソース プールが 1 つのクラスターに制限されるため、空きリソースを見つけられない可能性が高くなります。 さらに、割り当て要求がクラスターに固定されていて、そのクラスターでは要求されたリソースの種類がサポートされていない場合、クラスターに空きリソースがあっても、要求は失敗します。 次の図 3 は、唯一の候補であるクラスターに空きリソースがないため、固定された割り当てがエラーになる場合を示しています。 図 4 は、唯一の候補であるクラスターに空きリソースがあるものの、要求された VM サイズをサポートしていないため、固定された割り当てがエラーになる場合を示しています。

![Pinned Allocation Failure](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>クラウド サービスの割り当てエラーのトラブルシューティング

### <a name="error-message"></a>エラー メッセージ

Azure portal で、ご利用のクラウド サービスに移動し、サイドバーの *[操作ログ (クラシック)]* を選択してログを表示します。

以下の例外についてさらなる解決策を参照してください。

|例外の種類  |エラー メッセージ  |解決策  |
|---------|---------|---------|
|FabricInternalServerError     |操作は、エラー コード 'InternalError' とエラー メッセージ 'サーバーで内部エラーが発生しました。 要求をもう一度お試しください。' で失敗しました。|[FabricInternalServerError のトラブルシューティング](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |操作は、エラー コード 'InternalError' とエラー メッセージ 'サーバーで内部エラーが発生しました。 要求をもう一度お試しください。' で失敗しました。|[ServiceAllocationFailure のトラブルシューティング](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |操作 '`{Operation ID}`' が失敗しました: '要求された VM 層は、現在、このサブスクリプションのリージョン (`{Region ID}`) では利用できません。 別の層を試すか、別の場所にデプロイしてください。'。|[LocationNotFoundForRoleSize のトラブルシューティング](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |Azure 操作 '`{Operation ID}`' がコード Compute.ConstrainedAllocationFailed で失敗しました。 詳細:割り当てに失敗しました。要求の制約条件を満たしていません。 要求されている新しいサービスのデプロイがアフィニティ グループにバインドされているか、仮想ネットワークを対象としています。または、このホストされるサービスに既存のデプロイがあります。 ここに挙げた条件に該当する場合には、新しいデプロイが特定の Azure リソースに制限されます。 後でもう一度やり直すか、VM サイズを小さくするか、ロール インスタンスの数を減らしてください。 可能な場合には、上に挙げた制約条件を削除することや、別のリージョンにデプロイすることもできます。|[ConstrainedAllocationFailed のトラブルシューティング](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |デプロイ要求の制約条件のため、このデプロイに必要な VM サイズ (または VM サイズの組み合わせ) をプロビジョニングできません。 可能であれば、仮想ネットワークのバインドなどの制約条件の緩和、他のデプロイがなくアフィニティ グループが異なるホステッド サービスへのデプロイ、アフィニティ グループのないホステッド サービスへのデプロイ、別のリージョンへのデプロイのいずれかを試してください。|[OverconstrainedAllocationRequest のトラブルシューティング](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

サンプルのエラー メッセージ:

> "Azure 操作 '{操作 ID}' がコード Compute.ConstrainedAllocationFailed で失敗しました。 詳細:割り当てに失敗しました。要求の制約条件を満たしていません。 要求されている新しいサービスのデプロイがアフィニティ グループにバインドされているか、仮想ネットワークを対象としています。または、このホストされるサービスに既存のデプロイがあります。 ここに挙げた条件に該当する場合には、新しいデプロイが特定の Azure リソースに制限されます。 後でもう一度やり直すか、VM サイズを小さくするか、ロール インスタンスの数を減らしてください。 可能な場合には、上に挙げた制約条件を削除することや、別のリージョンにデプロイすることもできます。"

### <a name="common-issues"></a>一般的な問題

以下に、割り当て要求が単一のクラスターに固定される原因となる一般的な割り当てシナリオを示します。

* ステージング スロットへのデプロイ - クラウド サービスをいずれかのスロットにデプロイすると、クラウド サービス全体が特定のクラスターに固定されます。  つまり、運用スロットにデプロイが既に存在する場合、新しいステージング環境のデプロイは、運用スロットと同じクラスターにしか割り当てることができません。 クラスターが限界に近づくと、要求が失敗する場合があります。
* スケーリング - 既存のクラウド サービスに新しいインスタンスを追加する場合は、同じクラスターに割り当てる必要があります。  通常、小規模のスケーリング要求は割り当てることができますが、常に割り当てることができるわけではありません。 クラスターが限界に近づくと、要求が失敗する場合があります。
* アフィニティ グループ - 空のクラウド サービスに新しくデプロイする場合は、クラウド サービスがアフィニティ グループに固定されている場合を除き、そのリージョン内の任意のクラスターのファブリックによって割り当てることができます。 同じアフィニティ グループへのデプロイは、同じクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。
* アフィニティ グループ vNet - 以前の Virtual Network はリージョンではなくアフィニティ グループに関連付けられていたため、これらの仮想ネットワーク内のクラウド サービスはアフィニティ グループのクラスターに固定されました。 このような仮想ネットワークへのデプロイは、固定されたクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。

## <a name="solutions"></a>ソリューション

1. 新しいクラウド サービスに再デプロイする - この解決方法ではそのリージョン内のすべてのクラスターからプラットフォームを選択できるため、成功する可能性が最も高くなります。

   * 新しいクラウド サービスにワークロードをデプロイします  
   * 新しいクラウド サービスへのトラフィックを指すように CNAME または A レコードを更新します
   * 以前のサイトに送信されるトラフィックが 0 個になると、以前のクラウド サービスを削除できます。 この解決方法ではダウンタイムは発生しません。
2. 運用スロットとステージング スロットの両方を削除する - この解決方法では、既存の DNS 名が維持されますが、アプリケーションでダウンタイムが発生します。

   * 既存のクラウド サービスの運用スロットとステージング スロットを削除し、クラウド サービスを空にします。
   * 次に、既存のクラウド サービスに新しいデプロイを作成します。 これにより、リージョン内のすべてのクラスターで割り当てが再度試行されます。 クラウド サービスがアフィニティ グループに関連付けられていないことを確認します。
3. 予約済み IP - この解決方法では、既存の IP アドレスが維持されますが、アプリケーションでダウンタイムが発生します。  

   * PowerShell を使用して既存のデプロイ用の ReservedIP を作成します

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * 上の 2. の手順に従って、サービスの CSCFG で新しい ReservedIP を確実に指定します。
4. 新しいデプロイのアフィニティ グループを削除する - アフィニティ グループは推奨されなくなりました。 上の 1. の手順に従って、新しいクラウド サービスをデプロイしてください。 クラウド サービスがアフィニティ グループ内にないことを確認します。
5. リージョン Virtual Network に変換する - 「 [アフィニティ グループから、リージョン Virtual Network (VNet) に移行する方法](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet)」を参照してください。
