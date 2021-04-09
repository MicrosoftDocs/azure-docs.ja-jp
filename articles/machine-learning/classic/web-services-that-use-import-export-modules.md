---
title: 'ML Studio (classic): Web サービスでデータをインポートまたはエクスポートする - Azure'
description: データのインポート モジュールとデータのエクスポート モジュールを使用して、Web サービスとデータを送受信する方法について説明します。
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 4824b7a4233bc65d521e1c6ded7d1ea276b2a929
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520563"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>データのインポートおよびデータのエクスポート モジュールを使用する Azure Machine Learning Studio (クラシック) Web サービスをデプロイする

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


予測実験を作成するときには通常、Web サービスの入力と出力を追加します。 実験を展開するときに、コンシューマーはこれらの入力と出力を介して Web サービスとのデータの送受信を行うことができます。 一部のアプリケーションでは、コンシューマーのデータはデータ フィードから利用できるか、または Azure BLOB ストレージなどの外部データ ソースに既に存在しています。 このような場合、Web サービスの入力と出力を使用してデータを読み書きする必要はありません。 代わりに、バッチ実行サービス (BES) を使用して、データのインポート モジュールを使用してデータ ソースからデータを読み取り、データのエクスポート モジュールを使用して、スコア付けの結果を他のデータの場所に書き込むことができます。

データのインポート モジュールとエクスポート モジュールは、HTTP を使用する Web URL、Hive クエリ、Azure SQL Database 内のデータベース、Azure Table Storage、Azure Blob Storage、提供されたデータ フィード、または SQL Server データベースなどのさまざまなデータの場所に対してデータの読み取りと書き込みを行うことができます。

このトピックでは "Sample 5:Train, Test, Evaluate for Binary Classification: Adult Dataset" サンプルを使用しており、このデータセットは censusdata という名前の Azure SQL テーブルに既に読み込まれていることを前提としています。

## <a name="create-the-training-experiment"></a>トレーニング実験を作成する
"Sample 5:Train, Test, Evaluate for Binary Classification: Adult Dataset" サンプルを開くと、これはサンプル Adult Census Income Binary Classification データセットを使用しています。 キャンバスの実験は以下の図のようになります。

