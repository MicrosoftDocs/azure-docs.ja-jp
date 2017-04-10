---
title: "Azure Marketplace の仮想マシン イメージの管理 | Microsoft Docs"
description: "最初に発行した後に Azure Marketplace の仮想マシン イメージを管理する方法についての詳細なガイドです。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ed2921750f93f344a4c3dbef31d9f523dedc0aae


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Azure Marketplace の仮想マシン プランの作成後ガイド
この記事では、Azure Marketplace の発行済みの仮想マシン プランを更新する方法について説明します。 また、既存のプランに&1; つ以上の新しい SKU を追加するプロセスと、Azure Marketplace から発行済みの仮想マシン プランまたは SKU を削除するプロセスについて説明します。

一度 [Azure ポータル](http://portal.azure.com)にプラン/SKU がステージングされたら、以下のフィールドは変更できません。

* **プラン識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] タブ > [Offer Identifier (プラン識別子)]
* **SKU 識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [SKUs (SKU)] タブ > [Add a SKU (SKU を追加)]
* **Publisher Namespace:** [発行ポータル] > [Virtual Machines] > [チュートリアル] タブ > Tell Us About Your Company (会社情報を入力) \(「手順 2 会社を登録する」にあります) > [Publisher Namespace] > [名前空間]

一度 [Azure Marketplace](http://azure.microsoft.com/marketplace)にプラン/SKU が表示されたら、以下のフィールドは変更できません。

* **プラン識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] タブ > [Offer Identifier (プラン識別子)]
* **SKU 識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [SKUs (SKU)] タブ > [Add a SKU (SKU を追加)]
* **Publisher Namespace:** [発行ポータル] > [Virtual Machines] > [チュートリアル] タブ -> Tell Us About Your Company (会社情報を入力) \(「手順 2 登録する」にあります) > [Publisher Namespace] > [名前空間]
* **ポート:** [発行ポータル] -> [Virtual Mashines] -> [プランの選択] -> [VM イメージ] タブ -> [ポートを開く]
* **表示されている SKU の価格の変更**
* **表示されている SKU の課金モデルの変更**
* **表示されている SKU の課金リージョンの削除**
* **表示されている SKU のデータ ディスク数の変更**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1.SKU の技術的な詳細を更新する方法
表示されている SKU に新しいバージョンを追加し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[VM イメージ]** タブをクリックします。
4. **[SKUs (SKU)]** タブの **[VM イメージ]** セクションで、更新する SKU を探します。
5. その後、SKU の新しいバージョン番号を追加し、 **[+]** をクリックします。 新しいバージョンは、X.Y.Z の形式である必要があります (X、Y、Z は整数)。 バージョンは、増分変更のみでなければなりません。
6. **[OS VHD URL]** ボックスで、オペレーティング システム VHD に対して作成した Shared Access Signature URI を追加し、変更を保存します。
   
   > [!IMPORTANT]
   > 表示されている SKU のデータ ディスク数をインクリメント/デクリメントすることはできません。 この場合、新しい SKU を作成する必要があります。 詳細なガイダンスについては、「[3. 表示されているプランに新しい SKU を追加する方法](#3-how-to-add-a-new-sku-under-a-live-offer)」を参照してください。
   > 
   > 
7. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2.プランまたは SKU の非技術的な詳細を更新する方法
Azure Marketplace で発行済みのプランまたは SKU の非技術的な (マーケティング、法的情報、サポート、カテゴリの) 詳細を更新できます。

### <a name="21-update-the-offer-description-and-logos"></a>2.1 プランの説明とロゴの更新
プランの詳細を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. 左側のメニューで、 **[詳細]** タブをクリックします。 **[詳細]** タブの *[説明]* セクションで、プランのタイトル、プランの概要、プランの長い概要を更新し、変更を保存できます。
   
   > [!NOTE]
   > SKU の詳細を更新する際は、次の点に注意してください。
   > **プランと SKU の説明に、同じテキストを入力しないでください。SKU のタイトルとプランの長い概要に、同じテキストを入力しないでください。SKU のタイトルとプランの概要に、同じテキストを入力しないでください。**
   > 
   > 
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. **[詳細]** タブの *[ロゴ]* セクションで、ロゴを更新できます。 ただし、ロゴが [Azure Marketplace のガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の「Azure Marketplace のロゴのガイドライン」) に従っていることを確認してください。
   
   > [!NOTE]
   > Hero アイコンはオプションです。 Hero アイコンをアップロードしなくてもかまいません。 ただし、Hero アイコンをアップロード後に発行ポータルから削除するプロビジョニングがありません。 削除する場合は、[Hero アイコンのガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の「Hero ロゴ バナーに関する追加のガイドライン」) に従う必要があります。
   > 
   > 
7. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. SKU の説明の更新
SKU の詳細を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. 左側のメニューで、 **[プラン]** タブをクリックします。 **[プラン]** タブの *[SKUs (SKU)]* セクションで、SKU のタイトル、SKU の概要、および SKU の説明の詳細を更新し、変更を保存できます。
   
   > [!NOTE]
   > SKU の詳細を更新する際は、次の点に注意してください。 **プランと SKU の説明に、同じテキストを入力しないでください。SKU のタイトルとプランの長い概要に、同じテキストを入力しないでください。SKU のタイトルとプランの概要に、同じテキストを入力しないでください。**
   > 
   > 
6. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらのリンクを参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 既存のリンクの変更または新しいリンクの追加
既存のリンクを変更するか、新しいリンクを追加し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. 左側のメニューで、 **[リンク]** タブをクリックします。
6. 新しいリンクを追加する場合は、[リンク] ** セクションで、 **[リンクの追加]** をクリックします。 [リンクの追加] ** ダイアログ ボックスが開きます。 このダイアログ ボックスでは、リンクのタイトル フィールドと URL フィールドを追加し、変更を保存できます。 お客様の役に立つ情報が含まれる任意のリンクを入力できます。
7. 既存のリンクを更新または削除する場合は、適切なリンクを選択し、[編集] または [削除] を適宜クリックします。
   
   > [!NOTE]
   > このセクションで入力したリンクは、本番要求の処理中に検証されるため、適切に動作することを確認してください。
   > 
   > 
8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 既存のサンプル イメージの変更または新しいサンプル イメージの追加
既存のサンプル イメージを変更するか、新しいサンプル イメージを追加し、プランを再発行するには、次の手順に従います。

> [!NOTE]
> [https://portal.azure.com](https://portal.azure.com)に表示されるサンプル イメージは&1; つだけです。
> 
> 

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. 左側のメニューで、 **[SAMPLE IMAGES (サンプル イメージ)]** タブをクリックします。
6. 新しいサンプル イメージを追加する場合は、[Sample Images (サンプル イメージ)] ** セクションで、 **[UPLOAD A NEW IMAGE (新しいイメージのアップロード)]** をクリックし、変更を保存します。
   
   > [!NOTE]
   > サンプル イメージを含めるステップは、省略可能です。
   > 
   > 
7. 既存のサンプル イメージを更新または削除する場合は、適切なサンプル イメージを見つけ、 **[REPLACE IMAGE (イメージの置換)]** または [削除] ボタンを適宜クリックします。
8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)を参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 法的情報コンテンツの更新
法的情報コンテンツを更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. 左側のメニューで、 **[LEGAL (法的情報)]** タブをクリックします。 [Legal (法的情報)] ** セクションで、ポリシーまたは使用条件を更新できます。 [使用条件] ** ボックスにポリシーまたは使用条件を入力するか、貼り付け、変更を保存します。
6. 使用条項の文字制限は、1,000,000 文字です。
7. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 サポート情報の更新
サポート情報を更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[サポート]** タブをクリックします。
4. *[サポート]* タブの **[Engineering Contact (エンジニアリング連絡先)]** セクションで、連絡先の詳細を更新できます。 これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。
5. **[サポート]** タブの *[カスタマー サポート]* セクションで、**名前、電子メール、電話番号**、**サポート URL** などのサポート連絡先の詳細を更新できます。 これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。
   
   > [!NOTE]
   > 電子メールによるサポートのみを提供する場合は、 **[カスタマー サポート]** セクションでダミーの電話番号を入力します。 そうすると、指定した電子メールが代わりに使用されます。
   > 
   > 
6. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 カテゴリの更新
プランの [カテゴリ] セクションを更新し、プランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[カテゴリ]** タブをクリックします。
4. [カテゴリ] ** セクションで、プランのカテゴリを更新し、変更を保存できます。 Azure Marketplace ギャラリーのカテゴリを最大&5; つ選択できます。
5. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
6. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>手順&3;.表示されているプランに新しい SKU を追加する方法
発行済みのプランに新しい SKU を追加するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[SKUs (SKU)]** タブをクリックします。 その後、 **[ADD A SKU (SKU を追加)]**をクリックします。  新しいダイアログ ボックスが開きます。 SKU ID を小文字で入力します。 持ち込み課金モデル (BYOL) の新しい SKU を発行する場合は、BYOL 課金モデルのチェック ボックスをオンにします。 それ以外の場合は、BYOL のチェック ボックスをオフにします。 その後、ダイアログ ボックスのチェック マークをクリックして、新しい SKU を作成します。 新しい SKU に BYOL 課金モデルを選択しなかった場合は、新しい SKU の課金モデルは時間単位に自動的に設定されます。 時間単位課金モデルで 30 日間の無料試用版を有効にする場合は、Is a free trial available? (無料試用版の利用) の One Month (1 か月) オプションをクリックします。 それ以外の場合は、[NO TRIAL (試用版なし)] を選択します (注: Is a free trial available? (無料試用版の利用)] オプションは、新しい SKU を作成するときにダイアログ ボックスで BYOL を選択しなかった場合にのみ表示されます)。
   
   > [!IMPORTANT]
   > [Hide this SKU from the Marketplace because it should always be bought via a solution template (この SKU は、常にソリューション テンプレートを介して購入する必要があるため、Marketplace に表示しない)] オプションは、Azure Marketplace でソリューション テンプレート プランの発行が承認されている場合にのみ、[はい] とマークします。 それ以外の場合は、このオプションを常に [いいえ] としてマークします。
   > 
   > 
