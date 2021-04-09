---
title: 体験版とは Microsoft コマーシャル マーケットプレース
description: Marketplace 体験版機能の説明
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96490042"
---
# <a name="what-is-a-test-drive"></a>体験版とは

体験版は、購入前に試用するオプションを提供することで潜在顧客へのオファーを披露します。これにより、見込みの高い潜在顧客が生成され、コンバージョンが増加する結果になります。 体験版によって、実際の実装シナリオで製品を活用できます。 製品を試用しているお客様は、同様のソリューションを購入する明確な意図があることを示しています。 これを活用して、より見込みがある潜在顧客をフォローできます。

お客様は、体験版の恩恵も受けられます。 最初に製品を試してみることで、購入プロセスでの摩擦を減らすことができます。 さらに、体験版は事前にプロビジョニングされています。つまり、お客様が製品をダウンロード、設定、または構成する必要がありません。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

体験版は、ソリューションまたはアプリケーションを要求するお客様に対し、オンデマンドでデプロイするマネージド インスタンスです。 体験版インスタンスが割り当てられると、そのお客様は、一定の期間中使用できるようになります。 期間が終了すると、別のお客様用のスペースを作成するために削除されます。

発行者は、パートナー センターで体験版の設定を管理および構成します。 技術的な構成の詳細は、オファーの種類によって異なります。 詳細なガイダンスについては、「[体験版の技術的な構成](./test-drive-technical-configuration.md)」を参照してください。

お客様は、[AppSource](https://appsource.microsoft.com/en-US/) でオファーの CTA として体験版をご利用いただけます。 連絡先情報を入力し、オファーの使用条件とプライバシー ポリシーに同意してから、事前に構成された環境へのアクセスを取得して、一定期間、それを試すことができます。 顧客は、製品の主要な機能と利点に関するセルフガイドによる実践的な試用版を受け取り、発行者は貴重な潜在顧客を得ることができます。

## <a name="types-of-test-drives"></a>体験版の種類

コマーシャル マーケットプレースでは、製品の種類、シナリオ、およびマーケットプレースに応じて、さまざまな体験版が提供されています。

- Azure Resource Manager
    - Azure アプリケーション
    - SaaS
    - Virtual Machines
- ホストされた体験版
    - Dynamics 365 for Business Central (現在サポートされていません)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- ロジック アプリ (サポート モードのみ)
- Power BI

これらのいずれかの体験版を構成する方法の詳細については、「[体験版の技術的な構成](./test-drive-technical-configuration.md)」を参照してください。 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版

このデプロイ テンプレートには、ソリューションを構成しているすべての Azure リソースが含まれています。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 Azure Resource Manager の体験版は、次の種類のオファーで利用できます。 

- Azure アプリケーション
- SaaS
- 仮想マシン

>[!NOTE]
>これは、仮想マシンと Azure アプリケーションのオファーで唯一の体験版オプションです。

### <a name="hosted-test-drive-recommended"></a>ホストされた体験版 (推奨)

ホストされた体験版では、体験版のユーザー プロビジョニングおよびプロビジョニング解除を実行するサービスを Microsoft がホストして管理するので、セットアップの複雑さが解消されます。 Microsoft AppSource にオファーがある場合は、体験版を構築して Dynamics AX/CRM インスタンスに接続します。 次の AppSource オファーの種類を使用できます。

- 販売、サービス、プロジェクト サービス、フィールド サービスなどの Customer Engagement システムでは、[Dynamics 365 for Customer Engagement](partner-center-portal/create-new-customer-engagement-offer.md) を使用します。
- 財務、運用、製造、サプライ チェーンなどの Finance and Operations エンタープライズ リソース計画システムでは、[Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) を使用します。

### <a name="logic-app-test-drive"></a>ロジック アプリの体験版

この種類の体験版は、Microsoft によってホストされていないため、Dynamics AX/CRM オファーの種類には Azure Resource Manager (ARM) テンプレートを使用します。 ARM テンプレートを実行して、Azure サブスクリプションで必要なリソースを作成する必要があります。 ロジック アプリの体験版は、現在サポート モードになっており、Microsoft では推奨されていません。ロジック アプリの体験版を構成する方法の詳細については、「[体験版の技術的な構成](./test-drive-technical-configuration.md)」を参照してください。

### <a name="power-bi-test-drive"></a>Power BI の体験版

これは、カスタムビルドされたダッシュボードへの単なる埋め込みリンクです。 インタラクティブな Power BI の視覚化の単なるデモンストレーションを行う製品には、この種類の体験版を使用する必要があります。

## <a name="transforming-examples"></a>変換の例

リソースのアーキテクチャを体験版に変換するプロセスは難しい場合があります。 現在のアーキテクチャを最適に変換する方法については、こちらの例をご覧ください。

[Web サイト テンプレートを体験版に変換する](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[仮想マシン テンプレートを体験版に変換する](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[既存の Resource Manager テンプレートを体験版に変換する](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>体験版から潜在顧客を生み出す

コマーシャル マーケットプレースの体験版は、マーケティング担当にとって最適なツールです。 ビジネスでより多くの潜在顧客を生み出すために、市場進出プロセスに体験版を組み込むことをお勧めします。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

体験版での潜在顧客との取引を終了する前に、[Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) に忘れずに登録します。 また、体験版が役に立って顧客を獲得した話をお聞かせください。

## <a name="other-resources"></a>その他のリソース

体験版に関するその他のリソース:

- [体験版のベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF、ポップアップ ブロックがオフになっていることを確認してください)

## <a name="next-step"></a>次のステップ

- [体験版の技術的な構成](test-drive-technical-configuration.md)