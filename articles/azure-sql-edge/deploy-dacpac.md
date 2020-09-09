---
title: SQL Database DAC パッケージの使用 - Azure SQL Edge (プレビュー)
description: Azure SQL Edge での dacpac の使用について説明します (プレビュー)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233273"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>SQL Edge での SQL Database DAC パッケージ

Azure SQL Edge (プレビュー) は、IoT およびエッジのデプロイに対応するよう最適化されたリレーショナル データベース エンジンです。 これは業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されています。 SQL Server の業界をリードするリレーショナル データベース管理機能と共に、Azure SQL Edge では、リアルタイム分析や複雑なイベント処理のための組み込みのストリーミング機能が提供されます。

また、Azure SQL Edge には、SqlPackage.exe のネイティブ実装が用意されています。これにより、SQL Edge のデプロイ時に [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) パッケージをデプロイできます。 SQL Edge モジュールの `module twin's desired properties` オプションによって公開されている SqlPackage パラメーターを使用して、SQL Database dacpac を SQL Edge にデプロイできます。

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

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge で SQL Database DAC パッケージを使用する

SQL Edge で SQL Database DAC パッケージ (*.dacpac) を使用するには、次の手順のようにします。

1. SQL Database DAC パッケージを作成または抽出します。 既存の SQL Server データベースに対して DAC パッケージを生成する方法については、「[データベースからの DAC の抽出](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)」を参照してください。

2. *.dacpac を zip 圧縮し、Azure Blob ストレージ アカウントにアップロードします。 Azure Blob ストレージへのファイルのアップロードの詳細については、「[Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。

3. Azure portal を使用して、zip ファイルの共有アクセス署名を生成します。 詳細については、[Shared Access Signature (SAS) を使用したアクセスの委任](../storage/common/storage-sas-overview.md)に関するページを参照してください。

4. DAC パッケージの共有アクセス URI が含まれるように、SQL Edge モジュール構成を更新します。 SQL Edge モジュールを更新するには、次の手順のようにします。

    1. Azure portal で、IoT Hub のデプロイに移動します。

    2. 左側のウィンドウで、 **[IoT Edge]** を選択します。

    3. **[IoT Edge]** ページで、SQL Edge モジュールがデプロイされている IoT Edge を見つけて選択します。

    4. **[IoT Edge デバイス]** のデバイス ページで、 **[Set Module]\(モジュールの設定\)** を選択します。

    5. **[Set Module]\(モジュールの設定\)** ページで、SQL Edge モジュールに対して **[Configure]\(構成\)** を選択します。

    6. **[IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\)** ウィンドウで、 **[Set module twin's desired properties]\(モジュール ツインの必要なプロパティの設定\)** を選択します。 次の例に示すように、`SQLPackage` オプションの URI が含まれるように、必要なプロパティを更新します。

        > [!NOTE]
        > 次の JSON の SAS URI は単なる例です。 この URI をデプロイの実際の URI に置き換えます。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. **[保存]** を選択します。

    8. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** を選択します。

    9. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** 、 **[送信]** の順に選択します。

5. モジュールの更新後、DAC パッケージ ファイルがダウンロードおよび解凍され、SQL Edge インスタンスにデプロイされます。

Azure SQL Edge コンテナーを再起動するたびに、*.dacpac ファイル パッケージがダウンロードされて、変更が評価されます。 dacpac ファイルの新しいバージョンが検出された場合、変更が SQL Edge のデータベースにデプロイされます。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用した SQL Edge のデプロイ](deploy-portal.md)
- [データをストリーム配信する](stream-data.md)
- [SQL Edge (プレビュー) での ONNX を使用した機械学習と AI](onnx-overview.md)
