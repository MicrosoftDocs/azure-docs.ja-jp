---
title: Azure Data Factory マネージド仮想ネットワーク マッピング データ フローを使用してデータを変換する
description: このチュートリアルでは、Azure Data Factory でマッピング データ フローを使用してデータを変換するための詳細な手順を説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249442"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>マッピング データ フローを使用して安全にデータを変換する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](./introduction.md)」を参照してください。

このチュートリアルでは、Data Factory ユーザー インターフェイス (UI) を使い、[Data Factory マネージド仮想ネットワーク](managed-virtual-network-private-endpoint.md)内でマッピング データ フローを使用して *Azure Data Lake Storage Gen2 ソースから Data Lake Storage Gen2 シンク (どちらも、選択したネットワークへの接続のみを許可)* にデータをコピーして変換するパイプラインを作成します。 このチュートリアルの構成パターンは、マッピング データ フローを使用してデータを変換するときに拡張することができます。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
>
> * データ ファクトリを作成します。
> * データ フロー アクティビティが含まれるパイプラインを作成します。
> * 4 つの変換を使用して、マッピング データ フローを構築します。
> * パイプラインをテスト実行します。
> * データ フロー アクティビティを監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 Data Lake Storage を、*ソース* と *シンク* のデータ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)に関するページを参照してください。 *ストレージ アカウントで、選択したネットワークからのアクセスのみが許可されていることを確認します。* 

