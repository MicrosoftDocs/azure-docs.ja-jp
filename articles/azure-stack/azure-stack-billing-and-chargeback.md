---
title: Azure Stack でのお客様への請求と配賦 | Microsoft Docs
description: Azure Stack からリソース使用量情報を取得する方法を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337852"
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack での使用量と請求

この記事では、Azure Stack ユーザーにどのようにリソース使用量が請求されるかと、分析および配賦のために請求情報にアクセスする方法を説明します。

Azure Stack では、使用されるリソースの使用量データを収集し、グループ化します。 Azure Stack は、このデータを Azure Commerce に転送します。 Azure Commerce では、Azure の使用量に対する課金と同様に、Azure Stack の使用量に対して課金が行われます。

また、請求アダプターを使用して、使用量データを取得し、独自の請求システムや配賦システムにエクスポートしたり、Microsoft Power BI などのビジネス インテリジェンス ツールにエクスポートできます。

## <a name="usage-pipeline"></a>使用量パイプライン

Azure Stack 内の各リソース プロバイダーは、リソース使用率に従って使用量データを投稿します。 使用状況サービスにより、使用量データが定期的に (時間単位および日単位で) 集計され、使用量データベースに保存されます。 Azure Stack のオペレーターとユーザーは、Azure Stack リソース使用量 API を介して、保存されている使用量データにアクセスできます。

[Azure Stack インスタンスを Azure に登録](azure-stack-register.md)している場合、Azure Commerce に使用量データを送信するように Azure Stack が構成されます。 データが Azure にアップロードされたら、課金ポータルまたは Azure リソース使用量 API を使用してデータにアクセスできます。 Azure にレポートされる使用量データの詳細については、[使用量データのレポート](azure-stack-usage-reporting.md)に関する記事をご覧ください。  

次の図は、使用量パイプラインの主要なコンポーネントを示しています。

![使用量パイプライン](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>確認できる使用量情報とその確認方法

コンピューティング、ストレージ、ネットワークなど、Azure Stack のリソース プロバイダーでは、サブスクリプションごとに 1 時間間隔で使用量データが生成されます。 使用量データには、リソース名、使用されたサブスクリプション、使用された量など、使用されたリソースに関する情報が含まれます。 測定 ID リソースについては、[Usage API の FAQ](azure-stack-usage-related-faq.md) に関するページをご覧ください。

使用量データが収集されると、そのデータが [Azure に報告され](azure-stack-usage-reporting.md)、請求書が生成されます。請求書は、Azure Billing Portal から表示できます。

> [!NOTE]  
> 使用量データのレポートは、Azure Stack Development Kit (ASDK) および容量モデルのライセンスを持つ Azure Stack 統合システムのユーザーにとっては必須ではありません。 Azure Stack のライセンスの詳細については、[パッケージと価格に関するデータ シート](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)をご覧ください。

Azure 課金ポータルには、課金対象のリソースの使用量データが表示されます。 Azure Stack では、課金対象のリソースに加え、幅広い一連のリソースについて使用量情報が取得されます。この情報には、Azure Stack 環境で REST API または PowerShell コマンドレットを使ってアクセスできます。 Azure Stack のオペレーターは、すべてのユーザー サブスクリプションの使用量データを取得できます。 個々のユーザーは、自分自身の使用量の詳細のみを取得できます。

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>マルチテナント クラウド サービス プロバイダーの使用状況レポート

Azure Stack を使用している多数の顧客を持つマルチテナント クラウド サービス プロバイダー (CSP) は、各顧客の使用状況を個別に報告できます。これにより、プロバイダーはさまざまな Azure サブスクリプションに使用料金を課金できます。

各顧客の ID は、異なる Azure Active Directory (Azure AD) テナントによって表されます。 Azure Stack では、Azure AD テナントごとに 1 つの CSP サブスクリプションを割り当てることができます。 テナントとそのサブスクリプションを Azure Stack の基本登録に追加できます。 基本登録はすべての Azure Stack インスタンスに対して行われます。 サブスクリプションがテナントに登録されていなくても、ユーザーは引き続き Azure Stack を使用することができ、使用状況は基本登録に使用されたサブスクリプションに送信されます。

## <a name="next-steps"></a>次の手順

- [Azure Stack を登録する](azure-stack-registration.md)
- [Azure Stack の使用量データを Azure に報告する](azure-stack-usage-reporting.md)
- [プロバイダーリソース使用量 API](azure-stack-provider-resource-api.md)
- [テナント リソース使用量 API](azure-stack-tenant-resource-usage-api.md)
- [使用量に関するよくあるご質問](azure-stack-usage-related-faq.md)