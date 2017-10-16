---
title: "Azure Stack でのお客様への請求と配賦 | Microsoft Docs"
description: "Azure Stack からリソース使用量情報を取得する方法を説明します。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack での使用量と請求

使用量とは、ユーザーによって使われたリソースの量のことです。 Azure Stack では、ユーザーごとに使用量情報が収集され、その情報を使ってユーザーへの請求が行われます。 この記事では、Azure Stack ユーザーにどのようにリソース使用量が請求されるかと、分析や配賦などのために請求情報にアクセスする方法を説明します。

Azure Stack には、すべてのリソースの使用量データを収集および集計し、そのデータを Azure Commerce に転送するためのインフラストラクチャがあります。 このデータにアクセスして、請求アダプターを使って請求システムにエクスポートすることや、Microsoft Power BI などのビジネス インテリジェンス ツールにエクスポートすることができます。 この請求情報は、エクスポートされた後、分析に使われるか、配賦システムに転送されます。

![Azure Stack を請求アプリケーションに接続する請求アダプターの概念モデル](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>使用量パイプライン

Azure Stack 内の各リソース プロバイダーは、リソース使用率に従って使用量データを出力します。 Usage Service は、この使用量データを定期的に (時間単位または日単位で) 集計し、使用量データベースに保存します。 Azure Stack オペレーターおよびユーザーは、保存された使用量データに使用量 API を使用してローカルでアクセスできます。 

[Azure Stack インスタンスを Azure に登録](azure-stack-register.md)している場合は、Azure Commerce に使用量データを送信するように使用量ブリッジが構成されます。 データが Azure で使用可能になったら、Billing Portal または Azure 使用量 API を使用してアクセスできます。 Azure に報告される使用量データの詳細については、[使用量データのレポート](azure-stack-usage-reporting.md)に関する記事をご覧ください。 

次の図は、使用量パイプラインの主要なコンポーネントを示しています。

![使用量パイプライン](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>確認できる使用量情報とその確認方法

計算、ストレージ、ネットワークなど、Azure Stack のリソースプロバイダーでは、サブスクリプションごとに 1 時間間隔で使用量データが生成されます。 使用量データには、リソース名、使われたサブスクリプション、使われた量など、使われたリソースに関する情報が含まれます。測定 ID リソースについて知るには、[使用量 API に関するよくあるご質問](azure-stack-usage-related-faq.md)の記事をご覧ください。 

使用量データが収集されると、そのデータが [Azure に報告され](azure-stack-usage-reporting.md)、請求書が生成されます。請求書は、Azure Billing Portal から表示できます。 

> [!NOTE]
> 使用量データのレポートは、Azure Stack Development Kit および容量モデルのライセンスを持つ Azure Stack 統合システムのユーザーにとっては必須ではありません。 Azure Stack のライセンスの詳細については、[パッケージと価格](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)に関するデータ シートをご覧ください。

Azure Billing Portal では、課金対象のリソースについてのみ使用量データが表示されます。 Azure Stack では、課金対象のリソースのほかに、幅広い一連のリソースについて使用量情報が取得されます。この情報には、Azure Stack 環境で REST API または PowerShell を使ってアクセスできます。 ユーザーが自分の使用量の詳細のみを取得できるのに対し、Azure Stack オペレーターはすべてのユーザー サブスクリプションについて使用量データを取得できます。

## <a name="retrieve-usage-information"></a>使用量情報を取得する

使用量データを生成するには、実行されていて、システムをアクティブに使っているリソース (アクティブな仮想マシンや、データを格納しているストレージ アカウントなど) が必要です。Azure Stack Marketplace で実行されているリソースがあるかどうか不明な場合は、仮想マシン (VM) をデプロイし、実行されているかどうか仮想マシン監視ブレードを確認します。 使用量データを表示するには、次の PowerShell コマンドレットを使います。

1. [PowerShell for Azure Stack をインストールします。](azure-stack-powershell-install.md)
2. [Azure Stack ユーザー](user/azure-stack-powershell-configure-user.md)または [Azure Stack オペレーター](azure-stack-powershell-configure-admin.md)の PowerShell 環境を構成します 

3. 使用量データを取得するには、[Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell コマンドレットを使います。

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>次のステップ

[Azure Stack の使用量データを Azure に報告する](azure-stack-usage-reporting.md)

[プロバイダーリソース使用量 API](azure-stack-provider-resource-api.md)

[テナント リソース使用量 API](azure-stack-tenant-resource-usage-api.md)

[使用量に関するよくあるご質問](azure-stack-usage-related-faq.md)

