---
title: Azure Marketplace で既存の VM オファーを更新する
description: Azure Marketplace で既存の VM プランを更新する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: ansud
ms.openlocfilehash: e6099caf949d93abf8c665f61cd9b91d545dd0f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612553"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Azure Marketplace で既存の VM プランを更新する

この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)で仮想マシン (VM) プランを更新し、そのプランを再発行する際のさまざまな側面について説明します。 

プランを更新する理由には、次のような多くの一般的な理由があります。

-  既存の SKU に新しい VM イメージのバージョンを追加する
-  SKU を利用できるリージョンを変更する
-  新しい SKU を追加する
-  プランまたは個々の SKU のマーケットプレース メタデータを更新する
-  従量課金制を利用しているプランを更新する

このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能があります。  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>VM プランまたは SKU の許可されない変更

VM プランまたは SKU の一部の属性は、Azure Marketplace にプランを発行した後で変更することはできません。主な属性を以下に示します。

-  プランの**プラン ID** と**パブリッシャー ID**
-  既存の SKU の **SKU ID**
-  既存の SKU のデータ ディスクの数
-  既存の SKU に対する課金/ライセンス モデルの変更
-  発行された SKU の料金の引き上げ


## <a name="common-update-operations"></a>一般的な更新動作

VM プランには変更可能なさまざまな特性がありますが、次の操作が一般的なものです。

### <a name="update-the-vm-image-version-for-a-sku"></a>SKU に対する VM イメージのバージョンの更新

VM イメージがセキュリティ パッチ、追加機能などによって定期的に更新されるのは一般的です。  このようなシナリオでは、次の手順を使用して、SKU が参照する VM イメージを更新します。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[SKU]** タブで、更新する VM イメージに関連付けられている SKU をクリックします。

4.  **[Disk version]\(ディスク バージョン\)** で、 **[+New Disk Version]\(+新しいディスク バージョン\)** をクリックして新しい VM イメージを追加します。

