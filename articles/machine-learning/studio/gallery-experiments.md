---
title: "Cortana Intelligence ギャラリー 実験 | Microsoft Docs"
description: "Cortana Intelligence ギャラリーで実験を共有および発見します。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: roopalik;garye
ms.openlocfilehash: 472ecac4ac1d9a86f0a3ad8271dd96257c331e5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="discover-experiments-in-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーで実験を発見する
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Machine Learning Studio 向け実験
ギャラリーには、[Azure Machine Learning Studio](https://studio.azureml.net) で開発されたさまざまな[実験](https://gallery.cortanaintelligence.com/experiments)があります。 実験の範囲は、特定の機械学習手法を示す簡単な概念実証の実験から、複雑な機械学習の問題用の熟成されたソリューションまでに及びます。

> [!NOTE]
> ***実験***は、予測分析モデルの構築に使用できる Machine Learning Studio 内のキャンバスです。 さまざまな分析モジュールを使用してデータを接続することで、モデルを作成します。 さまざまなアイデアを取り入れ、試行して、最終的には Azure の Web サービスとしてモデルをデプロイできます。 基本的な実験を作成する方法の例については、「[Machine Learning のチュートリアル: Azure Machine Learning Studio で初めての実験を作成する](create-experiment.md)」を参照してください。 予測分析ソリューションを作成する方法のより包括的なチュートリアルについては、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](walkthrough-develop-predictive-solution.md)」を参照してください。
>
>

