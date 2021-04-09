---
title: Visual Studio Code を使用して Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザーを作成する
description: このチュートリアルでは、Visual Studio Code を使用して Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザーを作成する方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013925"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Visual Studio Code 上で Azure Stream Analytics 用のカスタム .NET 逆シリアライザーを作成する

Azure Stream Analytics では、[3 つのデータ形式が組み込みでサポートされています](stream-analytics-parsing-json.md)。JSON、CSV、Avro です。 カスタム .NET 逆シリアライザーを使用すると、クラウド ジョブについて、その他の形式 ([プロトコル バッファー](https://developers.google.com/protocol-buffers/)、[Bond](https://github.com/Microsoft/bond)、およびその他のユーザー定義形式など) からデータを読み取ることができます。

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Visual Studio Code 上のカスタム .NET 逆シリアライザー

Visual Studio Code を使用して、Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザーを作成、テスト、デバッグできます。

### <a name="prerequisites"></a>[前提条件]

* [.NET Core SDK](https://dotnet.microsoft.com/download) をインストールし、Visual Studio Code を再起動します。

* この[クイックスタート](quick-create-visual-studio-code.md)を使用して、Visual Studio Code を使用して Stream Analytics ジョブを作成する方法を学習します。

### <a name="create-a-custom-deserializer"></a>カスタム逆シリアライザーを作成する

1. Visual Studio Code 上でターミナルを開き、次のコマンドを実行して、カスタム逆シリアライザー用の **ProtobufDeserializer** という名前の .NET クラス ライブラリを作成します。

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. **ProtobufDeserializer** プロジェクト ディレクトリに移動し、[Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) および [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet パッケージをインストールします。

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. [MessageBodyProto クラス](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs)と [MessageBodyDeserializer クラス](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)をプロジェクトに追加します。

4. **ProtobufDeserializer** プロジェクトをビルドします。

### <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics プロジェクトを追加する

1. Visual Studio Code を開き、**Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 次に「ASA」と入力し、 **[ASA: 新しいプロジェクトの作成]** を選択します。 **ProtobufCloudDeserializer** という名前を付けます。

### <a name="configure-a-stream-analytics-job"></a>Stream Analytics ジョブを構成する

1. **JobConfig.json** をダブルクリックします。 次の設定を除き、既定の構成を使用します。

   |設定|推奨値|
   |-------|---------------|
   |グローバル ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |グローバル ストレージ設定のサブスクリプション| <お客様のサブスクリプション>|
   |グローバル ストレージ設定のストレージ アカウント| <お客様のストレージ アカウント>|
   |CustomCodeStorage 設定のストレージ アカウント|<お客様のストレージ アカウント>|
   |CustomCodeStorage 設定のコンテナー|<お客様のストレージ コンテナー>|

2. **[入力]** フォルダーの下にある **input.json** を開きます。 **[Add live input]\(ライブ入力の追加\)** を選択し、Azure Data Lake Storage Gen2/Blob Storage から入力を追加し、 **[Select from your Azure subscription]\(自分の Azure サブスクリプションから選択\)** を選択します。 次の設定を除き、既定の構成を使用します。

   |設定|推奨値|
   |-------|---------------|
   |名前|入力|
   |サブスクリプション|<お客様のサブスクリプション>|
   |ストレージ アカウント|<お客様のストレージ アカウント>|
   |コンテナー|<お客様のストレージ コンテナー>|
   |シリアル化の種類|**[カスタム]** を選択します|
   |SerializationProjectPath|CodeLens から **[Choose library project path]\(ライブラリ プロジェクト パスの選択\)** を選択し、前のセクションで作成した **ProtobufDeserializer** ライブラリ プロジェクトを選択します。 **[プロジェクトのビルド]** を選択してプロジェクトをビルドします|
   |SerializationClassName|CodeLens から **[select deserialization class]\(逆シリアル化クラスの選択\)** を選択して、クラス名と DLL パスを自動設定します|
   |Class Name (クラス名)|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="カスタム逆シリアライザーの入力を追加します。":::

3. 次のクエリを **ProtobufCloudDeserializer.asaql** ファイルに追加します。

   ```sql
   SELECT * FROM Input
   ```

4. [サンプルの protobuf 入力ファイル](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)をダウンロードします。 **[入力]** フォルダー内で、**input.json** を右クリックし、 **[ローカル入力の追加]** を選択します。 **local_input1.json** をダブルクリックし、次の設定を除き、既定の構成を使用します。

   |設定|推奨値|
   |-------|---------------|
   |ローカル ファイル パスの選択|CodeLens をクリックして、<ダウンロードしたサンプル protobuf 入力ファイルのファイル パス> を選択します|

### <a name="execute-the-stream-analytics-job"></a>Stream Analytics ジョブを実行する

1. **ProtobufCloudDeserializer.asaql** を開き、CodeLens から **[ローカルで実行]** を選択し、ドロップダウン リストから **[ローカル入力の使用]** を選択します。

2. 右側のジョブ ダイア グラム ビュー内の **[結果]** タブで結果を確認します。 ジョブ ダイアグラム内のステップをクリックして、中間結果を表示することもできます。 詳細については、[ジョブ ダイアグラムを使用したローカルでのデバッグに関する記事](debug-locally-using-job-diagram-vs-code.md)を参照してください。

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="ローカル実行の結果を確認します。":::

Stream Analytics ジョブのカスタム逆シリアライザーが正常に実装されました。 このチュートリアルでは、ローカル入力データを使用してカスタム逆シリアライザーをローカルでテストしました。 [クラウド内のライブ データ入力を使用](visual-studio-code-local-run-live-input.md)して、このテストを行うこともできます。 クラウド内でこのジョブを実行するには、入力と出力を適切に構成する必要があります。 その後、Visual Studio Code から Azure にジョブを送信し、先ほど実装したカスタム逆シリアライザーを使用してクラウドでジョブを実行します。

### <a name="debug-your-deserializer"></a>逆シリアライザーをデバッグする

標準の.NET コードをデバッグするのと同じ方法で、.NET 逆シリアライザーをローカルでデバッグできます。 

1. .NET 関数にブレークポイントを追加します。

2. Visual Studio Code の[アクティビティ] バーにある **[実行]** をクリックし、 **[create a launch.json file]\(launch.json ファイルを作成\)** を選択します。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="起動ファイルを作成します。":::

   **ProtobufCloudDeserializer** を選択し、ドロップダウン リストから **[Azure Stream Analytics]** を選択します。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="起動ファイル 2 を作成します。":::

   **launch.json** ファイルを編集して、<ASAScript>.asaql を ProtobufCloudDeserializer.asaql に置き換えます。
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="起動ファイルを構成します。":::

3. **F5** キーを押してデバッグを開始します。 期待どおりに、プログラムはブレークポイントで停止します。 これは、ローカル入力データとライブ入力データの両方に対して機能します。

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="カスタム逆シリアライザーをデバッグします。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで **[リソース グループ]** を選択し、作成したリソースの名前を選択します。  

2. リソース グループのページで **[削除]** を選択し、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プロトコル バッファー入力のシリアル化に対して、カスタム .NET 逆シリアライザーを実装する方法について学習しました。 カスタム逆シリアライザーの作成の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> * [Azure Stream Analytics ジョブ用の異なる .NET 逆シリアライザーを作成する](custom-deserializer-examples.md)
> * [Visual Studio Code を使用してライブ入力で Azure Stream Analytics ジョブをローカルでテストする](visual-studio-code-local-run-live-input.md)