---
title: Microsoft Azure Cloud Services のデプロイの問題についてよくあるご質問 | Microsoft Docs
description: この記事では、Microsoft Azure Cloud Services のデプロイについてよくあるご質問を紹介します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896479"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Azure Cloud Services (クラシック) のデプロイの問題: よく寄せられる質問 (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。
この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) のデプロイの問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>運用スロットに既存のデプロイが既に存在する場合に、ステージング スロットにクラウド サービスをデプロイすると、リソースの割り当てエラーが原因でデプロイが失敗することがあるのはなぜですか。
どちらかのスロットに既にクラウド サービスのデプロイがあると、クラウド サービス全体が特定のクラスターに固定されます。 したがって、運用スロットにデプロイが既に存在する場合、新しいステージング環境のデプロイは、運用スロットと同じクラスターにしか割り当てることができません。

クラウド サービスが存在するクラスターに、デプロイ要求を満たすための物理的なコンピューティング リソースが不足していると、割り当てエラーが発生します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーに関するページの「解決方法」](cloud-services-allocation-failures.md#solutions)を参照してください。

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>クラウド サービスのデプロイのスケールアップまたはスケールアウトを実行すると割り当てエラーが発生することがあるのはなぜですか。
クラウド サービスをデプロイすると、通常、そのクラウド サービスは特定のクラスターに固定されます。 したがって、スケールアップ/アウトを実行する場合、既存のクラウド サービスは新しいインスタンスを同じクラスター内に割り当てる必要があります。 クラスターの容量が満杯に近づいている場合や、必要なサイズ/種類の VM を利用できない場合、要求は失敗します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーに関するページの「解決方法」](cloud-services-allocation-failures.md#solutions)を参照してください。

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>クラウド サービスをアフィニティ グループにデプロイすると割り当てエラーが発生することがあるのはなぜですか。
空のクラウド サービスに対する新しいデプロイは、そのクラウド サービスがアフィニティ グループに固定されている場合を除き、Microsoft Azure のファブリックによって、そのリージョン内の任意のクラスターに割り当てられます。 同じアフィニティ グループへのデプロイは、同じクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーに関するページの「解決方法」](cloud-services-allocation-failures.md#solutions)を参照してください。

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>VM サイズを変更したり、既存のクラウド サービスに新しい VM を追加したりすると割り当てエラーが発生することがあるのなぜですか。
データセンター内のクラスターには、マシンの種類が異なるさまざまな構成があります (A シリーズ、Av2 シリーズ、D シリーズ、Dv2 シリーズ、G シリーズ、H シリーズなど)。 ただし、すべてのクラスターにすべての種類の VM が含まれているとは限りません。 たとえば、A シリーズ専用クラスターに既にデプロイされているクラウド サービスに D シリーズ VM を追加しようとすると、割り当てエラーが発生します。 これは、VM の SKU サイズを変更しようとする (たとえば、A シリーズから D シリーズに切り替える) 場合にも発生します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーに関するページの「解決方法」](cloud-services-allocation-failures.md#solutions)を参照してください。

お住まいの地域で使用できるサイズについては、[Microsoft Azure のリージョン別の利用可能な製品](https://azure.microsoft.com/regions/services)に関するページを参照してください。

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>サブスクリプションまたはサービスの制限/クォータ/制約が原因でクラウド サービスのデプロイが失敗することがあるのはなぜですか。
クラウド サービスのデプロイは、割り振る必要のあるリソースが、リージョン/データセンター レベルでサービスに提供できる既定または最大のクォータを超えた場合に、失敗することがあります。 詳細については、「[Cloud Services の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits)」を参照してください。

ポータルでサブスクリプションの現在の使用量/クォータを追跡することもできます:Azure portal  => [サブスクリプション]  => \<appropriate subscription> => [Usage + quota]\(使用量 + クォータ\)。

リソース使用量/消費量に関連する情報は、Azure 課金 API を使用して取得することもできます。 「[Azure Consumption API の概要](../cost-management-billing/manage/consumption-api-overview.md)」を参照してください。

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>デプロイしたクラウド サービス VM のサイズを、再デプロイせずに変更するにはどうしたらよいですか。
デプロイしたクラウド サービスの VM サイズを、再デプロイせずに変更することはできません。 VM サイズは CSDEF に組み込まれており、これを更新するには再デプロイが必要です。

詳細については、「[クラウド サービスの更新方法](cloud-services-update-azure-service.md)」を参照してください。

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Service Management API または PowerShell から Azure Resource Manager ストレージ アカウントを使用して Cloud Services をデプロイすることができません。なぜでしょうか。 

Cloud Services はクラシック リソースです。クラシック リソースは Azure Resource Manager モデルと直接の互換性がないため、Azure Resource Manager のストレージ アカウントに関連付けることができません。 いくつかの選択肢を次に示します。 

- REST API を使用してデプロイする。

    Service Management REST API を使用してデプロイすれば、クラシックと Azure Resource Manager の両方のストレージ アカウントに対応した Blob Storage への SAS URL を指定することでこの制限を回避できます。 "PackageUrl" プロパティの詳細については、[こちら](/previous-versions/azure/reference/ee460813(v=azure.100))を参照してください。

- [Azure Portal](https://portal.azure.com) を使用してデプロイする。

    [Azure Portal](https://portal.azure.com) からデプロイすれば、Azure Resource Manager リソースとクラシック リソースとの間の通信を可能にするプロキシ/shim を経由して呼び出しが行われるため正常に実行できます。 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Azure Portal がデプロイ用のストレージ アカウントの提供を要求するのはなぜですか。

クラシック ポータルでは、パッケージは管理 API レイヤーに直接アップロードされてから、API レイヤーがパッケージを内部ストレージ アカウントに一時的に格納しました。  API レイヤーはファイル アップロード サービスとして設計されていないため、このプロセスはパフォーマンスとスケーラビリティの問題の原因となります。  Azure Portal (Resource Manager デプロイ モデル) では、 API レイヤーへの最初のアップロードの中間手順をバイパスしているため、デプロイの速度と信頼性が向上しています。

コストはとても低く、すべてのデプロイに同じストレージ アカウントを再利用できます。 [ストレージのコスト計算ツール](https://azure.microsoft.com/pricing/calculator/#storage1)を使用して、サービス パッケージ (CSPKG) のアップロード、CSPKG のダウンロード、CSPKG の削除にかかるコストを判断できます。