このチュートリアルで変換するファイルは、[GitHub コンテンツ サイト](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)にある moviesDB.csv です。 GitHub からファイルを取得するには、コンテンツを任意のテキスト エディターにコピーして、.csv ファイルとしてローカルに保存します。 ファイルをご自分のストレージ アカウントにアップロードするには、[Azure portal を使用した BLOB のアップロード](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。 例では、**sample-data** という名前のコンテナーを参照します。

## <a name="create-a-data-factory"></a>Data Factory の作成

この手順では、データ ファクトリを作成し、Data Factory UI を開いて、データ ファクトリにパイプラインを作成します。

1. Microsoft Edge または Google Chrome を開きます。 現在、Data Factory UI をサポートしている Web ブラウザーは Microsoft Edge と Google Chrome のみです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。
1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   データ ファクトリの名前は "*グローバルに一意*" にする必要があります。 データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。

1. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
1. **[リソース グループ]** で、次の手順のいずれかを行います。

    * **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
    * **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。

1. **［作成］** を選択します
1. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、 **[Data Factory]** ページに移動します。
1. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Data Factory マネージド仮想ネットワークに Azure IR を作成する

この手順では、Azure IR を作成し、Data Factory マネージド仮想ネットワークを有効にします。

1. Data Factory ポータルで、 **[管理]** に移動し、 **[新規]** を選択して新しい Azure IR を作成します。

   ![新しい Azure IR の作成を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. **[Integration runtime setup]\(統合ランタイムのセットアップ\)** ページで、必要な機能に基づいて作成する統合ランタイムを選択します。 このチュートリアルでは、 **[Azure、セルフホステッド]** を選択し、 **[続行]** をクリックします。 
1. **[Azure]** を選択し、 **[続行]** をクリックして、Azure Integration ランタイムを作成します。

   ![新しい Azure IR を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. **[仮想ネットワークの構成 (プレビュー)]** で、 **[有効化]** を選択します。

   ![新しい Azure IR の有効化を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. **［作成］** を選択します

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>データ フロー アクティビティが含まれるパイプラインの作成

この手順では、データ フロー アクティビティが含まれるパイプラインを作成します。

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。

   ![パイプラインの作成を示すスクリーンショット。](./media/doc-common-process/get-started-page.png)

1. パイプラインの [プロパティ] ウィンドウで、パイプラインの名前として **TransformMovies** と入力します。
1. ファクトリの上部のバーで、 **[データ フローのデバッグ]** スライダーをオンにスライドします。 デバッグ モードを使用すると、ライブ Spark クラスターに対する変換ロジックの対話型テストが可能になります。 データ フロー クラスターのウォームアップには 5 から 7 分かかります。 データ フローの開発を計画している場合は、最初に **[データ フローのデバッグ]** をオンにします。 詳細については、[デバッグ モード](./concepts-data-flow-debug-mode.md)に関するページを参照してください。

    ![[データ フローのデバッグ] のスライダーを示すスクリーンショット。](media/tutorial-data-flow-private/dataflow-debug.png)
1. **[アクティビティ]** ウィンドウで、 **[移動と変換]** を展開します。 ウィンドウから **[Data Flow]** アクティビティをパイプライン キャンバスにドラッグします。

1. **[Adding data flow]\(データ フローの追加\)** ポップアップで、 **[Create new data flow]\(新しいデータ フローの作成\)** を選択し、 **[Mapping Data Flow]\(マッピング データ フロー\)** を選択します。 完了したら、 **[OK]** をクリックします。

    ![マッピング データ フローを示すスクリーンショット。](media/tutorial-data-flow-private/mapping-dataflow.png)

1. [プロパティ] ウィンドウで、データ フローに **TransformMovies** という名前を付けます。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>データ フロー キャンバスでの変換ロジックの作成

データ フローを作成すると、データ フロー キャンバスに自動的に飛ばされます。 この手順では、Data Lake Storage 内の moviesDB.csv ファイルを取得し、1910 年から 2000 年までのコメディの平均評価を集計するデータ フローを作成します。 次に、このファイルを Data Lake Storage ストレージに書き戻します。

### <a name="add-the-source-transformation"></a>ソース変換を追加する

この手順では、Data Lake Storage Gen2 をソースとして設定します。

1. データ フロー キャンバスで **[Add Source]\(ソースの追加\)** ボックスを選択して、ソースを追加します。

1. ソースに **MoviesDB** という名前を付けます。 **[新規]** を選択して、新しいソース データセットを作成します。

1. **[Azure Data Lake Storage Gen2]** を選択し、 **[続行]** を選択します。

1. **[DelimitedText]** を選択してから、 **[続行]** を選択します。

1. データセットに **MoviesDB** という名前を付けます。 リンクされたサービスのドロップダウンで、 **[新規]** を選択します。

1. リンクされたサービスの作成画面で、Data Lake Storage Gen2 のリンクされたサービスに **ADLSGen2** という名前を付けて、使用する認証方法を指定します。 次に、接続の資格情報を入力します。 このチュートリアルでは、**アカウント キー** を使用してストレージ アカウントに接続しています。 

1. **[Interactive authoring]\(インタラクティブな作成\)** を必ず有効にしてください。 これは有効になるまでに 1 分程かかる場合があります。

    ![インタラクティブな作成を示すスクリーンショット。](./media/tutorial-data-flow-private/interactive-authoring.png)

1. **[接続テスト]** を選択します。 プライベート エンドポイントを作成および承認しなければストレージ アカウントにアクセスできないようになっているため、これは失敗するはずです。 エラー メッセージ内に、プライベート エンドポイントを作成するためのリンクが表示されます。それをたどることで、マネージド プライベート エンドポイントを作成できます。 代わりに、 **[管理]** タブに直接移動し、[こちらのセクション](#create-a-managed-private-endpoint)の指示に従って、マネージド プライベート エンドポイントを作成する方法もあります。

1. ダイアログ ボックスは開いたままにして、ストレージ アカウントに移動します。

1. [このセクション](#approval-of-a-private-link-in-a-storage-account)の手順に従って、プライベート リンクを承認します。

1. ダイアログ ボックスに戻ります。 もう一度 **[接続テスト]** を選択し、 **[作成]** を選択して、リンクされたサービスをデプロイします。

1. データセットの作成画面で、 **[ファイル パス]** フィールドの下にファイルが配置されている場所を入力します。 このチュートリアルでは、moviesDB.csv ファイルはコンテナー **sample-data** に配置されています。 ファイルにはヘッダーが含まれているので、 **[先頭の行を見出しとして使用]** チェック ボックスをオンにします。 ストレージ内のファイルからヘッダー スキーマを直接インポートするには、 **[From connection/store]\(接続/ストアから\)** を選択します。 完了したら、 **[OK]** をクリックします。

    ![ソース パスを示すスクリーンショット。](media/tutorial-data-flow-private/source-file-path.png)

1. デバッグ クラスターが起動している場合は、ソース変換の **[データのプレビュー]** タブに移動し、 **[更新]** を選択して、データのスナップショットを取得します。 データ プレビューを使用すると、変換が正しく構成されていることを確認できます。

    ![[データのプレビュー] タブを示すスクリーンショット。](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>マネージド プライベート エンドポイントを作成する

前述の接続をテストした際にハイパーリンクを使用しなかった場合は、パスに従います。 次に、作成したリンクされたサービスに接続するマネージド プライベート エンドポイントを作成する必要があります。

1. **[管理]** タブに移動します。

   > [!NOTE]
   > Data Factory インスタンスでは、そのすべてで **[管理]** タブを使用できない場合があります。 表示されない場合は、 **[作成者]**  >  **[接続]**  >  **[プライベート エンドポイント]** を選択して、プライベート エンドポイントにアクセスできます。

1. **[マネージド プライベート エンドポイント]** セクションに移動します。
1. **[マネージド プライベート エンドポイント]** で、 **[+ 新規]** を選択します。

    ![[マネージド プライベート エンドポイント] の [新規] ボタンを示すスクリーンショット。](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. リストから **[Azure Data Lake Storage Gen2]** タイルを選択し、 **[続行]** を選択します。
1. 作成したストレージ アカウントの名前を入力します。
1. **［作成］** を選択します
1. 数秒後に、作成されたプライベート リンクに承認が必要であることが表示されます。
1. 作成したプライベート エンドポイントを選択します。 ストレージ アカウント レベルでプライベート エンドポイントを承認できるハイパーリンクが表示されます。

    ![[マネージド プライベート エンドポイント] ウィンドウを示すスクリーンショット。](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>ストレージ アカウントでプライベート リンクを承認する

1. ストレージ アカウントで、 **[設定]** セクションの **[プライベート エンドポイント接続]** に移動します。

1. 作成したプライベート エンドポイントのチェック ボックスをオンにし、 **[承認]** を選択します。

    ![プライベート エンドポイントの [承認] ボタンを示すスクリーンショット。](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. 説明を追加し、 **[はい]** を選択します。
1. Data Factory の **[管理]** タブにある **[マネージド プライベート エンドポイント]** セクションに戻ります。
1. 約 1 分後に、プライベート エンドポイントの承認の旨が表示されます。

### <a name="add-the-filter-transformation"></a>フィルター変換を追加する

1. データ フロー キャンバス上のソース ノードの横にあるプラス アイコンを選択して、新しい変換を追加します。 最初に追加する変換は、 **[フィルター]** です。

    ![フィルターの追加を示すスクリーンショット。](media/tutorial-data-flow-private/add-filter.png)
1. フィルター変換に **FilterYears** という名前を付けます。 **[フィルター適用]** の横にある式ボックスを選択して、式ビルダーを開きます。 ここでフィルター条件を指定します。

    ![FilterYears を示すスクリーンショット。](media/tutorial-data-flow-private/filter-years.png)
1. データ フローの式ビルダーでは、さまざまな変換で使用する式を対話形式で作成できます。 式には、組み込み関数、入力スキーマの列、ユーザー定義のパラメーターを含めることができます。 式の作成方法の詳細については、[データ フローの式ビルダー](./concepts-data-flow-expression-builder.md)に関するページを参照してください。

    * このチュートリアルでは、1910 年から 2000 年の間に公開された、ジャンルがコメディの映画をフィルター処理します。 現在、年は文字列になっているため、```toInteger()``` 関数を使用して整数に変換する必要があります。 以上演算子 (>=) と以下演算子 (<=) を使用して、年のリテラル値 1910 と 2000 に対する比較を行います。 これらの式を and (&&) 演算子を使用して結合します。 式は次のようになります。

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * コメディ映画を見つけるには、```rlike()``` 関数を使用して、ジャンル列でパターン 'Comedy' を検索します。 rlike 式と年の比較を結合して、次を取得します。

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * デバッグ クラスターがアクティブになっている場合は、 **[更新]** を選択して使用された入力と比較した式の出力を表示して、ロジックを確認できます。 データ フローの式言語を使用してこのロジックを実現する方法に対する正解は複数あります。

        ![フィルター式を示すスクリーンショット。](media/tutorial-data-flow-private/filter-expression.png)

    * 式の操作が完了したら、 **[Save and Finish]\(保存して終了する\)** を選択します。

1. フィルターが正しく機能していることを確認するには、**データ プレビュー** をフェッチします。

    ![フィルター処理されたデータのプレビューを示すスクリーンショット。](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>集計変換を追加する

1. 次に追加する変換は、 **[Schema modifier]\(スキーマ修飾子\)** の下にある **[集計]** 変換です。

    ![集計の追加を示すスクリーンショット。](media/tutorial-data-flow-private/add-aggregate.png)
1. 集計変換に **AggregateComedyRating** という名前を付けます。 **[グループ化]** タブで、ドロップダウン ボックスから **[year]** を選択し、映画の公開年ごとに集計をグループ化します。

    ![集計グループを示すスクリーンショット。](media/tutorial-data-flow-private/group-by-year.png)
1. **[集計]** タブに移動します。左側のテキスト ボックスで、集計列に **AverageComedyRating** という名前を付けます。 式ビルダーを使用して集計式を入力するには、右側の式ボックスを選択します。

    ![集計列の名前を示すスクリーンショット。](media/tutorial-data-flow-private/name-column.png)
1. 列 **[Rating]** の平均値を取得するには、```avg()``` 集計関数を使用します。 **[Rating]** は文字列で、```avg()``` で受け入れられるのは数値入力なので、```toInteger()``` 関数を使用して値を数値に変換する必要があります。 式は次のようになります。

    ```avg(toInteger(Rating))```

1. 完了したら、 **[Save and Finish]\(保存して終了する\)** を選択します。

    ![集計の保存を示すスクリーンショット。](media/tutorial-data-flow-private/save-aggregate.png)
1. 変換出力を表示するには、 **[Data Preview]\(データのプレビュー\)** タブに移動します。 **year** と **AverageComedyRating** の 2 つの列だけがあることに注目してください。

### <a name="add-the-sink-transformation"></a>シンク変換を追加する

1. 次に、 **[Destination]\(変換先\)** の下で **[シンク]** 変換を追加します。

    ![シンクの追加を示すスクリーンショット。](media/tutorial-data-flow-private/add-sink.png)
1. シンクに **Sink** という名前を付けます。 **[新規]** を選択して、シンク データセットを作成します。

    ![シンクの作成を示すスクリーンショット。](media/tutorial-data-flow-private/create-sink.png)
1. **[新しいデータセット]** ページで **[Azure Data Lake Storage Gen2]** を選択し、 **[続行]** を選択します。

1. **[形式の選択]** ページで、 **[DelimitedText]** を選択してから、 **[続行]** を選択します。

1. シンク データセットに **MoviesSink** という名前を付けます。 リンクされたサービスでは、ソース変換用に作成したものと同じ **ADLSGen2** のリンクされたサービスを選択します。 データの書き込み先となる出力フォルダーを入力します。 このチュートリアルでは、コンテナー **sample-data** 内のフォルダー **output** に書き込んでいます。 フォルダーは、事前に存在している必要はなく、動的に作成することができます。 **[先頭の行を見出しとして使用]** チェック ボックスを選択し、 **[スキーマのインポート]** で **[なし]** を選択します。 **[OK]** を選択します。

    ![シンク パスを示すスクリーンショット。](media/tutorial-data-flow-private/sink-file-path.png)

これで、データ フローの構築が完了しました。 これをパイプラインで実行する準備ができました。

## <a name="run-and-monitor-the-data-flow"></a>データ フローの実行と監視

パイプラインを発行する前にデバッグすることができます。 この手順では、データ フロー パイプラインのデバッグ実行をトリガーします。 データのプレビューではデータが書き込まれませんが、デバッグ実行ではシンクの変換先にデータが書き込まれます。

1. パイプライン キャンバスに移動します。 **[デバッグ]** を選択して、デバッグ実行をトリガーします。

1. データ フロー アクティビティのパイプライン デバッグでは、アクティブなデバッグ クラスターが使用されますが、それでも初期化には少なくとも 1 分かかります。 進行状況は **[出力]** タブで追跡することができます。実行が正常に完了したら、眼鏡のアイコンを選択して実行の詳細を確認します。

1. 詳細ページには、各変換手順で使用した行の数と所要時間が表示されます。

    ![監視実行を示すスクリーンショット。](media/tutorial-data-flow-private/run-details.png)
1. 変換を選択すると、データの列とパーティション分割に関する詳細情報が表示されます。

このチュートリアルに正しく従った場合は、シンク フォルダーに 83 行と 2 列が書き込まれているはずです。 BLOB ストレージをチェックすることで、データが正しいことを確認できます。

## <a name="summary"></a>まとめ

このチュートリアルでは、Data Factory UI を使用して、[Data Factory マネージド仮想ネットワーク](managed-virtual-network-private-endpoint.md)内でマッピング データ フローを使用して Data Lake Storage Gen2 ソースから Data Lake Storage Gen2 シンク (どちらも、選択したネットワークへの接続のみを許可) にデータをコピーして変換するパイプラインを作成しました。