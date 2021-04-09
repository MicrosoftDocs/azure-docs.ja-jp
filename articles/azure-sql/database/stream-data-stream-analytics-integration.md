---
title: Azure Stream Analytics 統合を使用してデータをストリーム配信する (プレビュー)
description: Azure Stream Analytics 統合を使用して Azure SQL Database にデータをストリーム配信します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454028"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Azure Stream Analytics 統合を使用して Azure SQL Database にデータをストリーム配信する (プレビュー)

ユーザーは、Azure SQL Database 内のデータベースから直接リアルタイムのストリーミング データをテーブルに取り込み、それを処理、表示、分析できるようになりました。 それを [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) を使用して Azure portal で行います。 このエクスペリエンスにより、コネクテッド カー、リモート監視、不正行為の検出など、さまざまなシナリオが可能になります。 Azure portal では、イベント ソース (イベント ハブ/IoT ハブ) を選択し、受信したリアルタイム イベントを表示して、イベントを格納するテーブルを選択できます。 また、ポータルで Azure Stream Analytics クエリ言語クエリを作成して、受信イベントを変換し、それを選択したテーブルに格納することもできます。 この新しいエントリ ポイントは、Stream Analytics に既に存在する作成と構成のエクスペリエンスに追加されます。 このエクスペリエンスはデータベースのコンテキストから開始されるため、Stream Analytics ジョブをすばやく設定し、Azure SQL Database 内のデータベースと Stream Analytics エクスペリエンスの間をシームレスに移動できます。