4. 左側にあるメニューから、 **[VM イメージ]** タブをクリックし、作成した新しい SKU を確認します。
5. 新しい SKU を設定する場合、ガイダンスについては、こちらの[リンク](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)の手順 5 を参照してください。
6. 新しい SKU のマーケティング資料を追加するには、こちらの[リンク](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)の「手順 1: Marketplace のマーケティング コンテンツを指定する」の「詳細」の 2 ～ 5 を参照してください。
7. 新しい SKU の価格情報を追加するには、セクション 2.1 を参照してください。 こちらの [リンク](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. 変更を行った後、**[発行]** タブに移動し、**[PUSH TO STAGING (ステージングにプッシュ)]** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4.表示されている SKU のデータ ディスク数を変更する方法
表示されている SKU のデータ ディスク数をインクリメント/デクリメントすることはできません。 この場合、新しい SKU を作成する必要があります。 詳細なガイダンスについては、「[3. 発行済みのプランに新しい SKU を追加する方法](#3-how-to-add-a-new-sku-under-a-live-offer)」を参照してください。

## <a name="5----how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.  表示されているプランを Azure Marketplace から削除する方法
発行済みのプランの削除を要求する場合は、さまざまな注意事項があります。 表示されているプランを Azure Marketplace から削除するには、次の手順に従って、サポート チームからガイダンスを入手してください。

1. こちらの[リンク](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)を使用して、サポート チケットを作成します
2. [問題の種類] で **[Managing offers (プランの管理)]** を選択し、[カテゴリ] で **[Modifying an offer and/or SKU already in production (既に運用中のプランまたは SKU の変更)]** を選択します
3. 要求を送信します。

サポート チームがプランまたは SKU の削除手順を説明します。

> [!NOTE]
> ドラフト状態の (つまり、ステージングまたは運用中ではない) プランは、**[履歴]** タブの **[ドラフトの破棄]** をクリックすることで、いつでも削除できます。
> 
> 

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6.表示されている SKU を Azure Marketplace から削除する方法
表示されている SKU を Azure Marketplace から削除するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のウィンドウで、 **[SKUs (SKU)]** タブをクリックします。
4. 削除する SKU を選択し、その SKU の [削除] ボタンをクリックします。
5. 完了したら、発行ポータルの [発行] タブに移動し、 **[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
6. Azure Marketplace でプランが再発行されたら、SKU は、Azure Marketplace と Azure ポータルから削除されます。

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7.表示されている SKU の現在のバージョンを Azure Marketplace から削除する方法
表示されている SKU の現在のバージョンを Azure Marketplace から削除するには、次の手順に従います。 プロセスが完了したら、SKU は、以前のバージョンにロールバックされます。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のウィンドウで、 **[VM イメージ]** タブをクリックします。
4. 現在のバージョンを削除する SKU を選択し、その バージョンの [削除] ボタンをクリックします。
5. 完了したら、発行ポータルの **[発行]** タブに移動し、**[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。
6. Azure Marketplace でプランが再発行されたら、SKU の現在のバージョンは、Azure Marketplace と Azure ポータルから削除されます。 SKU は、以前のバージョンにロールバックされます。

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8.表示価格を運用時の値に戻す方法
表示されている SKU の価格を変更しました (または、表示されている SKU の課金リージョンを削除しました)。 これは Azure Marketplace でサポートされていないため、変更を運用時の値に戻す必要があります。 どうすればよいですか。

次の手順に従ってください。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[価格]** タブをクリックします。
4. [価格] タブで、価格をリセットするリージョンを選択します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. 時間単位の課金モデルが指定されている SKU の場合、その SKU は、選択したリージョンの運用環境にあるため、すべてのコアの価格をリセットします。 BYOL 課金モデルが指定されている SKU の場合は、[EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY (外部でライセンスされた (BYOL) SKU の可用性)] の SKU のチェック ボックスをオンにして、そのリージョンで SKU を使用できるようにします (次のスクリーンショットを参照)。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. **[AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES (米国の価格に基づいて他の市場の価格を自動的に設定する)]**をクリックします。
   
   > [!NOTE]
   > ボタンのラベルは、選択したリージョンによって異なる場合があります。 このドキュメントを作成するときに米国を選択したため、次のスクリーンショットのボタンのラベルは、[Auto price other markets based on prices in United States (米国の価格に基づいて他の市場の価格を自動的に設定する)] です。
   > 
   > 
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. 自動料金設定ウィザードが開きます。 最初のページには、基本市場用の選択肢が表示されています。 必要に応じて選択し、**[->]** をクリックして、次のページに進みます。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. 2 ページ目には、コアとプランを選択するためのオプションが表示されます。 必要なプランとコアを選択し、[->] をクリックします。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 3 ページ目には、市場/リージョンが表示されます。 [Toggle All (すべて切り替える)] をクリックして、すべてのリージョンを選択するか、リージョンのボックスを手動でオンにします。 [->] をクリックして、次にページに進みます。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. 4 ページ目には、為替レートが表示されます。 [完了] をクリックして、手順を完了します。 選択内容に従って価格がリセットリセットされます。
11. [価格] タブに移動し、[VIEW SUMMARY AND CHANGES (概要と変更を表示)] をクリックします。
    [View Version (バージョンの表示)] セクションの [Draft (ドラフト)] と、[比較] セクションの [Production (運用)] を選択します (次のスクリーンショットを参照)。 価格に違いがない場合は、価格が運用時の値に適切に戻っていることを意味します。
    
    ![図](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. 変更を行った後、[発行] タブに移動し、 **[PUSH TO STAGING (ステージングにプッシュ)]**をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、こちらの [リンク](marketplace-publishing-vm-image-test-in-staging.md)
13. ステージング環境でプランをテストしたら、発行ポータルの [発行] タブに移動し、 **[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9.課金モデルを運用時の運用時の値に戻す方法
表示されている SKU の課金モデルを変更しました。 これは Azure Marketplace でサポートされていないため、変更を運用時の値に戻す必要があります。 どうすればよいですか。

次の手順に従ってください。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[SKUs (SKU)]** タブをクリックします。
4. 課金モデルを元に戻すには、[編集] ボタンをクリックします。 ウィンドウが開きます。 状況に応じて、 **[Billing and licensing is done externally from Azure (Azure 外部のライセンス (ライセンス持ち込み) を使用して課金を行う)]** をオンまたはオフにします。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. 完了したら、このドキュメントの質問 8 の回答を確認して、価格を元に戻してください。
6. 発行ポータルの **[発行]** タブに移動し、プランをステージングにプッシュして、テストします。 プランをテストしたら、 **[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10.表示されている SKU の可視性の設定を運用時の値に戻す方法
次の手順に従ってください。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[SKUs (SKU)]** タブをクリックします。
4. SKU を選択し、SKU の可視性の設定を運用時の値に戻します。
   
    ![図](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. 変更が完了したら、 **[REQUEST APPROVAL TO PUSH TO PRODUCTION (本番にプッシュするための承認を要求)]** をクリックして、Azure Marketplace でプランを再発行します。

## <a name="see-also"></a>関連項目
* [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)
* [Understanding seller insights reporting (Seller Insights レポートを理解する)](marketplace-publishing-report-seller-insights.md)
* [Understanding Payout reporting (支払いレポートを理解する)](marketplace-publishing-report-payout.md)
* [How to change your Cloud Solution Provider reseller incentive (Cloud Solution Provider のリセラー インセンティブを変更する方法)](marketplace-publishing-csp-incentive.md)
* [Troubleshooting Common Publishing Problems in the Marketplace (Marketplace での発行に関してよくある問題のトラブルシューティング)](marketplace-publishing-support-common-issues.md)
* [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)
* [Creating a VM Image on-premise (オンプレミスでの VM イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)
* [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


