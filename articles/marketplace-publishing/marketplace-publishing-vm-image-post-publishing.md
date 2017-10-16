---
title: "Azure Marketplace の仮想マシン イメージの管理 | Microsoft Docs"
description: "仮想マシン イメージを Azure Marketplace に初めて発行した後で管理する方法についての詳細なガイド"
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
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Azure Marketplace の仮想マシン プランの作成後ガイド
この記事では、Azure Marketplace の発行済みの仮想マシン プランを更新する方法について説明します。 既存のプランに 1 つ以上の新しい SKU を追加するプロセスについて説明します。 Azure Marketplace から発行済みの仮想マシン プランまたは SKU を削除するプロセスについても説明します。

プラン/SKU が [Azure ポータル](http://portal.azure.com)にステージングされた後は、以下のテキスト ボックスを変更することはできません。

* **プラン識別子:** 発行ポータルで、**[仮想マシン]** に移動し、プランを選択します。 次に、**[VM イメージ]** > **[プラン識別子]** をクリックします。
* **SKU 識別子:** 発行ポータルで、**[仮想マシン]** に移動し、プランを選択します。 次に、**[SKU]** > **[Add a SKU]\(SKU の追加\)** をクリックします。
* **Publisher Namespace**: 発行ポータルで、**[仮想マシン]** > **[ウォークスルー]** > **[会社についてお聞かせください]** (“手順 2. 会社を登録する” にあります) > **[Publisher Namespace]** > **[Namespace]** に移動します。

プラン/SKU が [Marketplace](http://azure.microsoft.com/marketplace) に表示された後は、以下のテキスト ボックスを変更することはできません。

* **プラン識別子:** 発行ポータルで、**[仮想マシン]** に移動し、プランを選択します。 次に、**[VM イメージ]** > **[プラン識別子]** をクリックします。
* **SKU 識別子:** 発行ポータルで、**[仮想マシン]** に移動し、プランを選択します。 次に、**[SKU]** > **[Add a SKU]\(SKU の追加\)** をクリックします。
* **Publisher Namespace**: 発行ポータルで、**[仮想マシン]** > **[ウォークスルー]** > **[会社についてお聞かせください]** (“手順 2. 会社を登録する” にあります) > **[Publisher Namespace]** > **[Namespace]**に移動します。
* **ポート:** 発行ポータルで、**[仮想マシン]** に移動し、プランを選択します。 次に、**[VM イメージ]** > **[Open Ports]\(ポートを開く\)** をクリックします。
* **表示されている SKU の価格の変更**
* **表示されている SKU の課金モデルの変更**
* **表示されている SKU の課金リージョンの削除**
* **表示されている SKU のデータ ディスク数の変更**

## <a name="update-the-technical-details-of-a-sku"></a>SKU の技術面の詳細を更新する
表示されている SKU に新しいバージョンを追加してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[VM イメージ]** タブをクリックします。
4. **[SKU]** セクションで、更新する SKU を探します。
5. SKU の新しいバージョン番号を追加し、**[+]** ボタンをクリックします。 新しいバージョンは、X.Y.Z 形式にする必要があります (X、Y、Z は整数)。 バージョンは、増分変更のみでなければなりません。
6. **[OS VHD URL]** ボックスで、オペレーティング システム VHD に対して作成した Shared Access Signature URI を入力し、変更を保存します。

   > [!IMPORTANT]
   > 表示されている SKU のデータ ディスク数を増加/減少させることはできません。 この場合、新しい SKU を作成する必要があります。 詳細なガイダンスについては、「[表示されているプランに新しい SKU を追加する](#add-a-new-sku-under-a-listed-offer)」セクションを参照してください。
   >
   >
7. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![VM イメージ](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>プランまたは SKU の技術面の詳細を更新する
Marketplace に発行済みのプランまたは SKU の技術面以外の (マーケティング、法的情報、サポート、カテゴリの) 詳細を更新できます。

### <a name="update-the-offer-description-and-logos"></a>プランの説明とロゴを更新する
プランの詳細を更新してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. **[詳細]** タブをクリックします。**[説明]** セクションで、プランの **[タイトル]**、**[概要]** および **[概要 (長文)]** を更新し、変更を保存します。

   > [!NOTE]
   > SKU の詳細を更新する場合は、以下の制約に注意してください。 
   * プランの説明と SKU の説明に、同じテキストを入力しないでください。
   * SKU のタイトルとプランの長文の概要に、同じテキストを入力しないでください。 
   * SKU のタイトルとプランの概要に、同じテキストを入力しないでください。
   >
   >

    ![詳細](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. **[詳細]** タブの **[ロゴ]** セクションで、ロゴを更新します。 ロゴが [Azure Marketplace のガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)に従っていることを確認してください。

   > [!NOTE]
   > Hero アイコンはオプションです。 Hero アイコンをアップロードしなくてもかまいません。 ただし、Hero アイコンをアップロードした場合、後で発行ポータルから削除することはできません。 [Hero アイコンのガイドライン](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)に従ってください。
   >
   >
7. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![ロゴ](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>SKU の説明の更新
SKU の詳細を更新してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. **[プラン]** タブをクリックします。**[SKU]** セクションで、SKU の **[タイトル]**、**[概要]** および **[説明]** を更新し、変更を保存します。

   > [!NOTE]
   > SKU の詳細を更新する場合は、以下の制約に注意してください。 
   * プランの説明と SKU の説明に、同じテキストを入力しないでください。 
   * SKU のタイトルとプランの長文の概要に、同じテキストを入力しないでください。 
   * SKU のタイトルとプランの概要に、同じテキストを入力しないでください。
   >
   >
6. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![プラン](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>既存のリンクを変更するか新しいリンクを追加する
既存のリンクを変更するか新しいリンクを追加してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. **[リンク]** タブをクリックします。
6. 新しいリンクを追加するには、**[リンク]** セクションで、**[+ リンクの追加]** をクリックします。 **[リンクの追加]** ダイアログ ボックスで、リンクの **[タイトル]** と **[URL]** を入力し、変更を保存します。 お客様の役に立つ可能性がある情報を含む任意のリンクを入力できます。
7. 既存のリンクを更新または削除するには、リンクを選択し、**[編集]** ボタンまたは **[削除]** ボタンをクリックします。

   > [!NOTE]
   > このセクションで入力したリンクは、運用要求の処理中に検証されるため、適切に動作することを確認してください。
   >
   >
8. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![リンク](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![リンクを追加する](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>既存のサンプル イメージを変更するか新しいサンプル イメージを追加する
既存のサンプル イメージを変更するか新しいサンプル イメージを追加してプランを再発行するには、次の手順に従います。

> [!NOTE]
> [Azure ポータル](https://portal.azure.com)に表示されるサンプル イメージは 1 つだけです。
>
>

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. **[サンプル イメージ]** タブをクリックします。
6. 新しいサンプル イメージを追加するには、**[サンプル イメージ]** セクションで、**[UPLOAD A NEW IMAGE]\(新しいイメージのアップロード\)** をクリックし、変更を保存します。

   > [!NOTE]
   > サンプル イメージを含める手順は省略可能です。
   >
7. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![サンプル イメージ](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>法的情報コンテンツを更新する
法的情報コンテンツを更新してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[マーケティング]** タブをクリックします。
4. **[英語 (米国)]** をクリックします。
5. **[法的情報]** タブをクリックします。**[法的情報]** セクションで、ポリシー/使用条件を更新します。 **[使用条件]** ボックスにポリシー/使用条件を入力するか貼り付けた後、変更を保存します。
6. 使用条件の文字制限は、百万字です。
7. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
8. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![法的情報](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>サポート情報を更新する
サポート情報を更新してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[サポート]** タブをクリックします。
4. **[エンジニアリング連絡先]** セクションで、エンジニアリング連絡先の詳細を更新します。 これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。
5. **[カスタマー サポート]** セクションで、サポートの連絡先の詳細と **[サポート URL]** を更新します。 これらの詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。

   > [!NOTE]
   > 電子メールによるサポートのみを提供する場合は、**[カスタマー サポート]** セクションにはダミーの電話番号を入力します。 この場合は、指定したメールが代わりに使用されます。
   >
   >
6. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![サポート](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>カテゴリを更新する
プランの [カテゴリ] セクションを更新してプランを再発行するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[カテゴリ]** タブをクリックします。
4. **[カテゴリ]** セクションで、プランのカテゴリを更新し、変更を保存できます。 Azure Marketplace ギャラリーのカテゴリを最大 5 つ選択できます。
5. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
6. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![カテゴリ](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>表示されているプランに新しい SKU を追加する
発行済みのプランに新しい SKU を追加するには、次の手順を実行します。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[SKU]** タブをクリックします。次に、**[SKU の追加]** をクリックします。 
4. ダイアログ ボックスに **[SKU 識別子]** を小文字で入力します。 新しい SKU をライセンス持ち込み課金モデル (BYOL) で発行する場合は、**[BYOL 課金モデル]** チェック ボックスをオンにします。 それ以外の場合はチェック ボックスをオフにします。 新しい SKU を作成するには、チェック マークをクリックします。 BYOL 課金モデルを選択しなかった場合、課金モデルは自動敵に 1 時間単位に設定されます。 時間単位の課金モデルの 30 日間の無料試用版を提供する場合は、**[Is a free trial available?]\(無料試用版を提供しますか?\)** に対して **[1 か月]** を選択します。 それ以外の場合は、**[No Trial]\(試用なし\)**を選択します。 (**[Is a free trial available?]\(無料試用版を提供しますか?\)** は、新しい SKU の作成中に BYOL を選択していない場合にのみ表示されます)。

   > [!IMPORTANT]
   > **[Hide this SKU from the Marketplace because it should always be bought via a solution template]\(この SKU は、常にソリューション テンプレートを介して購入する必要があるため、Marketplace に表示しない\)** オプションは、ソリューション テンプレートの発行が承認されている場合に*のみ*、**[はい]** にする必要があります。 それ以外の場合は、このオプションは、常に **[いいえ]** にする必要があります。
   >
   >
4. 左側のメニューで、**[VM イメージ]** タブをクリックし、作成した新しい SKU を確認します。
5. 新しい SKU を設定するには、[VM イメージ用の証明書の取得](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)に関するページを参照してください。
6. 新しい SKU のマーケティング資料を追加するには、「[Marketplace のマーケティング コンテンツを指定する](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)」を参照してください。
7. 新しい SKU の価格情報を追加するには、「[価格を設定する](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)」を参照してください。
8. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
9. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

    ![SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![SKU を追加する](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>表示されている SKU のデータ ディスク数を変更する
表示されている SKU のデータ ディスク数を増加/減少させることはできません。 この場合、新しい SKU を作成する必要があります。 詳細なガイダンスについては、「[表示されているプランに新しい SKU を追加する](#add-a-new-sku-under-a-listed-offer)」セクションを参照してください。

## <a name="delete-a-listed-offer-from-the-marketplace"></a>表示されているプランを Azure Marketplace から削除する
発行済みのプランの削除を要求する場合は、さまざまな注意事項に対処する必要があります。 表示されているプランを Azure Marketplace から削除するためのガイダンスをサポート チームから入手するには、次の手順に従います。

1. [[インシデントの作成]](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) ページで、サポート チケットを作成します。

2. **[問題の種類]** で **[Managing offers]\(プランの管理\)** を選択し、**[カテゴリ]** で **[Modifying an offer and/or SKU already in production]\(既に運用中のプランまたは SKU の変更\)** を選択します
3. 要求を送信します。

サポート チームがプラン/SKU の削除手順を説明します。

> [!NOTE]
> [ドラフト] 状態の間は、プランをいつでも削除できます ([ステージング] または [運用] 状態のプランは削除できません)。 **[履歴]** タブで、**[ドラフトの破棄]** をクリックします。
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>表示されている SKU を Azure Marketplace から削除する
表示されている SKU を Azure Marketplace から削除するには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。

2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のウィンドウで、**[SKU]** タブをクリックします。
4. 削除する SKU を選択し、**[削除]** ボタンをクリックします。
5. 発行ポータルで **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。
6. Marketplace にプランが再発行されると、SKU が Marketplace と Azure ポータルから削除されます。

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>表示されている SKU の現在のバージョンを Azure Marketplace から削除する
表示されている SKU の現在のバージョンを Azure Marketplace から削除するには、次の手順に従います。 

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。

2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[VM イメージ]** タブをクリックします。
4. 現在のバージョンを削除する SKU を選択し、**[削除]** ボタンをクリックします。
5. 発行ポータルで **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。
6. Marketplace でプランが再発行されると、SKU の現在のバージョンが Marketplace と Azure ポータルから削除されます。 SKU は、以前のバージョンにロールバックされます。

## <a name="revert-the-listing-price-to-production-values"></a>表示価格を運用時の値に戻す
表示価格を運用時の値に戻すには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。
2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[価格]** タブをクリックします。
4. 価格をリセットするリージョンを選択します。

    ![価格のリージョン](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. 時間単位の課金モデルが指定されている SKU の場合は、選択したリージョンで運用中であるため、すべてのコアの価格がリセットされます。 BYOL 課金モデルが指定されている SKU の場合は、**[EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY]\(外部でライセンスされた (BYOL) SKU の可用性\)** セクションで該当する SKU のチェック ボックスをオンにして、そのリージョンで SKU を使用できるようにします。

    ![価格モデル](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. **[AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES]\(米国の価格に基づいて他の市場の価格を自動的に設定する\)** をクリックします。

   > [!NOTE]
   > ボタンのラベルは、選択したリージョンによって異なっている可能性があります。 ここでは [米国] を選択したため、ボタンのラベルは **[AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES]\(米国の価格に基づいて他の市場の価格を自動的に設定する\)** になっています。
   >
   >

    ![価格の自動設定](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. 自動料金設定ウィザードの 1 ページ目で、基本市場を選択し、**矢印**ボタンをクリックします。

    ![基本市場](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. 2 ページ目で、サービス プランとメーター (コア) を選択し、**矢印** ボタンをクリックします。

    ![サービス プランとメーター](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 3 ページ目で、すべての地域を選択する場合は **[Toggle All]\(すべてを切り替え\)** をクリックします。 または、特定の地域のチェック ボックスを手動で選択できます。 **矢印**ボタンをクリックします。

    ![市場を選択する](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. 4 ページ目で、換算レートを確認し、**[完了]** をクリックします。 選択内容に従って価格がリセットされます。

11. **[価格]** タブで、**[VIEW SUMMARY AND CHANGES]\(概要と変更の表示\)** をクックします。
    **[View Version]\(バージョンの表示\)** で **[ドラフト]** を選択し、**[Compare with]\(比較対象\)** で **[Production]\(運用\)**  を選択します。 価格に差がなければ、価格は運用時の値に戻っています。

    ![概要と変更の表示](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
13. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

## <a name="revert-the-billing-model-to-production-values"></a>課金モデルを運用時の値に戻す
課金モデルを運用時の値に戻すには、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。

2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[SKU]** タブをクリックします。
4. 課金モデルを元に戻すには、**[編集]** ボタンをクリックします。 開いたウィンドウで、**[Billing and licensing is done externally from Azure (Azure 外部のライセンス]\(ライセンス持ち込み) を使用して課金を行う\)** をオンまたはオフにします。

    ![課金を編集する](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. この記事の「表示価格を運用時の値に戻す」で説明されている手順に従います。
6. **[発行]** タブに移動し、**[PUSH TO STAGING]\(ステージング環境にプッシュ\)** をクリックします。 ステージング環境でプランをテストする方法に関する詳細なガイダンスについては、[Marketplace 向けの VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)に関するページを参照してください。
7. ステージング環境でプランをテストしたら、発行ポータルの **[発行]** タブに移動します。 **[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認の要求\)** をクリックして、プランを Marketplace に再発行します。

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>表示されている SKU の可視性の設定を運用時の値に戻す
表示されている SKU の可視性の設定を運用時の値に戻には、次の手順に従います。

1. [発行ポータル](https://publish.windowsazure.com)にサインインします。

2. **[仮想マシン]** タブに移動し、プランを選択します。
3. 左側のメニューで、**[SKU]** タブをクリックします。
4. SKU を選択し、SKU の可視性の設定を運用時の値に戻します。

    ![表示](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. 変更が終わったら、**[REQUEST APPROVAL TO PUSH TO PRODUCTION]\(運用環境にプッシュするための承認を要求\)** をクリックして、プランを Marketplace に再発行します。

## <a name="see-also"></a>関連項目
* [概要: Azure Marketplace にプランを発行する](marketplace-publishing-getting-started.md)
* [支払いレポートを理解する](marketplace-publishing-report-payout.md)
* [Cloud Solution Provider のリセラー インセンティブを変更する](marketplace-publishing-csp-incentive.md)
* [Marketplace での発行に関してよくある問題のトラブルシューティングを行う](marketplace-publishing-support-common-issues.md)
* [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)
* [オンプレミスで VM イメージを作成する](marketplace-publishing-vm-image-creation-on-premise.md)
* [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