![Stream Analytics のフロー](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>主な利点

- 最低限のコンテキスト切り替え:ポータルの Azure SQL Database のデータベースから開始し、他のサービスに切り替えることなくリアルタイム データをテーブルに取り込むことができます。
- ステップ数の削減:データベースとテーブルのコンテキストが Stream Analytics ジョブを事前構成するために使用されます。
- プレビュー データによる使いやすさの向上:イベント ソース (イベント ハブ/IoT ハブ) から受信したデータを選択したテーブルのコンテキストでプレビューできます

> [!IMPORTANT]
> Azure Stream Analytics ジョブは、Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse Analytics に出力できます。 詳細については、「[出力](../../stream-analytics/stream-analytics-define-outputs.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- Azure SQL Database 内のデータベース。 詳細については、[Azure SQL Database での単一データベースの作成](single-database-create-quickstart.md)に関する記事を参照してください。
- コンピューターがサーバーに接続するためのファイアウォール規則。 詳細については、[サーバーレベルのファイアウォール規則の作成](firewall-create-server-level-portal-quickstart.md)に関する記事を参照してください。

## <a name="configure-stream-analytics-integration"></a>Stream Analytics 統合を構成する

1. Azure portal にサインインします。
2. ストリーミング データを取り込むデータベースに移動します。 **[Stream analytics (preview)]\(Stream Analytics (プレビュー)\)** を選択します。

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. このデータベースへのストリーミング データの取り込み開始するには、 **[作成]** を選択し、ストリーミング ジョブに名前を付けて、 **[Next: Input]\(次へ: 入力\)** を選択します。

    ![Stream Analytics ジョブの基本を構成します](./media/stream-data-stream-analytics-integration/create-job.png)

4. イベント ソースの詳細を入力し、 **[Next:Output]\(次へ: 出力\)** を選択します。

   - **[Input type]\(入力の種類\)** :イベントハブ/IoT ハブ
   - **入力のエイリアス**:イベント ソースを識別する名前を入力してください
   - **サブスクリプション**:Azure SQL Database のサブスクリプションと同じです
   - **[イベント ハブの名前空間]** :名前空間の名前
   - **[イベント ハブ名]** : 選択した名前空間内のイベント ハブの名前
   - **[イベント ハブ ポリシー名]** \(既定では新規作成):ポリシー名を指定します
   - **[イベント ハブ コンシューマー グループ]** \(既定では新規作成):コンシューマー グループ名を指定します  

      ここで作成する新しい各 Azure Stream Analytics ジョブに対してコンシューマー グループとポリシーを作成することをお勧めします。 コンシューマー グループでは、同時実行の閲覧者は 5 人しか許可されないので、ジョブごとに専用のコンシューマー グループを指定することで、その上限を超過した場合に発生するエラーを回避します。 専用ポリシーを利用すると、他のリソースに影響を及ぼさずに、キーを交代で利用したりアクセス許可を取り消したりできます。

     ![Stream Analytics ジョブ出力を構成します](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. ストリーミング データを取り込むテーブルを選択します。 完了したら、 **[作成]** を選択します。

   - **[ユーザー名]** 、 **[パスワード]** :SQL サーバー認証用の資格情報を入力します。 **[検証]** を選択します。
   - **テーブル**: **[新規作成]** または **[既存のデータを使用する]** を選択します。 このフローでは、 **[作成]** を選択します。 これにより、Stream Analytics ジョブの開始時に新しいテーブルが作成されます。

     ![Stream Analytics ジョブを作成する](./media/stream-data-stream-analytics-integration/create.png)

6. クエリ ページが開き、次の詳細が表示されます。

   - データの取り込み元となる **[入力]** (入力イベント ソース)  
   - 変換されたデータを格納する **[出力]** \(出力テーブル)
   - SELECT ステートメントを使用するサンプルの [SAQL クエリ](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md)。
   - **[入力のプレビュー]** :入力イベント ソースからの最新の受信データのスナップショットを表示します。
     - お使いのデータでのシリアル化の種類が、自動検出されます (JSON/CSV)。 JSON/CSV/AVRO に手動で変更することも可能です。
     - 表形式または未加工の形式で受信データをプレビューできます。
     - 表示されたデータが最新でない場合、 **[更新]** を選択して最新のイベントを表示します。
     - 特定の時間範囲の受信イベントに対してクエリをテストするには、 **[時間範囲の選択]** を選択します。
     - サンプルの JSON/CSV ファイルをアップロードしてクエリをテストするには **[サンプル入力のアップロード]** を選択します。 SAQL クエリのテストの詳細については、「[サンプル データを利用した Azure Stream Analytics ジョブのテスト](../../stream-analytics/stream-analytics-test-query.md)」を参照してください。

     ![クエリのテスト](./media/stream-data-stream-analytics-integration/test-query.png)

   - **[テスト結果]** : **[Test query]\(クエリのテスト\)** を選択すると、ストリーミング クエリの結果が表示されます

     ![テスト結果](./media/stream-data-stream-analytics-integration/test-results.png)

   - **[Test results schema]\(テスト結果のスキーマ\)** :テスト後のストリーミング クエリの結果のスキーマを表示します。 テスト結果のスキーマが出力スキーマと一致していることを確認します。

     ![テスト結果のスキーマ](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **[Output Schema]\(出力スキーマ\)** :これには、手順 5 で選択したテーブルのスキーマが含まれます (新規または既存)。

      - [新規作成]:手順 5 でこのオプションを選択した場合は、ストリーミング ジョブを開始するまでスキーマが表示されません。 新しいテーブルを作成する場合は、適切なテーブル インデックスを選択します。 テーブル インデックス作成の詳細については、「[クラスター化インデックスと非クラスター化インデックスの説明](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)」を参照してください。
      - [既存のものを使用]:手順 5. でこのオプションを選択した場合は、選択したテーブルのスキーマが表示されます。

7. クエリの作成およびテストを完了したら、 **[クエリの保存]** を選択します。 **[Start Stream Analytics job]\(Stream Analytics ジョブを開始する\)** を選択して、変換されたデータを SQL テーブルに取り込む操作を開始します。 次のフィールドを確定したら、ジョブを **開始** します。
   - **[出力開始時刻]** :これにより、ジョブの最初の出力時刻が定義されます。  
     - [今すぐ]:ジョブはすぐに開始され、新しい受信データを処理します。
     - カスタム:ジョブはすぐに開始されますが、特定の時点 (過去または将来の可能性があります) のデータを処理します。 詳細については、「[Azure Stream Analytics ジョブを開始する方法](../../stream-analytics/start-job.md)」を参照してください。
   - **[ストリーミング ユニット]** :Azure Stream Analytics の価格は、このサービスに入力されるデータを処理するために必要なストリーミング ユニットの数に基づいて決まります。 詳細については、「[Azure Stream Analytics の価格](https://azure.microsoft.com/pricing/details/stream-analytics/)」をご覧ください。
   - **[出力データのエラー処理]** :  
     - Retry: エラーが発生すると、Azure Stream Analytics により、書き込みが成功するまで無期限でイベントの書き込みが試行されます。 再試行にはタイムアウトがありません。 最終的には、再試行しているイベントによって後続のすべてのイベントの処理が妨げられます。 このオプションは既定の出力エラー処理ポリシーです。
     - ドロップ:Azure Stream Analytics により、データ変換エラーを起こした出力イベントがドロップされます。 ドロップされたイベントを、後で再処理のために復旧することはできません。 (ネットワーク エラーなど) 一時的なエラーはすべて、出力エラー処理ポリシーの構成に関係なく、再試行されます。
   - **[SQL Database output settings]\(SQL Database 出力の設定\)** :ご自分の以前のクエリ ステップのパーティション構成を継承し、テーブルへの複数のライターによる完全な並列トポロジを有効にするためのオプションです。 詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](../../stream-analytics/stream-analytics-sql-output-perf.md)」を参照してください。
   - **[Max batch count]\(最大バッチ カウント\)** :各一括挿入トランザクションで送信されるレコード数の推奨される上限です。  
    出力エラー処理の詳細については、「[Azure Stream Analytics の出力エラーポリシー](../../stream-analytics/stream-analytics-output-error-policy.md)」を参照してください。  

     ![ジョブの開始](./media/stream-data-stream-analytics-integration/start-job.png)

8. ジョブを開始すると、実行中のジョブが一覧に表示され、次のアクションを実行できるようになります。
   - **[Start/stop the job]\(ジョブの開始/停止\)** :ジョブが実行されている場合は、ジョブを停止できます。 ジョブが停止している場合は、ジョブを開始できます。
   - **[Edit job]\(ジョブの編集\)** :クエリを編集できます。 ジョブにさらに多くの変更を加える場合 (入力/出力を追加するなど)、Stream Analytics でジョブを開きます。 ジョブの実行中は、編集オプションは無効になっています。
   - **[Preview output table]\(出力テーブルのプレビュー\)** :SQL クエリ エディターでテーブルをプレビューできます。
   - **[Open in Stream Analytics]\(Stream Analytics で開く\)** :Stream Analytics でジョブを開いて、ジョブの詳細の監視およびデバッグを表示します。

     ![Stream Analytics ジョブ](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>次のステップ

- [Azure Stream Analytics のドキュメント](../../stream-analytics/index.yml)
- [Azure Stream Analytics のソリューション パターン](../../stream-analytics/stream-analytics-solution-patterns.md)
