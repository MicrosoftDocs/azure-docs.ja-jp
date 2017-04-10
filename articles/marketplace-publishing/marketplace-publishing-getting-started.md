---
title: "プランを作成して Marketplace にデプロイする方法の概要 |Microsoft Docs"
description: "承認済みの Microsoft 開発者になり、仮想マシン イメージ、テンプレート、データ サービス、または開発者向けサービスを作成して Azure Marketplace にデプロイするために必要な手順を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: b76185c0a4a0e17b663affee9a02b65f222fedeb
ms.openlocfilehash: d679096476406831c1fda4f695adff84e63d6ae8
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Azure Marketplace でプランを発行して管理する方法
この記事では、Azure Marketplace に掲載されるソリューションを開発者が作成、デプロイ、管理し、Azure の他の顧客やパートナーに有償で提供するための方法を紹介しています。

## <a name="what-is-the-azure-marketplace"></a>Azure Marketplace とは何ですか?
Azure の発行元にとって、Azure Marketplace は、クラウド ベースのアプリケーションやモバイル ソリューションを短期間で開発しようとしている他の開発者、ISV、および IT プロフェッショナルに革新的なソリューションやサービスを配布し販売するしくみです。 ソリューションがビジネス ユーザーを対象としている場合は、[AppSource](http://appsource.microsoft.com) Marketplace を検討することをお勧めします。


## <a name="supported-types-of-solutions"></a>サポートされるソリューションの種類
まず、発行元は、会社がどのような種類のソリューションを提供するかを定義します。 Azure Marketplace では、次の種類のプランを提供しています。

|ソリューションの種類|仮想マシン|ソリューション テンプレート|
|---|---|---|
|定義|完全にインストールされたオペレーティング システムと&1; つ以上のアプリケーションを含む事前に構成されたイメージ。 Azure Virtual Machines サービスで仮想マシンの作成やデプロイに必要な情報は、仮想マシン イメージにより提供されます。|個別に存在するさまざまな Azure サービス (他の販売者から提供されているサービスを含む) を参照できるデータの構造。Azure の利用者は、このテンプレートを通じて、そのサービスやデータを単一の体系的な方法でデプロイすることができます。|
|例|**たとえば**、Azure の発行元として、革新的なデータベース サービスを有する VM を作成して検証します。この VM は、Azure の他の利用者が進んで調達して独自のクラウド サービス環境にデプロイするほど魅力的なものです。|**たとえば**、Azure の発行元として、一連のサービスを Azure 全体からバンドルします。このサービスによって、負荷分散、セキュリティ強化、および高可用性が実現されたクラウド サービスをすばやくデプロイできます。 Azure の他のサブスクライバーは、同じまたは類似した Azure サービスを手動で特定、調達、デプロイ、および構成するのではなく、目的に応じたソリューション テンプレートを調達することで、時間を節約できます。|

> [!NOTE]
> 手順の中には、異なる種類のソリューションで共通して使用されるものや、ソリューション固有のものがあることに注意してください。 この記事では、ソリューションの種類ごとに必要な手順の概要を示します。

## <a name="how-to-publish-a-solution"></a>ソリューションの発行方法
![図](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1.事前承認のためにソリューションをノミネートする
- [こちら](https://createopportunity.azurewebsites.net)から、**Microsoft Azure Certified for Virtual Machines** のソリューション指定フォームに必要事項を入力してください

>[!NOTE]
> パートナー アカウント マネージャーまたは DX パートナー マネージャーと協力している場合は、お客様のソリューションを Azure 認定プログラムにノミネートするように依頼してください。または、[Microsoft Azure 認定](http://createopportunity.azurewebsites.net) Web ページに移動し、申し込みフォームに入力したうえで、[Microsoft Sponsor Contact (Microsoft スポンサーの取引先担当者)] フィールドに、パートナー アカウント マネージャーまたは DX パートナー マネージャーの電子メールを入力します。

[Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)の資格基準を満たしている場合、アプリケーションが承認された際には、マイクロソフトがお客様のソリューションを Azure Marketplace にオンボードするお手伝いをします。

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2.Microsoft 販売者としてアカウントを登録する
- [Microsoft 開発者アカウントとして Microsoft アカウントを登録する](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>手順&3;.ソリューションを発行する
1. 技術面以外の前提条件を満たす
  - [技術面以外の前提条件を満たす](marketplace-publishing-pre-requisites.md)
  - [VM の技術的な前提条件](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md)
2. プランを作成する
  - [仮想マシン](marketplace-publishing-vm-image-creation.md)
  - [ソリューション テンプレート](marketplace-publishing-solution-template-creation.md)
3. [プランのマーケティング コンテンツの作成](marketplace-publishing-push-to-staging.md)
4. ステージング環境でプランをテストする
  - [ステージング環境での VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)
  - [ステージングでのソリューション テンプレート プランのテスト](marketplace-publishing-solution-template-test-in-staging.md)
5. [Azure Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>仮想マシン イメージ固有
* [Creating a VM Image on-premise (オンプレミスでの VM イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)
* [Windows を実行する仮想マシンを Azure ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux を実行する仮想マシンを Azure ポータルを使用して作成する](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Troubleshooting common issues encountered during VHD creation (VHD の作成中に発生した一般的な問題をトラブルシューティングする)](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>ソリューションの管理方法
* [仮想マシン プランの作成後ガイド](marketplace-publishing-vm-image-post-publishing.md)
* [プランまたは SKU の非技術的な詳細を更新する方法](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [プランまたは SKU の技術的な詳細を更新する方法](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [表示されているプランに新しい SKU を追加する方法](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [表示されている SKU のデータ ディスク数を変更する方法](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [表示されているプランを Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md)
* [表示されている SKU を Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [表示されている SKU の現在のバージョンを Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [表示価格を運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [課金モデルを運用時の運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [表示されている SKU の可視性の設定を運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [How to change your Cloud Solution Provider reseller incentive (Cloud Solution Provider のリセラー インセンティブを変更する方法)](marketplace-publishing-csp-incentive.md)
* [Seller Insights レポートの概要](marketplace-publishing-report-seller-insights.md)
* [支払いレポートを理解する](marketplace-publishing-report-payout.md)
* [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>その他のリソース
* [Azure PowerShell の設定](marketplace-publishing-powershell-setup.md)