## <a name="discover"></a>発見
[ギャラリーで](http://gallery.cortanaintelligence.com)実験を閲覧するには、ギャラリー ホーム ページの上部にある **[実験]** を選択します。

**[実験](https://gallery.cortanaintelligence.com/experiments)** ページには、最近追加された一般的な実験の一覧が表示されます。 すべての実験を表示するには、**[すべて表示]** ボタンをクリックします。 特定の実験を検索するには、**[すべて参照]** を選択し、フィルター条件を選択します。 ギャラリー ページの上部にある **[Search (検索)]** ボックスに検索語句を入力することもできます。

実験の詳細情報については、実験の詳細ページで入手できます。 実験の詳細ページを開くには、目的の実験を選択します。 実験の詳細ページの **[コメント]** セクションで、実験についてコメントすること、フィードバックを提供すること、または質問することができます。 実験は、Twitter や LinkedIn で友人や同僚と共有することもできます。 実験の詳細ページへのリンクをメールで送信し、他のユーザーを招待してそのページを見せることもできます。

![この項目を友人と共有する](./media/gallery-how-to-use-contribute-publish/share-links.png)

![独自のコメントを追加する](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>ダウンロード
Machine Learning Studio ワークスペースに、ギャラリーにある任意の実験のコピーをダウンロードできます。 その後、コピーに変更を加えて独自のソリューションを作成できます。

Cortana Intelligence ギャラリーには、実験のコピーをインポートする方法が 2 つ用意されています。

* **ギャラリーから**。 ギャラリーで目的の実験を見つけた場合は、コピーをダウンロードしてから、Machine Learning Studio ワークスペースで開くことができます。
* **Machine Learning Studio 内から**。 Machine Learning Studio で、ギャラリー内の任意の実験をテンプレートとして使用し、新しい実験を作成することができます。

### <a name="from-the-gallery"></a>ギャラリーから

1. ギャラリーで、実験の詳細ページを開きます。
2. **[Studio で開く]** を選択します。

    ![ギャラリーから実験を開く](./media/gallery-experiments/open-experiment-from-gallery.png)

**[Studio で開く]** を選択すると、Machine Learning Studio ワークスペースで実験が開きます。 (Machine Learning Studio にまだサインインしていない場合は、最初に Microsoft アカウントを使用してサインインするように求められます。)

### <a name="from-within-machine-learning-studio"></a>Machine Learning Studio 内から

1. Machine Learning Studio で **[新規]** を選択します。
2. **[実験]** を選択します。 実験をギャラリーの一覧から選択するか、**[検索]** ボックスを使用して特定の実験を検索します。
3. 実験をマウスでポイントし、**[Studio で開く]** を選択します。 (実験に関する情報を表示するには、**[ギャラリーで表示]** を選択します。 この操作で、ギャラリー内の実験の詳細ページに移動します。)

    ![Machine Learning Studio 内から ギャラリー実験を開く](./media/gallery-experiments/open-experiment-from-studio.png)

Machine Learning Studio で作成した他のすべての実験のように、実験をカスタマイズ、反復、およびデプロイすることができます。

![Studio で開かれている実験](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>投稿
ギャラリーにサインインすると、ギャラリー コミュニティのメンバーになります。 コミュニティのメンバーは独自の実験を投稿できるため、ユーザーは他のユーザーが見つけたソリューションを利用できるようになります。

### <a name="publish-your-experiment-to-the-gallery"></a>ギャラリーに実験を公開する

1. Microsoft アカウントを使用して、Machine Learning Studio にサインインします。
2. 実験を作成したら、それを実行します。
3. 実験をギャラリーで発行する準備ができたら、実験キャンバスの下にあるアクションの一覧で、**[ギャラリーに発行する]** を選択します。

    ![[ギャラリーに発行する] を選択する](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. **[実験の説明]** ページで、タイトルとタグを入力します。 タイトルとタグはわかりやすいものにしてください。 使用した手法や、解決する実際の問題がわかるようにしてください。 わかりやすい実験のタイトルの例としては、「二項分類: Twitter のセンチメント分析」などが挙げられます。

    ![発行用のタイトルとタグを入力する](./media/gallery-experiments/experiment-description.png)
5. **[概要]** ボックスに実験の概要を入力します。 実験で解決する問題と、対処の方法について簡単に説明します。
6. **[DETAILED DESCRIPTION]** ボックスで、実験の各部分で実行した手順を説明します。 説明に含めると有用なトピックの例を次に示します。
   * 実験グラフのスクリーンショット
   * データ ソースと説明
   * データ処理
   * 特徴エンジニアリング
   * モデルの説明
   * 結果およびモデルのパフォーマンスの評価

   マークダウンを使用して説明に書式を設定することができます。 実験を発行したときに、実験の説明ページに入力した内容がどのように表示されるかを確認するには、**[プレビュー]** を選択します。

   ![[プレビュー] を選択し、テキストの外観を表示する](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > マークダウンの編集とプレビューのために用意されているのは、小さなテキスト ボックスです。 マークダウン エディターで実験のドキュメントを記述し、完成したドキュメントをコピーしてからギャラリー内のテキスト ボックスに貼り付けることをお勧めします。 実験を発行した後は、編集やプレビューのために、マークダウンを使用する標準的な Web ベースのツールを使用して修正を加えることができます。

7. **[イメージの選択]** ページで、実験のサムネイル イメージを選択します。 このサムネイル イメージは、実験の詳細ページ上部と実験タイルに表示されます。 他のユーザーは、ギャラリーを参照するときにサムネイル イメージを見ることになります。 コンピューターからイメージをアップロードすることも、ギャラリーからストック イメージを選択することもできます。
    </br>
    ![ギャラリーのイメージをアップロードまたは選択する](./media/gallery-experiments/select-gallery-image.png)
8. **[設定]** ページの **[表示]** で、コンテンツを一般向けに発行する (**[公開]**) か、ページへのリンクを持っているユーザーのみがアクセスできるようにする (**[非公開]**) かを選択できます。

    ![一般向けに公開するか、または非公開にするかを選択する](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > 一般向けに発行する前にドキュメントが正しく見えることを確認したい場合は、まず、実験を **[非公開]** として発行できます。 可視性の設定は後で、実験の詳細ページで **[公開]** に変更できます。
   >
   >
9. ギャラリーに実験を発行するには、**[OK]** チェック マークをオンにします。

    ![[OK] チェック マークをオンにして実験を発行する](./media/gallery-experiments/ok-checkmark.png)

高品質のギャラリーの実験を発行する方法のヒントについては、「[実験を文書化して発行する場合のヒント](#tips-for-documenting-and-publishing-your-experiment)」を参照してください。

これで終わりです。すべて完了しました。

実験をギャラリーに表示し、他のユーザーとリンクを共有できるようになりました。 可視性の設定として **[公開]** を使用して実験を発行した場合、実験はギャラリー内での参照および検索の結果に表示されます。 実験のドキュメントは、、ギャラリーにサインインすればいつでも実験の詳細ページで編集できます。

自分の投稿の一覧を表示するには、ギャラリー ページの右上隅にある自分のイメージを選択します。 次に、自分の名前を選択してアカウント ページを開きます。

![アカウント名を選択する](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>実験を更新する
必要がある場合は、ギャラリーに発行した実験のワークフロー (モジュールやパラメーターなど) に変更を加えることができます。 Machine Learning Studio で、実験に必要な変更を加えたら、もう一度発行します。 発行済みの実験は、ユーザーの変更によって更新されます。

ギャラリーでは、実験について次の情報を直接変更できます。

* 実験名
* 要約または説明
* タグ
* イメージ
* 可視性の設定 (**[公開]** または **[非公開]**)

ギャラリーから実験を削除することもできます。

これらの変更や実験の削除は、ギャラリーで、実験の詳細ページまたはプロファイル ページから行うことができます。


#### <a name="from-the-experiment-details-page"></a>実験の詳細ページから
実験の詳細ページで実験の詳細を変更するには、**[編集]** を選択します。

![[編集] を選択し、実験を編集する](./media/gallery-experiments/edit-button.png)

[詳細] ページが編集モードになります。 変更を加えるには、実験の名前、要約、またはタグの横にある **[編集]** を選択します。 変更が完了したら、**[完了]** を選択します。

![[編集] を選択して詳細を編集し、完了したら [完了] を選択する](./media/gallery-experiments/edit-details-page.png)

実験の可視性 (**[公開]** または **[非公開]**) を変更したり、ギャラリーから実験を削除したりするには、**[設定]** アイコンを選択します。

![[設定] を選択し、可視性の変更または実験の削除を行う](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>プロファイル ページから
プロファイル ページで、目的の実験の下矢印を選択し、**[編集]** を選択します。 これで、編集モードになっている実験の詳細ページに移動されます。 変更が完了したら、**[完了]** を選択します。

ギャラリーから実験を削除するには、**[削除]** を選択します。

![[編集] または [削除] を選択する](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>実験の文書化と発行のヒント
* 読者は読む前からデータ サイエンスの経験を持っていると想定できますが、平易な言葉を使用すると有益です。 できる限り詳しく説明してください。
* Cortana Intelligence Suite コンポーネントは、比較的新しいテクノロジーです。 すべての読者がその使用方法をよく知っているわけではありません。 十分な情報と段階を追った説明を提供して、読者が実験をたどりやすいようにします。
* 読者が実験のドキュメントを正しく解釈して使用するためには、イメージが役立つ場合があります。 イメージには、実験グラフやデータのスクリーン ショットが含まれます。 実験のドキュメントにイメージを挿入する方法の詳細については、「[発行のガイドラインとサンプル コレクション](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1)」を参照してください。
* 実験にデータ セットを含める (つまり、Import Data モジュールからデータ セットをインポートをしない) 場合、データ セットは実験の一部になり、ギャラリーで発行されます。 発行するデータ セットに、すべてのユーザーによる共有とダウンロードを許可するライセンス条項があることを確認してください。 ギャラリーへの投稿は、Azure の [使用条件](https://azure.microsoft.com/support/legal/website-terms-of-use/)の対象です。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
**実験用のイメージの送信や編集を行う場合の要件を教えてください。**

実験と一緒に送信するイメージは、投稿の実験タイルを作成するために使用されます。 イメージのサイズは 500 KB 未満、縦横比は 3:2、解像度は 960 &#215; 640 にすることをお勧めします。

**実験で使用したデータ セットはどうなりますか?データ セットもギャラリーで発行されるのでしょうか?**

データ セットが実験の一部となっていて、Import Data モジュールからインポートされていない場合、データ セットは実験の一部としてギャラリーで発行されます。 実験と一緒に発行するデータ セットに適切なライセンス条項があることを確認してください。 ライセンス条項では、すべてのユーザーによるデータの共有とダウンロードが許可されている必要があります。

**Import Data モジュールを使用して Azure HDInsight や SQL Server からデータを取得する実験を行いました。その際、自分の資格情報を使用してデータを取得しました。このような実験を発行できますか?どのようにすれば、自分の資格情報が共有されないと確信できますか?**

現在のところ、資格情報を使用する実験は、ギャラリーでは発行できません。

**複数のタグを入力する方法。**

タグを入力した後に別のタグを入力するには、Tab キーを押します。

**[ギャラリーに移動する](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