![実験の初期構成です。](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Azure SQL テーブルからデータを読み取るには:

1. データセット モジュールを削除します。
2. コンポーネントの検索ボックスで、「インポート」と入力します。
3. 結果の一覧から、 *データのインポート* モジュールを実験キャンバスに追加します。
4. *データのインポート* モジュールの出力と *見つからないデータのクリーンアップ* モジュールの入力を接続します。
5. [プロパティ] ウィンドウで、 **[Azure SQL Database]** in the **[Azure SQL Database]** を選択します。
6. **[データベース サーバー名]** 、 **[データベース名]** 、 **[ユーザー名]** 、 **[パスワード]** の各フィールドに、データベースの適切な情報を入力します。
7. データベースのクエリ フィールドに、次のクエリを入力します。

    ```tsql
     select [age],
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
    ```
8. 実験キャンバスの下部で、 **[実行]** をクリックします。

## <a name="create-the-predictive-experiment"></a>予測実験を作成する
次に、Web サービスの展開元の予測実験を設定します。

1. 実験キャンバスの下部で、 **[Web サービスの設定]** をクリックして **[予測 Web サービス] \(推奨)** を選択します。
2. 予測実験から *Web サービス入力モジュール* と *Web サービス出力モジュール* を削除します。
3. コンポーネントの検索ボックスで、「エクスポート」と入力します。
4. 結果の一覧から、 *データのエクスポート* モジュールを実験キャンバスに追加します。
5. *モデルのスコア付け* モジュールの出力と *データのエクスポート* モジュールの入力を接続します。
6. [プロパティ] ウィンドウで、データの移動先ドロップダウンから **[Azure SQL Database]** を選択します。
7. **[データベース サーバー名]** 、 **[データベース名]** 、 **[サーバー ユーザー アカウント名]** 、 **[サーバー ユーザー アカウント パスワード]** の各フィールドに、データベースの適切な情報を入力します。
8. **[保存する列のコンマ区切りリスト]** フィールドで、「スコア付けラベル」と入力します。
9. **[データ テーブル名] フィールド** で、「dbo.ScoredLabels」と入力します。 テーブルが存在しない場合、テーブルは実験を実行したとき、または Web サービスが呼び出されたときに作成されます。
10. **[データベース列のコンマ区切りリスト]** フィールドで、「スコア付けラベル」と入力します。

最終的な Web サービスを呼び出すアプリケーションを作成する場合は、実行時に別の入力クエリまたは変換先テーブルを指定します。 これらの入力と出力を構成するために、Web サービス パラメーターの機能を使用して *データのインポート* モジュールの *データソース* プロパティと *データのエクスポート* モード データの変換先プロパティを設定できます。  Web サービス パラメーターの詳細については、Cortana Intelligence と Machine Learning のブログにある [Azure Machine Learning Studio Web サービス パラメーターのエントリ](/archive/blogs/machinelearning/azureml-web-service-parameters)を参照してください。

クエリのインポートおよび変換先テーブルの Web サービス パラメーターを構成するには:

1. *データのインポート* モジュールの [プロパティ] ウィンドウで、 **[データベース クエリ]** フィールドの右上部にあるアイコンをクリックして、 **[Set as web service parameter]** を選択します。
2. *データのエクスポート* モジュールの [プロパティ] ウィンドウで、 **[データテーブル名]** フィールドの右上部にあるアイコンをクリックして、 **[Set as web service parameter]** を選択します。
3. *データのエクスポート* モジュールの [プロパティ] ウィンドウの下部にある **[Web サービス パラメーター]** セクションで、[データベース クエリ] をクリックし、そのクエリの名前を「Query」に変更します。
4. **[データ テーブル名]** をクリックし、名前を「**Table**」に変更します。

完了すると、実験は以下の図のようになります。

![最終的に実験は次のようになります。](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Web サービスとして予測実験をデプロイできるようになります。

## <a name="deploy-the-web-service"></a>Web サービスをデプロイする
従来の、または新しい Web サービスにデプロイすることができます。

### <a name="deploy-a-classic-web-service"></a>従来の Web サービスとしてデプロイする
従来の Web サービスとしてデプロイし、それを使用するアプリケーションを作成します。

1. 実験キャンバスの下部で、[実行] をクリックします。
2. 実行が完了したら、 **[Web サービスのデプロイ]** をクリックして、 **[Deploy Web Service [Classic] \(Web サービスのデプロイ [従来])]** を選択します。
3. Web サービス ダッシュボードで、API キーを見つけます。 この API キーをコピーして、後で使用できるように保存します。
4. **[既定のエンドポイント]** テーブルで、 **[バッチ実行]** リンクをクリックして API ヘルプ ページを開きます。
5. Visual Studio で、次のように選択して C# コンソール アプリケーションを作成します: **[新規]**  >  **[プロジェクト]**  >  **[Visual C#]**  >  **[Windows クラシック デスクトップ]**  >  **[コンソール アプリ (.NET Framework)]** 。
6. API ヘルプ ページで、ページ下部にある **[サンプル コード]** セクションを見つけます。
7. C# サンプル コードをコピーして Program.cs ファイルに貼り付け、BLOB ストレージへの参照をすべて削除します。
8. *apiKey* 変数の値を、以前に保存した API キーで更新します。
9. 要求の宣言を見つけて、*データのインポート* モジュールと *データのエクスポート* モジュールに渡される Web サービス パラメーターの値を更新します。 この場合は、元のクエリを使用しますが、新しいテーブル名を定義します。

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. アプリケーションを実行します。

実行が完了すると、新しいテーブルがスコア付けの結果を含むデータベースに追加されます。

### <a name="deploy-a-new-web-service"></a>新しい Web サービスのデプロイ

> [!NOTE]
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。

新しい Web サービスとしてデプロイし、それを使用するアプリケーションを作成します。

1. 実験キャンバスの下部で、 **[実行]** をクリックします。
2. 実行が完了したら、 **[Web サービスのデプロイ]** をクリックして、 **[Deploy Web Service [New] \(Web サービスのデプロイ [新規])]** を選択します。
3. [実験のデプロイ] ページで、Web サービスの名前を入力し、料金プランを選択して **[デプロイ]** をクリックします。
4. **[クイック スタート]** ページで **[使用]** をクリックします。
5. **[サンプル コード]** セクションで、 **[Batch]** をクリックします。
6. Visual Studio で、次のように選択して C# コンソール アプリケーションを作成します: **[新規]**  >  **[プロジェクト]**  >  **[Visual C#]**  >  **[Windows クラシック デスクトップ]**  >  **[コンソール アプリ (.NET Framework)]** 。
7. C# サンプル コードをコピーして Program.cs ファイルに貼り付けます。
8. *apiKey* 変数の値を、 **[Basic consumption info (基本的な実行情報)]** セクションにある **プライマリ キー** を使用して更新します。
9. *scoreRequest* 宣言を見つけて、*データのインポート* モジュールと *データのエクスポート* モジュールに渡される Web サービス パラメーターの値を更新します。 この場合は、元のクエリを使用しますが、新しいテーブル名を定義します。

    ```csharp
    var scoreRequest = new
    {
        Inputs = new Dictionary<string, StringTable>()
        {
        },
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable3" },
        }
    };
    ```
10. アプリケーションを実行します。