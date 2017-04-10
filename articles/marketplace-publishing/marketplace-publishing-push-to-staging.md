---
title: "プランを準備およびテストして Azure Marketplace にデプロイする | Microsoft Docs"
description: "マーケティング コンテンツの指定、価格プランの構成、Azure Marketplace へのデプロイ前のプランのテストに関する詳細な手順を説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b


---
# <a name="complete-the-offer-creation-with-marketing-content"></a>マーケティング コンテンツを使用したプラン作成の実行
発行処理のこの手順では、特定のマーケティング コンテンツと Azure Marketplace でのプランと SKU の詳細を指定する必要があります。 たとえば、製品、会社のロゴ、価格プラン、プランの詳細、プランまたは SKU をステージング環境へプッシュするために必要なその他の情報を指定します。 この情報は、Azure ポータルでマーケティング コンテンツとして使用されます。 このプロセスは、[発行ポータル][link-pubportal]で開始します。

## <a name="step-1-provide-marketplace-marketing-content"></a>手順 1: Marketplace のマーケティング コンテンツを指定する
**英語は、既定で指定され、サポートされる唯一の言語です。** フィールドに指定するすべての情報が英語であることを確認してください。 ステージングにプッシュするまでは、すべての情報をいつでも編集することができます。

1. 発行ポータル ( [https://publish.windowsazure.com](https://publish.windowsazure.com)) に移動します。
2. 左側のメニューで、 **[マーケティング]** タブをクリックします。
3. メイン パネルで、 **[英語 (米国)]** をクリックします。
   
   > [!IMPORTANT]
   > ステージングにプッシュするために、イメージを含め、すべてのフィールドが入力されている必要があります。
   > 
   > 

### <a name="details-and-plans"></a>詳細と SKU
1. プランのタイトル (最大 50 文字)、プランの概要 (最大 100 文字)、プランの長い概要 (最大 256 文字)、プランの説明 (最大 1300 文字)、ロゴを、 **[詳細]** タブに入力します。
2. SKU のタイトル (最大 50 文字)、SKU の概要 (最大 100 文字)、SKU の説明 (最大 2000 文字) を、 **[Plans (SKU)]** タブに入力します。
   
   > [!NOTE]
   > プランと SKU の概要、長い概要、説明の書式設定には HTML タグを使用できます。 使用できる HTML タグは、h1、h2、h3、h4、h5、p、ol、ul、li、a[target|href]、strong、em、b、i です。
   > 
   > 
3. プランと SKU の説明に、同じテキストを入力しないでください。
4. SKU のタイトルとプランの長い概要に、同じテキストを入力しないでください。
5. SKU のタイトルとプランの概要に、同じテキストを入力しないでください。
6. 複数の SKU が指定されたプランの場合は、各 SKU のタイトルが重複しないようにしてください。
7. 必要な要件 (発行ポータルに記載された) を満たしたイメージを各サイズあたり 1 つ PNG 形式でアップロードします。
8. ロゴが以下で説明する Azure Marketplace ロゴ ガイドラインに従っていることを確認します。
   
   ![図](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Azure Marketplace のロゴのガイドライン**

発行ポータルにアップロードされるすべてのロゴは、以下のガイドラインに従っている必要があります。

* Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は少なくしてください。
* Azure ポータルのテーマの色は白黒です。 したがって、この色を、ロゴの背景色として使用しないでください。 Azure ポータルでロゴが目立つ色を背景色として使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 **透明な背景を使用している場合は、ロゴ/テキストが白、黒または青ではないことを確認します。**
* ロゴではグラデーションの背景を使用しないでください。
* ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールは "フラット" にする必要があり、グラデーションは避ける必要があります。
* ロゴは拡大しないでください。
* 小型のロゴのサイズ: 40 X 40 ピクセル
* 中型のロゴのサイズ: 90 X 90 ピクセル
* 大型のロゴのサイズ: 115 X 115 ピクセル
* ワイドなロゴのサイズ: 255 X 115 ピクセル
* Hero ロゴのサイズ: 815 X 290 ピクセル

> [!NOTE]
> Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 ただし、アップロードされたら、Hero アイコンは発行ポータルから削除できません。 その時点で、パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。
> 
> 

  ![図](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Hero ロゴ アイコンに関する追加のガイドライン (省略可能)**

* Hero ロゴはオプションです。 パブリッシャーは、Hero ロゴをアップロードしなくてもかまいません。 **ただし、アップロードされたら、Hero アイコンは発行ポータルから削除できません。その時点で、パートナーは、Hero アイコンの Azure Marketplace ガイドラインに従う必要があります。ガイドラインに従っていない場合、プランは運用環境に対して承認されません。**
* 発行者表示名、SKU のタイトル、およびプランの長い概要は、白のフォント色で表示されます。 このため、Hero アイコンの背景色には明るい色を使用しないでください。 黒、白、および透明は Hero アイコンの背景では使用できません。
* プランが表示されると、パブリッシャーの表示名、SKU のタイトル、プランの長い概要、作成ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 右側にスペースを空けておくだけで、テキスト (つまり、 パブリッシャーの表示名、SKU のタイトル、プランの長い概要) は、その場所にプログラムによって挿入されます。 右側のテキスト用の空のスペースは 415 x 100 です (また、左から 370 ピクセルだけオフセットされます)。
  
  ![図](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>リンク
左のバーの **[リンク]** タブで、お客様に役立つ情報があればそのリンクを入力します。 各リンクの名前と URL を入力します。

![図](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>サンプル イメージ (省略可能)
> [!NOTE]
> サンプル イメージを含めるステップは、省略可能です。
> 発行ポータルには複数のサンプル イメージをアップロードできますが、Azure ポータルに表示されるイメージは 1 つだけです (システムによってランダムに選択されます)。 この理由から、アップロードするのは 1 つのサンプル イメージだけにすることをお勧めします。
> 
> 

左側のメニューの **[サンプル画像]** タブで、**[新しい画像のアップロード]** をクリックして新しいイメージをアップロードします。 既存のイメージがある場合、それを置き換えるには、 **[イメージの置換]**をクリックします。

![図](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>法的情報
**[法的情報]** タブで、ポリシー/利用条件へのリンクを指定します。 大きい **[利用条件]** ボックスに利用条件を入力するか、貼り付けます。 使用条項の文字制限は、1,000,000 文字です。

![図](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**注意:** Virtual Machine プランについては、一度 Azure ポータルにプラン/SKU がステージングされたら、以下のフィールドは変更できません。

* **プラン識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] タブ > [Offer Identifier (プラン識別子)]
* **SKU 識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [SKUs (SKU)] タブ > [Add a SKU (SKU を追加)]
* **Publisher Namespace:** [発行ポータル] > [Virtual Machines] > [チュートリアル] タブ > [Tell Us About Your Company (会社情報を入力)] (「手順 2 会社を登録する」にあります) > [Publisher Namespace] > [名前空間]

Virtual Machine プランについては、一度 Azure Marketplace にプラン/SKU が表示されたら、以下のフィールドは変更できません。

* **プラン識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] > [Offer Identifier (プラン識別子)]
* **SKU 識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [SKUs (SKU)] タブ > [Add a SKU (SKU を追加)]
* **Publisher Namespace:** [発行ポータル] > [Virtual Machines] > [チュートリアル] タブ -> [Tell Us About Your Company (会社情報を入力)] (「手順 2 登録する」にあります) > [Publisher Namespace] > [名前空間]
* **ポート:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] タブ > [ポートを開く]
* **表示されている SKU の価格の変更**
* **表示されている SKU の課金モデルの変更**
* **表示されている SKU の課金リージョンの削除**
* **表示されている SKU のデータ ディスク数の変更**

## <a name="step-2-set-your-prices"></a>手順 2.: 価格を設定する
### <a name="pricing-models"></a>価格モデル
| 価格モデル | 説明 |
| --- | --- |
| 基本 |月額固定料金 (購入時に支払い) 例: 10 ドル/月 |
| 消費量 (別名:  使用量、メーター) |従量課金。プランの発行者が定義します。 超過料金はシートやユーザーごとなどの方法で定義できません。これは、比例配分を行うためのユーザーまたは容量の分割の概念を採用していないためです。 使用量は、時間単位でパートナーが報告します。 お客様は前払いではなく、月額プランなどの月ごとの請求サイクルで支払います。 |
| 無料試用版 |お客様は、期間限定で、無料で試用し、その後、標準料金を支払うことを選択できます。 |
| Free レベル |プランは常時無料です |
| プランの移行 ( 変換またはアップグレード/ダウングレードとも呼ばれます) |ユーザーが現在のプランから別の許容されるプランに移行するという概念。パートナーが定義します。 |

**使用できる価格モデル (プランの種類別)**

> [!IMPORTANT]
> 特定の価格モデルを使用できるかどうかは、プランの種類によって異なります。 次の表を参照してください。
> 
> 

|  | 基本のみ | 消費量のみ | 基本 + 消費量 |
| --- | --- | --- | --- |
| 仮想マシン イメージ |いいえ |あり |いいえ |
| 開発者サービス |あり |あり |はい |

### <a name="21-set-your-vm-prices"></a>2.1. VM 料金を設定する
仮想マシンについては、現在、次の **3 つの課金モデル**

* **時間単位:** お客様への請求は、パブリッシャーによって VM サイズに設定された料金に基づいて時間単位で行われます。 SKU の **時間単位の課金** モデルの場合、合計額は、ソフトウェア コスト (パブリッシャーが請求) とインフラストラクチャ コスト (Microsoft が請求) を足した金額です。 この合計コストは、購入を検討しているお客様に、時間単位と月単位の料金として表示されます (次のスクリーンショットを参照)。 **パブリッシャーが受け取るのは、請求ソフトウェア コストの 80% です。** したがって、SKU の価格を設定する前に適宜計算してください。
  
    ![図](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **無料試用版:** これも時間単位のモデルの 1 つです。 VM をデプロイしてから最初の 30 日間は、お客様にソフトウェア コストが請求されることはありません (無料)。 30 日が経過すると、時間単位モデルでパブリッシャーによって設定された料金に基づいて、時間単位で請求が行われます。
* **ライセンス持ち込み (BYOL):** VM で実行されているソフトウェアのライセンスが、パブリッシャーによって管理されます。

**重要:** 一度 Azure Marketplace にプラン/SKU が表示されたら、以下のフィールドは変更できません。

* **表示されている SKU の価格の変更**
* **表示されている SKU の課金モデルの変更**
* **表示されている SKU の課金リージョンの削除**
* **表示されている SKU のデータ ディスク数の変更**
* **プラン識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] > [Offer Identifier (プラン識別子)]
* **SKU 識別子:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [SKUs (SKU)] タブ > [Add a SKU (SKU を追加)]
* **Publisher Namespace:** [発行ポータル] > [Virtual Machines] > [チュートリアル] タブ -> [Tell Us About Your Company (会社情報を入力)] (「手順 2 登録する」にあります) > [Publisher Namespace] > [名前空間]
* **ポート:** [発行ポータル] > [Virtual Machines] > [プランの選択] > [VM イメージ] タブ > [ポートを開く]

### <a name="sell-to-countries-of-the-sku"></a>SKU の "販売先" の国
どこで SKU を利用できるようにするかについては、慎重に検討する必要があります。 たとえば、"Microsoft レミット" 対象として分類されている国や、"ISV レミット" 対象として分類されている国があります。

* "Microsoft レミット" 対象の国では、Microsoft がお客様から税金を徴収し、政府に納税 (レミット) します。
* "ISV レミット" 対象の国では、パートナーがお客様から税金を徴収し、政府に納税する必要があります。 "ISV レミット" 対象の国への販売を選択した場合は、その国での税金を計算して支払うことができなければなりません。

> [!NOTE]
> [発行ポータル](https://publish.windowsazure.com)で SKU の価格を設定しない限り、SKU はその国で使用できません。 時間単位 SKU および BYOL SKU の価格を設定するためのガイダンスを次に示します。
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 SKU の時間単位の価格モデルを設定する方法
SKU の時間単位の価格モデルを設定するには、次の手順に従ってください。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[SKUs (SKU)]** タブをクリックします。
4. SKU が "時間単位の課金モデル" としてマークされていることを確認します。 マークされていない場合、課金モデルを元に戻すには、 **[編集]** ボタンをクリックします。 ウィンドウが開きます。 [Billing and licensing is done externally from Azure (aka Bring Your Own License) (Azure 外部のライセンス (ライセンス持ち込み) を使用して課金を行う)] チェック ボックスをオフにして、変更を保存します。
5. SKU デプロイメントの最初の 30 日間に対して無料試用版を有効にする場合は、[Is a free trial available? (無料試用版を利用しますか)] という質問に対して [1 か月] を選択します。 それ以外の場合は、[No Trial (試用版を利用しない)] を選択します。 その後、次の手順に従ってください。
6. 左側のメニューで、 **[価格]** タブをクリックします。
7. 基本リージョンを選択します。
   
   ![図](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. すべてのコアの価格を設定します。 **SKU でサポートされていない場合も、SKU のコアすべての価格を入力する必要があります。**
   
    ![図](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. その他のリージョンの価格を手動で設定するか、自動料金設定ウィザードを使用して、基本リージョンに基づいて他のリージョンの価格を設定します。 自動料金設定ウィザードを使用するには、**[AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES (米国の価格に基づいて他の市場の価格を自動的に設定する)]** をクリックします。 **注:** ボタンのラベルは、選択したリージョンによって異なる場合があります。 このドキュメントを作成するときに米国を選択したため、次のスクリーンショットのボタンのラベルは、[Auto price other markets based on prices in United States (米国の価格に基づいて他の市場の価格を自動的に設定する)] です。
   
   ![図](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. 自動料金設定ウィザードが開きます。 最初のページには、基本市場用の選択肢が表示されています。 必要に応じて選択し、[->] をクリックして、次のページに進みます。
    
    ![図](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. 2 ページ目には、コアとプランを選択するためのオプションが表示されます。 必要なプランを選択し、[->] をクリックします。 **[Toggle All (すべて切り替える)]** をクリックして、すべての**サービス プラン**または**価格メーター**を選択するか、手動でチェック ボックスをオンにします。 **SKU でサポートされていない場合も、SKU のコアすべての価格を入力する必要があります。** したがって、すべてのコアのサイズが選択されていることを確認します。
    
    ![図](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. 3 ページ目には、市場/リージョンが表示されます。 **[Toggle All (すべて切り替える)]** をクリックして、すべてのリージョンを選択するか、リージョンのボックスを手動でオンにします。 [->] をクリックして、次にページに進みます。 **注:** Microsoft 税金レミットの対象国には家の記号が付いています。 詳細については、このページの「SKU の "販売先" の国」を参照してください。
    
    ![図](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. 4 ページ目には、為替レートが表示されます。 [完了] をクリックして、手順を完了します。

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 SKU の BYOL 価格モデルを設定する方法
SKU の BYOL の価格モデルを設定するには、次の手順に従ってください。

1. [発行ポータル](https://publish.windowsazure.com)にログインします。
2. **[Virtual Machines]** タブに移動し、プランを選択します。
3. 左側のメニューで、 **[SKUs (SKU)]** タブをクリックします。
4. SKU に "ライセンス持ち込み SKU" のマークが付いていることを確認します。 マークされていない場合、課金モデルを元に戻すには、[編集] ボタンをクリックします。 ウィンドウが開きます。 [Billing and licensing is done externally from Azure (aka Bring Your Own License) (Azure 外部のライセンス (ライセンス持ち込み) を使用して課金を行う)] チェック ボックスをオンにして、変更を保存します。
   
   ![図](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. 左側のメニューで、 **[価格]** タブをクリックします。
6. 基本リージョンを選択し、[EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY (外部でライセンスされた (BYOL) SKU の可用性)] の SKU のチェック ボックスをオンにして、そのリージョンで SKU を使用できるようにします (次のスクリーンショットを参照)。
   
   ![図](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. 他のリージョンで SKU を使用できるように、手動で、または自動料金設定ウィザードを使用して設定します。 「 **2.1.1 SKU の時間単位の価格モデルを設定する方法** 」の項目 9 ～ 13 を参照してください (自動料金設定ウィザードの使用について説明しています)。

### <a name="22-set-your-developer-service-prices"></a>2.2. 開発者向けサービスの価格を設定する
基本と使用量を組み合わせたプランにすることができます。ここで、"基本料金" は月額、"超過料金" は従量課金になります。 (詳細については、以下を参照してください。)

**例**: Contoso 開発者サービスのプラン

| プラン | 料金 | 内容 | 移行パス |
| --- | --- | --- | --- |
| 無料 |0 ドル/月 |基本的な機能 |他のすべてのプランに移行可能 |
| ブロンズ |10 ドル/月 |基本的な機能のほか、機能 X の 1,000 のクォータ。 |ブロンズ プラス、シルバー、ゴールドの各プランに移行可能 |
| ブロンズ プラス |無料試用期間: 0 ドル/月 + 0 ドル/メーター 01 |基本的な機能のほか、機能 X の 10,000 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。 |シルバー プラスとゴールドの各プランに移行可能 |
| ブロンズ プラス |有料の期間 (別名:  無料試用版の有効期限切れ): 10 ドル/月 + 0.05 ドル/メーター 01 |基本的な機能のほか、機能 X の 10,000 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。 |シルバー プラスとゴールドの各プランに移行可能 |
| シルバー |0.15 ドル/メーター 01 |お客様は、メーター 01 経由で、従量課金で支払います (機能 X 用) |ブロンズ、ゴールドの各プランに移行可能 |
| シルバー プラス |20 ドル/月 + 0.15 ドル/メーター 01 + 0.01 ドル/メーター 02 |基本的な機能のほか、機能 X の 10,000 および機能 Y の 100 のクォータ。機能 X のクォータが使用されると、お客様は メーター 01 経由の従量課金で支払うことができます。  機能 Y のクォータが使用されると、お客様は メーター 02 経由の従量課金で支払うことができます。 |ブロンズ プラス、ゴールドの各プランに移行可能 |
| ゴールド |1,000 ドル/月 |機能 X の 10,000 のクォータ、機能 Y の 1,000 のクォータ、機能 Z は無制限に使用できます |Free を除くすべてのプランに移行可能 |

## <a name="step-3-provide-support-information"></a>手順 3.: サポート情報を入力する
連絡先の詳細は、パートナーとマイクロソフト間の内部コミュニケーションにのみに使用されます。 サポートURL は、エンド ユーザーに提供されます。

1. 発行ポータルの左側にある **[サポート]** に移動します。
2. **[エンジニアリング連絡先]**に情報を入力します。
3. **[カスタマー サポート]**に情報を入力します。 電子メール サポートのみを指定する場合は、ダミーの電話番号を入力すると、指定した電子メールが代わりに使用されます。
4. サポート URL を入力します。

## <a name="step-4-choose-azure-marketplace-categories"></a>手順 4.: Azure Marketplace カテゴリを選択する
**[カテゴリ]** タブには、一連の選択項目が表示されます。 該当するカテゴリを最大 5 つまで選択できます。

## <a name="how-your-marketing-will-appear"></a>マーケティングの表示例
[Azure Marketplace Web サイト](https://azure.microsoft.com/marketplace/)と [Azure Portal](https://portal.azure.com) における、プラン マーケティング情報の使用方法についての詳細なビューを次に示します。

### <a name="azure-marketplace-website"></a>Azure Marketplace Web サイト
![図](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![図](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure Marketplace Web サイト上のプランの一覧*

![図](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure Marketplace Web サイト上のプランの説明の詳細*

![図](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure Marketplace Web サイト上のプランの説明の価格詳細*

### <a name="azure-portal"></a>Azure ポータル
![図](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Azure ポータルでのプランの一覧*

![図](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Azure ポータルのプランの説明の詳細*

## <a name="next-steps"></a>次のステップ
Marketplace コンテンツが読み込まれたので、プランのステージングでのテストに進みましょう。 ただし、手順はプランの種類によって異なるため、以下の一覧から適切なプランの種類を選択する必要があります。

* [ステージング環境での VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)
* [ステージングでのソリューション テンプレート プランのテスト](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md



<!--HONumber=Dec16_HO2-->


