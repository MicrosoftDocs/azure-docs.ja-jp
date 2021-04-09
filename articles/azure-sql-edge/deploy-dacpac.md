---
title: SQL Database DACPAC および BACPAC パッケージの使用 - Azure SQL Edge
description: Azure SQL Edge での dacpac および bacpac の使用について説明します
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392250"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge での SQL Database DACPAC および BACPAC パッケージ

Azure SQL Edge は、IoT およびエッジのデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、Microsoft SQL データベース エンジンの最新バージョンに基づいて構築されています。 SQL Server の業界をリードするリレーショナル データベース管理機能と共に、Azure SQL Edge では、リアルタイム分析や複雑なイベント処理のための組み込みのストリーミング機能が提供されます。

Azure SQL Edge には、[SQL Database DACPAC および BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) パッケージを SQL Edge のデプロイ中またはデプロイ後に配置できるネイティブのメカニズムが用意されています。

SQL Database dacpac および bacpac パッケージは、`MSSQL_PACKAGE` 環境変数を使用して SQL Edge にデプロイできます。 環境変数は、次のいずれかを使用して構成できます。  
- dacpac および bacpac ファイルが格納されている、SQL コンテナー内のローカル フォルダーの場所。 このフォルダーは、マウント ポイントまたはデータ ボリューム コンテナーのいずれかを使用してホスト ボリュームにマップできます。 
- dacpac または bacpac ファイルにマッピングする SQL コンテナー内のローカル ファイル パス。 このファイル パスは、マウント ポイントまたはデータ ボリューム コンテナーのいずれかを使用してホスト ボリュームにマップできます。 
- dacpac または bacpac ファイルを含む zip ファイルにマッピングされる、SQL コンテナー内のローカル ファイル パス。 このファイル パスは、マウント ポイントまたはデータ ボリューム コンテナーのいずれかを使用してホスト ボリュームにマップできます。 
- dacpac および bacpac ファイルを含む zip ファイルへの Azure BLOB SAS URL。
- dacpac または bacpac ファイルへの Azure BLOB SAS URL。 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge で SQL Database DAC パッケージを使用する

Azure Blob Storage と zip ファイル使用して SQL Database DAC パッケージ `(*.dacpac)` または BACPAC ファイル `(*.bacpac)` をデプロイ (またはインポート) するには、次の手順に従います。 

1. 以下に説明するメカニズムを使用して、DAC パッケージを作成または抽出するか、Bacpac ファイルをエクスポートします。 
    - SQL Database DAC パッケージを作成または抽出します。 既存の SQL Server データベースに対して DAC パッケージを生成する方法については、「[データベースからの DAC の抽出](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)」を参照してください。
    - デプロイされた DAC パッケージまたはデータベースのエクスポート。 既存の SQL Server データベースに対して bacpac ファイルを生成する方法については、「[データ層アプリケーションのエクスポート](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/)」を参照してください。

2. `*.dacpac` または `*.bacpac` ファイルを zip 圧縮し、Azure BLOB ストレージ アカウントにアップロードします。 Azure Blob ストレージへのファイルのアップロードの詳細については、「[Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。

3. Azure portal を使用して、zip ファイルの共有アクセス署名を生成します。 詳細については、[Shared Access Signature (SAS) を使用したアクセスの委任](../storage/common/storage-sas-overview.md)に関するページを参照してください。

4. DAC パッケージの共有アクセス URI が含まれるように、SQL Edge モジュール構成を更新します。 SQL Edge モジュールを更新するには、次の手順のようにします。

    1. Azure portal で、IoT Hub のデプロイに移動します。

    2. 左側のウィンドウで、 **[IoT Edge]** を選択します。

    3. **[IoT Edge]** ページで、SQL Edge モジュールがデプロイされている IoT Edge を見つけて選択します。

    4. **[IoT Edge デバイス]** のデバイス ページで、 **[Set Module]\(モジュールの設定\)** を選択します。

    5. **[モジュールの設定]** ページで、Azure SQL Edge モジュールをクリックします。

    6. **[IoT Edge モジュールの更新]** ペインで、 **[環境変数]** を選択します。 `MSSQL_PACKAGE` 環境変数を追加し、環境変数の値として上記の手順 3 で生成した SAS URL を指定します。 

    7. **[Update]\(更新\)** を選択します。

    8. **[モジュールの設定]** ページで、 **[確認と作成]** を選択します。

    9. **[モジュールの設定]** ページで、 **[作成]** を選択します。

5. モジュールの更新後、パッケージ ファイルがダウンロードおよび解凍され、SQL Edge インスタンスにデプロイされます。

Azure SQL Edge コンテナーを再起動するたびに、SQL Edge によって、zip 圧縮されたファイル パッケージがダウンロードされて、変更の有無が評価されます。 dacpac ファイルの新しいバージョンが検出された場合、変更が SQL Edge のデータベースにデプロイされます。

## <a name="known-issue"></a>既知の問題

一部の DACPAC または BACPAC のデプロイ中に、ユーザーにコマンド タイムアウトが発生し、dacpac デプロイ操作が失敗する場合があります。 この問題が発生した場合は、SQLPackage.exe (または SQL クライアント ツール) を使用して DACPAC または BACPAC を手動で適用してください。 

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用した SQL Edge のデプロイ](deploy-portal.md)。
- [データをストリーム配信する](stream-data.md)
- [SQL Edge での ONNX を使用した機械学習と AI](onnx-overview.md)