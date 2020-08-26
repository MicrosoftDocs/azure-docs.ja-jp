---
title: 体験版とは Microsoft コマーシャル マーケットプレース
description: Marketplace 体験版機能の説明
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: bd3b77f80a414dd3db1d5106929fa0e215e34c1f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121869"
---
# <a name="what-is-a-test-drive"></a>体験版とは

体験版は、*購入前に試用する*オプションを提供することで潜在顧客へのオファーを披露し、その結果、コンバージョンが増加し、見込みの高い潜在顧客が生成される優れた方法です。 体験版によって、実世界の実装シナリオで製品を活用しながら、見込みの高い潜在顧客を生成できます。

体験版は、ソリューションまたはアプリケーションを要求する顧客に対してオンデマンドでデプロイするマネージド インスタンスです。 体験版インスタンスが割り当てられると、設定された分量の時間使用でき、その後、別の顧客のための余地を作るために削除されます。

発行者は、パートナー センターで体験版の設定を管理および構成します。 技術的な構成の詳細は、使用しているオファーの種類によって異なります。 詳細なガイダンスについては、このトピックの最後にある「[次の手順](#next-step)」のリンクを参照してください。

潜在顧客は、コマーシャル マーケットプレースで体験版を利用できます。 連絡先情報を入力し、オファーの使用条件とプライバシー ポリシーに同意してから、事前に構成された環境にアクセスして、一定期間、それを試すことができます。 顧客は、製品の主要な機能と利点に関するセルフガイドによる実践的な試用版を受け取り、発行者は貴重な潜在顧客を得ることができます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

発行者は、パートナー センター内で体験版の設定を管理および構成します。 セットアップが完了すると、要求する顧客に対してオンデマンドでデプロイされるマネージド インスタンスになります。 体験版インスタンスが割り当てられると、設定された分量の時間使用でき、その後、別の顧客のための余地を作るために削除されます。

## <a name="types-of-test-drives"></a>体験版の種類

コマーシャル マーケットプレースでは、製品の種類、シナリオ、およびマーケットプレースに応じて、さまざまな体験版が提供されています。

- Azure Resource Manager
- ホストされた体験版
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- ロジック アプリ
- Power BI

これらのいずれかの体験版を構成する方法の詳細については、このトピックの最後にある「[次の手順](#next-step)」のリンクを参照してください。

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版

このデプロイ テンプレートには、ソリューションを構成しているすべての Azure リソースが含まれています。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 Azure Resource Manager の体験版は、次の種類のオファーで利用できます。 

- Azure アプリケーション
- Dynamics 365 for Business Central
- Dynamics 365 for Customer Engagement および PowerApps
- Dynamics 365 for Operations
- SaaS
- 仮想マシン

>[!NOTE]
>これは、仮想マシンと Azure アプリケーションのオファーで唯一の体験版オプションです。

### <a name="hosted-test-drive"></a>ホストされた体験版

ホストされた体験版では、体験版のユーザー プロビジョニング、デプロイ、およびプロビジョニング解除を実行するサービスを Microsoft がホストして管理するので、セットアップの複雑さが解消されます。 Microsoft AppSource にオファーがある場合は、Dynamics AX/CRM インスタンス、または Azure 以外の他のリソースと接続する体験版をビルドします。 AppSource オファーを次の Dynamics 365 オファーと接続するには、この種類の体験版を使用します。

- 財務、運用、サプライ チェーン、CRM などの Business Central エンタープライズ リソース計画システムでは、[Dynamics 365 for Business Central](partner-center-portal/create-new-operations-offer.md) を使用します。
- 販売、サービス、プロジェクト サービス、フィールド サービスなどの Customer Engagement システムでは、[Dynamics 365 for Customer Engagement](partner-center-portal/create-new-customer-engagement-offer.md) を使用します。
- 財務、運用、製造、サプライ チェーンなどの Finance and Operations エンタープライズ リソース計画システムでは、[Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) を使用します。

### <a name="logic-app-test-drive"></a>ロジック アプリの体験版

この種類の体験版は、Microsoft によってホストされていません。 Dynamics 365 オファーまたはその他のカスタム リソースと接続するには、この種類の体験版を使用します。

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

- [技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF、ポップアップ ブロックがオフになっていることを確認してください)

## <a name="next-step"></a>次のステップ

- [体験版の技術的な構成](test-drive-technical-configuration.md)
