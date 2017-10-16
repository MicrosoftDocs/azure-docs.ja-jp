---
title: "プランを作成して Marketplace にデプロイする方法の概要 |Microsoft Docs"
description: "Microsoft 認定開発者として、仮想マシン イメージ、テンプレート、データ サービス、または開発者向けサービスを作成して Azure Marketplace にデプロイするために必要な手順を理解します"
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
ms.openlocfilehash: 8fbf201343f6710d2781a4b56ae54833ed4c06cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Azure Marketplace にプランを発行して管理する
この記事では、Azure Marketplace に掲載されるソリューションを開発者が作成、デプロイ、管理し、Azure の他の顧客やパートナーに有償で提供するための方法を紹介しています。

## <a name="marketplace-publishing"></a>Marketplace への発行
開発した革新的なソリューションやサービスを Azure の発行元として、Marketplace で他の開発者、ISV、および IT プロフェッショナルに配布および販売できます。 Marketplace を通して、クラウド ベースのアプリケーションやモバイル ソリューションを簡単に開発することを望んでいる顧客にアピールすることができます。 ソリューションのターゲットがビジネス ユーザーの場合は、[AppSource](http://appsource.microsoft.com) Marketplace を検討することをお勧めします。


## <a name="supported-types-of-solutions"></a>サポートされるソリューションの種類
発行元として最初に行うのは、どのような種類のソリューションを提供するかを定義することです。 Marketplace では、次の種類のプランをサポートしています。

|ソリューションの種類|仮想マシン|ソリューション テンプレート|
|---|---|---|
|**定義**|全体がインストールされるオペレーティング システムと 1 つ以上のアプリケーションを含む構成済みのイメージ。 Azure Virtual Machines サービスで仮想マシンの作成やデプロイに必要な情報は、仮想マシン イメージにより提供されます。|1 つ以上の個別の Azure サービスを参照できるデータ構造。他の販売者によって発行されたサービスも含みます。 Azure サブスクライバーは、これを使用して、1 つ以上のプランを単一の統合された方法でデプロイできます。|
|**例**|Azure の発行元は、革新的なデータベース サービスを提供する VM の作成と検証を行っています。 他の Azure サブスクライバーは、その VM を調達して自分のクラウド サービス環境にデプロイすることができます。|Azure の発行元は、Azure から一連のサービスをバンドルして、負荷分散、セキュリティ強化、および高可用性が実現されたクラウド サービスをすばやくデプロイできるようにしています。 他の Azure サブスクライバーは、各自の目的に適合するソリューション テンプレートを調達することで、時間を節約できます。 同一または類似する Azure サービスを手動で探して調達し、デプロイして構成する必要はありません。|

> [!NOTE]
> 手順の中には、異なる種類のソリューションで共通して使用されるものや、ソリューション固有のものがあります。 この記事では、ソリューションの種類ごとに必要な手順の概要を示します。

## <a name="publish-a-solution"></a>ソリューションを発行する
![ノミネート、登録、発行](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>事前承認のためにソリューションをノミネートする
仮想マシン [ソリューション](https://createopportunity.azurewebsites.net)を Marketplace に発行するには、Microsoft Azure Certified **ソリューション ノミネート フォーム**を完成させてください。

>[!NOTE]
> パートナー アカウント マネージャーまたは DX パートナー マネージャと協力している場合は、ソリューションを Azure Certified プログラムにノミネートするように依頼してください。 [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) Web ページに移動して申し込みフォームに入力することもできます。 パートナー アカウント マネージャーまたは DX パートナー マネージャーの電子メール アドレスを **[Microsoft Sponsor Contact]\(Microsoft スポンサーの取引先担当者\)** ボックスに入力してください。

[Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)の資格基準を満たしている場合、アプリケーションが承認された際には、マイクロソフトがお客様のソリューションを Azure Marketplace にオンボードするお手伝いをします。

### <a name="register-your-account-as-a-microsoft-seller"></a>Microsoft 販売者としてアカウントを登録する
Microsoft アカウントを [Microsoft 開発者アカウント](marketplace-publishing-accounts-creation-registration.md)として登録します。

### <a name="publish-your-solution"></a>ソリューションを発行する
ソリューションを Marketplace に公開するには、次の手順に従います。
1. 技術面以外の前提条件を満たします。

    a. [技術面以外の前提条件](marketplace-publishing-pre-requisites.md)を満たします。

    b. [VM の技術面の前提条件](marketplace-publishing-vm-image-creation-prerequisites.md)を満たします。

    c. [ソリューション テンプレートの技術面の前提条件](marketplace-publishing-solution-template-creation-prerequisites.md)を満たします。

2. プランを作成します。

    a. [仮想マシン](marketplace-publishing-vm-image-creation.md) プランを作成します。

    b. [ソリューション テンプレート](marketplace-publishing-solution-template-creation.md) プランを作成します。

3. プランの[マーケティング コンテンツ](marketplace-publishing-push-to-staging.md)を作成します。

4. ステージング環境でプランをテストします。

    a. VM プランを[ステージング環境](marketplace-publishing-vm-image-test-in-staging.md)でテストします。

    b. ソリューション テンプレート プランを[ステージング環境](marketplace-publishing-solution-template-test-in-staging.md)でテストします。

5. プランを [Marketplace](marketplace-publishing-push-to-production.md) にデプロイします。


### <a name="create-and-manage-a-virtual-machine-image"></a>仮想マシン イメージを作成して管理する
以下のリソースを使用して、VM イメージを作成して管理します。
* VM イメージを[オンプレミスで](marketplace-publishing-vm-image-creation-on-premise.md)作成します。
* [Windows の Azure ポータル](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)で実行される仮想マシンを作成します。
* [Linux の Azure ポータル](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)で実行される仮想マシンを作成します。
* [VHD の作成中](marketplace-publishing-vm-image-creation-troubleshooting.md)に発生する一般的な問題をトラブルシューティングします。

## <a name="manage-your-solution"></a>ソリューションを管理する
以下のリソースを参照してソリューションを管理します。
* [仮想マシン プランの作成後ガイドを読む](marketplace-publishing-vm-image-post-publishing.md)
* [プランまたは SKU の技術面以外の詳細を更新する](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [プランまたは SKU の技術面の詳細を更新する](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [表示されているプランに新しい SKU を追加する](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [表示されている SKU のデータ ディスク数を変更する](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [表示されているプランを Azure Marketplace から削除する](marketplace-publishing-vm-image-post-publishing.md)
* [表示されている SKU を Azure Marketplace から削除する](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [表示されている SKU の現在のバージョンを Azure Marketplace から削除する](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [表示価格を運用時の値に戻す](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [課金モデルを運用時の値に戻す](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [表示されている SKU の可視性の設定を運用時の値に戻す](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Cloud Solution Provider のリセラー インセンティブを変更する](marketplace-publishing-csp-incentive.md)
* [支払いレポートを理解する](marketplace-publishing-report-payout.md)
* [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>その他のリソース
[Azure PowerShell SDK を設定する](marketplace-publishing-powershell-setup.md)
