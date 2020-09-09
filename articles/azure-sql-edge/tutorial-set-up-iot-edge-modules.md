---
title: Azure SQL Edge での IoT Edge モジュールの設定
description: 鉄鉱石の不純物を予測する、この 3 部構成の Azure SQL Edge チュートリアルの第 2 部では、IoT Edge のモジュールと接続を設定します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235034"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge のモジュールと接続を設定する

Azure SQL Edge で鉄鉱石の不純物を予測する、この 3 部構成チュートリアルの第 2 部では、IoT Edge の次のモジュールを設定します。

- Azure SQL Edge
- データ ジェネレーター IoT Edge モジュール

## <a name="create-azure-stream-analytics-module"></a>Azure Stream Analytics モジュールの作成

このチュートリアルで使用する Azure Stream Analytics モジュールを作成します。 SQL Edge でのストリーミング ジョブの使用の詳細については、[SQL Edge でのストリーミング ジョブの使用](stream-analytics.md)に関するページを参照してください。

Edge として設定されたホスティング環境で、Azure Stream Analytics ジョブが作成されたら、チュートリアルの入力と出力を設定します。

1. **入力**を作成するには、 **[+ ストリーム入力の追加]** をクリックします。 [詳細] セクションに次の情報を入力します。

   フィールド|値
   -----|-----
   イベント シリアル化形式|JSON
   エンコード|UTF-8
   イベントの圧縮タイプ|なし

2. **出力**を作成するには、 **[+ 追加]** をクリックし、[SQL Database] を選択します。 [詳細] セクションに次の情報を入力します。

   > [!NOTE]
   > このセクションで指定したパスワードは、SQL Edge モジュールを「**Azure SQL Edge モジュールのデプロイ**」セクションでデプロイするときに SQL SA パスワードとして指定する必要があります。

   フィールド|値
   -----|-----
   データベース|IronOreSilicaPrediction
   サーバー名|tcp:.,1433
   ユーザー名|sa
   Password|強力なパスワードを指定
   テーブル|IronOreMeasurements1

3. **[クエリ]** セクションに移動し、次のようにクエリを設定します。

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. **[Configure]\(構成\)** で、 **[Publish]\(発行\)** を選択し、 **[Publish]\(発行\)** ボタンを選択します。 SQL Database Edge モジュールで使用するための SAS URI を保存します。

## <a name="specify-container-registry-credentials"></a>コンテナー レジストリの資格情報の指定

モジュール イメージをホストするコンテナー レジストリに対する資格情報を指定する必要があります。 これは、リソース グループに作成されているコンテナー レジストリで見つかります。 **[アクセス キー]** セクションに移動します。 次のフィールドをメモします。

- レジストリ名
- ログイン サーバー
- ユーザー名
- Password

ここで、IoT Edge モジュールにコンテナーの資格情報を指定します。

1. リソース グループに作成された IoT ハブに移動します。

2. **[IoT Edge]** セクションの **[デバイスの自動管理]** で **[デバイス ID]** をクリックします。 このチュートリアルでは、ID は `IronOrePredictionDevice` です。

3. **[モジュールの設定]** セクションを選択します。

4. **[コンテナー レジストリの資格情報]** で、次の値を入力します。

   _フィールド_|_Value_
   -------|-------
   名前|レジストリ名
   Address|ログイン サーバー
   [ユーザー名]|ユーザー名
   Password|Password
  
## <a name="deploy-the-data-generator-module"></a>データ ジェネレーター モジュールのデプロイ

1. **[IoT Edge モジュール]** セクションで、 **[+ 追加]** をクリックし、 **[IoT Edge モジュール]** を選択します。

2. IoT Edge モジュール名とイメージ URI を指定します。
   イメージ URI は、リソース グループのコンテナー レジストリにあります。 **[サービス]** の下にある **[リポジトリ]** セクションを選択します。 このチュートリアルでは、`silicaprediction` という名前のリポジトリを選択します。 適切なタグを選択します。 イメージの URI の形式は次のとおりです。

   *<コンテナー レジストリのログイン サーバー>* / *<リポジトリ名>* : *<タグ名>*

   次に例を示します。

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. **[追加]** をクリックします。

## <a name="deploy-the-azure-sql-edge-module"></a>Azure SQL Edge モジュールのデプロイ

1. 「[Azure SQL Edge (プレビュー) をデプロイする](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal)」の手順に従って、Azure SQL Edge モジュールをデプロイします。

2. **[モジュールの設定]** ページの **[Specify Route]\(ルートの指定\)** で、モジュールから IoT Edge ハブへの通信のためのルートを次のように指定します。 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   次に例を示します。

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. **モジュール ツイン**の設定では、チュートリアルで前に保存した関連する SAS URL で SQLPackage と ASAJonInfo を更新してください。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>次の手順

- [ONNX を使用して Azure SQL Edge に ML モデルをデプロイする](tutorial-run-ml-model-on-sql-edge.md)
