---
title: クラウド サービスの割り当てエラーのトラブルシューティング | Microsoft Docs
description: Azure で Cloud Services をデプロイするときの割り当てエラーのトラブルシューティング
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155636"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Azure で Cloud Services をデプロイするときの割り当てエラーのトラブルシューティング
## <a name="summary"></a>まとめ
インスタンスをクラウド サービスにデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。 これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。 この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。 この情報は、サービスのデプロイを計画する場合にも役立ちます。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>背景情報 – 割り当てのしくみ
Azure データセンターのサーバーは、クラスターにパーティション分割されています。 複数のクラスターで、新しいクラウド サービスの割り当て要求が試みられます。 最初のインスタンスを (ステージング環境または運用環境の) クラウド サービスにデプロイすると、このクラウド サービスは 1 つのクラスターに固定されます。 このクラウド サービスでさらにデプロイすると、すべて同じクラスターで処理されます。 この記事では、このことを "クラスターに固定されている" と言います。 次の図 1 は、複数のクラスターで試行される通常の割り当てを示しています。図 2 は、クラスター 2 に固定されている割り当てを示しています。これは、既存のクラウド サービス CS_1 がクラスター 2 でホストされているためです。

![Allocation Diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>割り当てエラーが発生する理由
割り当て要求がクラスターに固定されている場合、利用可能なリソース プールが 1 つのクラスターに制限されるため、空きリソースを見つけられない可能性が高くなります。 さらに、割り当て要求がクラスターに固定されていて、そのクラスターでは要求されたリソースの種類がサポートされていない場合、クラスターに空きリソースがあっても、要求は失敗します。 次の図 3 は、唯一の候補であるクラスターに空きリソースがないため、固定された割り当てがエラーになる場合を示しています。 図 4 は、唯一の候補であるクラスターに空きリソースがあるものの、要求された VM サイズをサポートしていないため、固定された割り当てがエラーになる場合を示しています。

![固定された割り当てのエラー](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>クラウド サービスの割り当てエラーのトラブルシューティング
### <a name="error-message"></a>エラー メッセージ
次のようなエラー メッセージが表示されることがあります。

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>一般的な問題
以下に、割り当て要求が単一のクラスターに固定される原因となる一般的な割り当てシナリオを示します。

* ステージング スロットへのデプロイ - クラウド サービスをいずれかのスロットにデプロイすると、クラウド サービス全体が特定のクラスターに固定されます。  つまり、運用スロットにデプロイが既に存在する場合、新しいステージング環境のデプロイは、運用スロットと同じクラスターにしか割り当てることができません。 クラスターが限界に近づくと、要求が失敗する場合があります。
* スケーリング - 既存のクラウド サービスに新しいインスタンスを追加する場合は、同じクラスターに割り当てる必要があります。  通常、小規模のスケーリング要求は割り当てることができますが、常に割り当てることができるわけではありません。 クラスターが限界に近づくと、要求が失敗する場合があります。
* アフィニティ グループ - 空のクラウド サービスに新しくデプロイする場合は、クラウド サービスがアフィニティ グループに固定されている場合を除き、そのリージョン内の任意のクラスターのファブリックによって割り当てることができます。 同じアフィニティ グループへのデプロイは、同じクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。
* アフィニティ グループ vNet - 以前の Virtual Network はリージョンではなくアフィニティ グループに関連付けられていたため、これらの仮想ネットワーク内のクラウド サービスはアフィニティ グループのクラスターに固定されました。 このような仮想ネットワークへのデプロイは、固定されたクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。

## <a name="solutions"></a>解決方法
1. 新しいクラウド サービスに再デプロイする - この解決方法ではそのリージョン内のすべてのクラスターからプラットフォームを選択できるため、成功する可能性が最も高くなります。

   * 新しいクラウド サービスにワークロードをデプロイします  
   * 新しいクラウド サービスへのトラフィックを指すように CNAME または A レコードを更新します
   * 以前のサイトに送信されるトラフィックが 0 個になると、以前のクラウド サービスを削除できます。 この解決方法ではダウンタイムは発生しません。
2. 運用スロットとステージング スロットの両方を削除する - この解決方法では、既存の DNS 名が維持されますが、アプリケーションでダウンタイムが発生します。

   * 既存のクラウド サービスの運用スロットとステージング スロットを削除し、クラウド サービスを空にします。
   * 次に、既存のクラウド サービスに新しいデプロイを作成します。 これにより、リージョン内のすべてのクラスターで割り当てが再度試行されます。 クラウド サービスがアフィニティ グループに関連付けられていないことを確認します。
3. 予約済み IP - この解決方法では、既存の IP アドレスが維持されますが、アプリケーションでダウンタイムが発生します。  

   * Powershell を使用して既存のデプロイ用の ReservedIP を作成します

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * 上の 2. の手順に従って、サービスの CSCFG で新しい ReservedIP を確実に指定します。
4. 新しいデプロイのアフィニティ グループを削除する - アフィニティ グループは推奨されなくなりました。 上の 1. の手順に従って、新しいクラウド サービスをデプロイしてください。 クラウド サービスがアフィニティ グループ内にないことを確認します。
5. リージョン Virtual Network に変換する - 「 [アフィニティ グループから、リージョン Virtual Network (VNet) に移行する方法](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)」を参照してください。
