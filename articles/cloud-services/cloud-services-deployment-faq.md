---
title: "Microsoft Azure Cloud Services のデプロイの問題についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Microsoft Azure Cloud Services のデプロイについてよくあるご質問を紹介します。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 9b788b1d95c821a4bb76cd4dea1d689d36e2f92b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services のデプロイの問題: よくあるご質問 (FAQ)

この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) のデプロイの問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>運用スロットに既存のデプロイが既に存在する場合に、ステージング スロットにクラウド サービスをデプロイすると、リソースの割り当てエラーが原因でデプロイが失敗することがあるのはなぜですか。
どちらかのスロットに既にクラウド サービスのデプロイがあると、クラウド サービス全体が特定のクラスターに固定されます。 したがって、運用スロットにデプロイが既に存在する場合、新しいステージング環境のデプロイは、運用スロットと同じクラスターにしか割り当てることができません。

クラウド サービスが存在するクラスターに、デプロイ要求を満たすための物理的なコンピューティング リソースが不足していると、割り当てエラーが発生します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーの解決方法](cloud-services-allocation-failures.md#solutions)に関するページを参照してください。

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>クラウド サービスのデプロイのスケールアップまたはスケールアウトを実行すると割り当てエラーが発生することがあるのはなぜですか。
クラウド サービスをデプロイすると、通常、そのクラウド サービスは特定のクラスターに固定されます。 したがって、スケールアップ/アウトを実行する場合、既存のクラウド サービスは新しいインスタンスを同じクラスター内に割り当てる必要があります。 クラスターの容量が満杯に近づいている場合や、必要なサイズ/種類の VM を利用できない場合、要求は失敗します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーの解決方法](cloud-services-allocation-failures.md#solutions)に関するページを参照してください。

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>クラウド サービスをアフィニティ グループにデプロイすると割り当てエラーが発生することがあるのはなぜですか。
空のクラウド サービスに対する新しいデプロイは、そのクラウド サービスがアフィニティ グループに固定されている場合を除き、Microsoft Azure のファブリックによって、そのリージョン内の任意のクラスターに割り当てられます。 同じアフィニティ グループへのデプロイは、同じクラスターで試行されます。 クラスターが限界に近づくと、要求が失敗する場合があります。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーの解決方法](cloud-services-allocation-failures.md#solutions)に関するページを参照してください。

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>VM サイズを変更したり、既存のクラウド サービスに新しい VM を追加したりすると割り当てエラーが発生することがあるのなぜですか。
データセンター内のクラスターには、マシンの種類が異なるさまざまな構成があります (A シリーズ、Av2 シリーズ、D シリーズ、Dv2 シリーズ、G シリーズ、H シリーズなど)。 ただし、すべてのクラスターにすべての種類の VM が含まれているとは限りません。 たとえば、A シリーズ専用クラスターに既にデプロイされているクラウド サービスに D シリーズ VM を追加しようとすると、割り当てエラーが発生します。 これは、VM の SKU サイズを変更しようとする (たとえば、A シリーズから D シリーズに切り替える) 場合にも発生します。

このような割り当てエラーを軽減する方法については、[クラウド サービスの割り当てエラーの解決方法](cloud-services-allocation-failures.md#solutions)に関するページを参照してください。

お住まいの地域で使用できるサイズについては、「[Microsoft Azure: リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services)」を参照してください。

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>サブスクリプションまたはサービスの制限/クォータ/制約が原因でクラウド サービスのデプロイが失敗することがあるのはなぜですか。
クラウド サービスのデプロイは、割り振る必要のあるリソースが、リージョン/データセンター レベルでサービスに提供できる既定または最大のクォータを超えた場合に、失敗することがあります。 詳細については、「[Cloud Services の制限](../azure-subscription-service-limits.md#cloud-services-limits)」を参照してください。

ポータルでサブスクリプションの現在の使用量/クォータを追跡することもできます。Azure Portal => サブスクリプション = \<該当するサブスクリプション> => "使用量 + クォータ" とお進みください。

リソース使用量/消費量に関連する情報は、Azure 課金 API を使用して取得することもできます。 「[Azure Resource Usage API (プレビュー)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview)」を参照してください。

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>デプロイしたクラウド サービス VM のサイズを、再デプロイせずに変更するにはどうしたらよいですか。
デプロイしたクラウド サービスの VM サイズを、再デプロイせずに変更することはできません。 VM サイズは CSDEF に組み込まれており、これを更新するには再デプロイが必要です。

詳細については、「[クラウド サービスの更新方法](cloud-services-update-azure-service.md)」を参照してください。

 

