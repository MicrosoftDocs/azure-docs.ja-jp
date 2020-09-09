---
title: Azure SQL Edge (プレビュー) での Azure Stream Analytics Edge ジョブの使用
description: Azure SQL Edge (プレビュー) で Stream Analytics ジョブを使用する方法について説明します
keywords: SQL Edge、ストリーム分析、
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e323c56f22c3a7d32bda5afe03a3462d3263f0d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254074"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>SQL Edge での Azure Stream Analytics ジョブの使用

Azure SQL Edge (プレビュー) は、IoT およびエッジのデプロイに対応するよう最適化されたリレーショナル データベース エンジンです。 これは業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されています。 SQL Server の業界をリードするリレーショナル データベース管理機能と共に、Azure SQL Edge では、リアルタイム分析や複雑なイベント処理のための組み込みのストリーミング機能が提供されます。

Azure SQL Edge には、ストリーム分析ランタイムがネイティブで実装されています。 この実装により、Azure Stream Analytics エッジ ジョブを作成し、そのジョブを SQL Edge ストリーミング ジョブとしてデプロイすることができます。 SQL Edge モジュールの `module twin's desired properties` オプションによって公開されている ASAJobInfo パラメーターを使用して、Azure Stream Analytics ジョブを SQL Edge にデプロイできます。

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|フィールド | 説明 |
|------|-------------|
| SqlPackage | SQL Database DAC パッケージを含む *.zip ファイルの Azure Blob ストレージ URI。
| ASAJobInfo | ASA Edge ジョブの Azure Blob ストレージ URI。

## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge ジョブを作成する

1. Azure Portal にアクセスします。

2. 新しい **Azure Stream Analytics on IoT Edge** ジョブを作成します。 **Edge** をターゲットにするホスティング環境を選択します。

3. Azure Stream Analytics ジョブの入力と出力を定義します。 ここで設定する SQL 出力はそれぞれ、データベース内の 1 つのテーブルに関連付けられます。 複数のテーブルにデータをストリームする必要がある場合は、複数の SQL Database 出力を作成する必要があります。 異なるデータベースを指すように SQL 出力を構成できます。

    **入力**。 EdgeHub をエッジ ジョブの入力として選択し、リソース情報を提供します。

    **出力**。 SQL Database を出力として選択します。 **[Provide SQL Database settings manually]\(SQL データベース設定を手動で行う\)** を選択します。 データベースとテーブルの構成の詳細を提供します。

    |フィールド      | 説明 |
    |---------------|-------------|
    |出力エイリアス | 出力エイリアスの名前。|
    |データベース | データベースの名前です。 SQL Edge インスタンス上に存在するデータベースの有効な名前である必要があります。|
    |サーバー名 | SQL インスタンスの名前 (または IP アドレス) とポート番号の詳細。 SQL Edge のデプロイの場合、**tcp:.,1433** をサーバー名に使用できます。|
    |ユーザー名 | 以前に指定したデータベースへのデータ閲覧者とデータ作成者のアクセス権を持つ SQL サインイン アカウント。|
    |Password | 以前に指定した SQL サインイン アカウントのパスワード。|
    |テーブル | ストリーミング ジョブの出力対象となるテーブルの名前。|
    |パーティション分割の継承| 以前のクエリ ステップまたは入力のパーティション構成を継承できます。 このオプションを有効にすると、ディスクベースのテーブルに書き込んでいて、ジョブの完全並列トポロジがある場合にスループットの向上を期待できます。|
    |バッチ サイズ| すべての一括挿入トランザクションで送信されるレコードの最大数。|

    次に示すのは、入力/出力の構成例です。

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Azure Stream Analytics 用の SQL 出力アダプターの詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](../stream-analytics/stream-analytics-sql-output-perf.md)」を参照してください。

4. エッジ ジョブの ASA ジョブ クエリを定義します。 このクエリでは、定義済みの入力/出力エイリアスを、クエリの入力および出力の名前として使用する必要があります。 詳細については、[Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)を参照してください。

5. エッジ ジョブのストレージ アカウントの設定を行います。 ストレージ アカウントは、エッジ ジョブの発行ターゲットとして使用されます。

6. **[Configure]\(構成\)** で、 **[Publish]\(発行\)** を選択し、 **[Publish]\(発行\)** ボタンを選択します。 SQL Edge モジュールで使用するための SAS URI を保存します。

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Azure Stream Analytics Edge ジョブを SQL Edge にデプロイする

ストリーミング ジョブを SQL Edge モジュールにデプロイするには、前の手順のストリーミング ジョブの SAS URI が含まれるように SQL Edge モジュール構成を更新します。 SQL Edge モジュールを更新するには、次の手順を実行します。

1. Azure portal で、IoT Hub のデプロイに移動します。

2. 左側のウィンドウで、 **[IoT Edge]** を選択します。

3. **[IoT Edge]** ページで、SQL Edge モジュールがデプロイされている IoT Edge を見つけて選択します。

4. **[IoT Edge デバイス]** のデバイス ページで、 **[Set Module]\(モジュールの設定\)** を選択します。

5. **[Set Module]\(モジュールの設定\)** ページで、SQL Edge モジュールに対して **[Configure]\(構成\)** を選択します。

6. **[IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\)** ウィンドウで、 **[Set module twin's desired properties]\(モジュール ツインの必要なプロパティの設定\)** を選択します。 次の例に示すように、`ASAJobInfo` オプションの URI が含まれるように、必要なプロパティを更新します。

    > [!NOTE]
    > 次の JSON の SAS URI は単なる例です。 この URI をデプロイの実際の URI に置き換えます。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. **[保存]** を選択します。

8. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** を選択します。

9. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** 、 **[送信]** の順に選択します。

10. モジュールの更新後、Stream Analytics ジョブ ファイルがダウンロードおよび解凍され、SQL Edge インスタンスにデプロイされます。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用した SQL Edge のデプロイ](deploy-portal.md)。

- [データをストリーム配信する](stream-data.md)

- [SQL Edge (プレビュー) での ONNX を使用した機械学習と AI](onnx-overview.md)