5.  新しい VM イメージの**ディスク バージョン**を指定します。 ディスク バージョンは、[セマンティック バージョン](https://semver.org/)形式に従う必要があります。 バージョンの形式は X.Y.Z で、X、Y、Z は整数です。 提供する新しいバージョンが以前のすべてのバージョンよりも大きいことを確認します。そうしないと、再発行後、新しいバージョンはポータルまたは Azure Marketplace に表示されません。

6.  **[OS VHD URL]** に、オペレーティング システム VHD に対して作成した [Shared Access Signature (SAS) URI](./cpp-get-sas-uri.md) を入力します。 

    > [!WARNING] 
    > SKU の異なるバージョン間ではデータ ディスク数を変更できません。 以前のバージョンでデータ ディスクが構成されている場合は、この新しいバージョンでも同じデータ ディスク数である必要があります。

7.  **[発行]** をクリックし、新しい VM バージョンを Azure Marketplace に発行するワークフローを開始します。


### <a name="change-region-availability-of-a-sku"></a>SKU の利用可能なリージョンの変更

公開後に、プラン/SKU をより多くのリージョンで利用可能にすることができます。  または、特定のリージョンでのプラン/SKU のサポートを停止することができます。
利用可能性を変更するには、次の手順を実行します。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[SKU]** タブで、利用可能性を変更する SKU をクリックします。

4.  **[提供先の国/地域]** フィールドの下の **[国/地域の選択]** ボタンをクリックします。

5.  [提供先の国/地域] ポップアップで、この SKU に対するリージョンを追加または削除します。

6.  **[発行]** をクリックして発行ワークフローを開始し、SKU を利用できるリージョンを更新します。

SKU が新しいリージョンで利用できるようになると、その特定のリージョン用の価格を**価格データのエクスポート**機能を使用して指定できるようになります。 前に利用可能であったリージョンを再び利用できるように追加した場合、価格の変更は許可されないため、その価格を更新することはできません。


### <a name="add-a-new-sku"></a>新しい SKU の追加

既存のプランで新しい SKU を利用できるようにするには、以下の手順に従います。 

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[SKU]** タブで、 **[Add new SKU] (新しい SKU の追加)** をクリックし、ポップアップに **[SKU ID]** を指定します。

4.  記事「[Azure Marketplace への仮想マシンの発行](./cpp-publish-offer.md)」で説明されているように、VM を再発行します。

5.  **[発行]** をクリックし、新しい SKU を発行するワークフローを開始します。


### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータ (会社名やロゴなど) を更新します。 

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  **[Marketplace]** タブに移動し、記事「[Azure Marketplace への仮想マシンの発行](./cpp-publish-offer.md)」の指示に従ってメタデータを変更します。

4.  **[発行]** をクリックし、変更を発行するワークフローを開始します。


### <a name="update-pricing-on-published-offers"></a>発行されているプランの更新

いったん従量課金制プランが発行されると、SKU の料金を直接引き上げることはできません  (ただし、同じプランで新しい SKU を作成し、以前の SKU を削除し、新規のお客様向けにプランを再発行することができます)。一方、次の手順を使用して、発行されたプランの価格を引き下げることができます。

1.  [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2.  **[すべてのプラン]** から、更新するプランを見つけます。

3.  価格を下げる SKU をクリックします。

4.  1 x 1 の GUI で価格を設定した場合、UI で直接価格を変更することができます。 スプレッドシートのインポート/エクスポートで価格を設定した場合、インポート/エクスポート機能でしか価格を下げることはできません。

3.  **[Save]** をクリックします。

4.  **[発行]** をクリックし、変更を発行するワークフローを開始します。

引き下げた新しい料金は、Web サイトで公開されると新しいお客様向けに表示されます。  この新しい料金は、次のようにお客様に影響します。

- 新規のお客様は、この新しい料金で請求されます。 
- 既存のお客様の場合、価格の引き下げは、価格の引き下げが有効になった請求期間の開始時点に遡って反映されます。
価格の引き下げが発生した請求期間の請求が既に行われている場合、引き下げられた価格分を補うために次の請求期間中に返金されます。


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>簡略化された通貨による価格

2018 年 9 月 1 日以降、 **[Simplified Currency Pricing] (簡略化された通貨による価格)** という新しいセクションがポータルに追加されます。 Microsoft では、世界中でより予測可能な価格とお客様からの回収を実現することにより、Azure Marketplace でのビジネスの合理化を図っています。 この合理化には、お客様に請求する通貨の数を減らすことが含まれます。

この新しいセクションには、これらの新しい通貨で価格が表示されます。 これらの新しい決済通貨にすべてのお客様を移行すると、元の価格セクションは廃止され、[Simplified Currency Pricing] (簡略化された通貨による価格) セクションのみが残ります。

2018 年 11 月 1 日までに、決済通貨が変更されるリージョンに対して新しい価格を設定する必要があります。 決済通貨が変更されないリージョンに対して価格を上げることはできなくなります。

> [!NOTE] 
> API を使用してプランを発行すると、プラン JSON 内で新しいセクションを確認できます。 プランの種類に応じて `virtualMachinePricingV2` または `monthlyPricingV2` として注釈が付けられます。 

この変更に関する質問がある場合は、[Azure Marketplace のサポート](../../support-azure-marketplace.md)にお問い合わせください。


## <a name="compare-feature"></a>機能の比較

既に発行されているプランを変更する場合、 **[比較]** 機能を活用して加えられた変更を監査することができます。 この機能を使用するには:

1.  編集プロセス中の任意のタイミングで、プランの **[比較]** をクリックします。

    ![比較機能ボタン](./media/publishvm_037.png)


2.  マーケティングの資産やメタデータのバージョンを横に並べて表示します。


## <a name="history-of-publishing-actions"></a>発行操作の履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニューバーにある **[履歴]** 項目をクリックします。 ここで、Azure Marketplace プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

