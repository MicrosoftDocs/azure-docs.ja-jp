---
title: チュートリアル - Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザー
description: このチュートリアルでは、Visual Studio を使用して Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザーを作成する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018209"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>チュートリアル:Azure Stream Analytics 用のカスタム .NET 逆シリアライザー

Azure Stream Analytics では、[3 つのデータ形式が組み込みでサポートされています](stream-analytics-parsing-json.md)。JSON、CSV、Avro です。 カスタム .NET 逆シリアライザーを使用すると、クラウド ジョブとエッジ ジョブの両方について、その他の形式 ([プロトコル バッファー](https://developers.google.com/protocol-buffers/)、[Bond](https://github.com/Microsoft/bond)、およびその他のユーザー定義形式など) からデータを読み取ることができます。

このチュートリアルでは、Visual Studio を使用して Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザーを作成する方法について説明します。 Visual Studio Code で .NET 逆シリアライザーを作成する方法については、[Visual Studio Code での Azure Stream Analytics ジョブに対応した .NET 逆シリアライザーの作成](visual-studio-code-custom-deserializer.md)に関する記事をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プロトコル バッファー用のカスタム逆シリアライザーを作成する。
> * Visual Studio で Azure Stream Analytics ジョブを作成する。
> * カスタム逆シリアライザーを使用するように Stream Analytics ジョブを構成する。
> * Stream Analytics ジョブをローカルで実行して、カスタム逆シリアライザーをテストおよびデバッグする。


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* [Visual Studio 2019 (推奨)](https://www.visualstudio.com/downloads/) または [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/) をインストールします。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。 

* [Visual Studio 用の Stream Analytics ツールをインストール](stream-analytics-tools-for-visual-studio-install.md)するか、最新バージョンに更新します。 

* Visual Studio で **Cloud Explorer** を開き、Azure サブスクリプションにサインインします。

* Azure Storage アカウントでコンテナーを作成します。
作成したコンテナーは、Stream Analytics ジョブに関連する資産を格納するために使用されます。 既存のコンテナーを含むストレージ アカウントが既にある場合は、それを使用できます。 ない場合は、[新しいコンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md)必要があります。

## <a name="create-a-custom-deserializer"></a>カスタム逆シリアライザーを作成する

1. Visual Studio を開き、 **[ファイル] > [新規作成] > [プロジェクト]** の順に選択します。 **Stream Analytics** を検索し、**[Azure Stream Analytics Custom Deserializer Project (.NET)]\(Azure Stream Analytics カスタム逆シリアライザー プロジェクト (.NET)\)** を選択します。 プロジェクトに、**Protobuf Deserializer** のような名前を付けます。

   ![Visual Studio .NET Standard クラス ライブラリ プロジェクトを作成する](./media/custom-deserializer/create-dotnet-library-project.png)

2. ソリューション エクスプローラーで **Protobuf Deserializer** プロジェクトを右クリックし、メニューから **[NuGet パッケージの管理]** を選択します。 次に、**Microsoft.Azure.StreamAnalytics** と **Google.Protobuf** NuGet パッケージをインストールします。

3. [MessageBodyProto クラス](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs)と [MessageBodyDeserializer クラス](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)をプロジェクトに追加します。

4. **Protobuf Deserializer** プロジェクトをビルドします。

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics プロジェクトを追加する

1. ソリューション エクスプローラーで **Protobuf Deserializer** ソリューションを右クリックし、**[追加] > [新しいプロジェクト]** の順に選択します。 **[Azure Stream Analytics] > [Stream Analytics]** で、**[Azure Stream Analytics アプリケーション]** を選択します。 **ProtobufCloudDeserializer** という名前を付け、**[OK]** を選択します。 

2. **ProtobufCloudDeserializer** Azure Stream Analytics プロジェクトで、**[参照]** を右クリックします。 **[プロジェクト]** の下に、**Protobuf Deserializer** を追加します。 自動的に設定されます。

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics ジョブを構成する

1. **JobConfig.json** をダブルクリックします。 次の設定を除き、既定の構成を使用します。

   |設定|推奨値|
   |-------|---------------|
   |グローバル ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |グローバル ストレージ設定のサブスクリプション| <お客様のサブスクリプション>|
   |グローバル ストレージ設定のストレージ アカウント| <お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |カスタム コード ストレージ設定のストレージ アカウント|<お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のコンテナー|<お客様のストレージ コンテナー>|

2. **[入力]** の下で、**[Input.json]** をダブルクリックします。 次の設定を除き、既定の構成を使用します。

   |設定|推奨値|
   |-------|---------------|
   |source|Blob Storage|
   |リソース|現在のアカウントからデータ ソースを選択します|
   |サブスクリプション|<お客様のサブスクリプション>|
   |ストレージ アカウント|<お客様のストレージ アカウント>|
   |コンテナー|<お客様のストレージ コンテナー>|
   |イベントのシリアル化の形式|その他 (Protobuf、XML、専用...)|
   |リソース|ASA プロジェクト参照または分離コードから読み込みます|
   |CSharp アセンブリ名|ProtobufDeserializer.dll|
   |Class Name (クラス名)|MessageBodyProto.MessageBodyDeserializer|
   |イベントの圧縮タイプ|なし|

3. 次のクエリを **Script.asaql** ファイルに追加します。

   ```sql
   SELECT * FROM Input
   ```

4. [サンプルの protobuf 入力ファイル](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)をダウンロードします。 **[入力]** フォルダーで **[Input.json]** を右クリックし、**[ローカル入力の追加]** を選択します。 次に、**[local_Input.json]** をダブルクリックし、次の設定を構成します。

   |設定|推奨値|
   |-------|---------------|
   |入力のエイリアス|入力|
   |ソースの種類|データ ストリーム|
   |イベントのシリアル化の形式|その他 (Protobuf、XML、専用...)|
   |CSharp アセンブリ名|ProtobufDeserializer.dll|
   |Class Name (クラス名)|MessageBodyProto.MessageBodyDeserializer|
   |ローカル入力ファイルのパス|<ダウンロードしたサンプル protobuf 入力ファイルのファイル パス>|

## <a name="execute-the-stream-analytics-job"></a>Stream Analytics ジョブを実行する

1. **Script.asaql** を開き、**[ローカルで実行]** を選択します。

2. **[Stream Analytics ローカル実行の結果]** で結果を確認します。

Stream Analytics ジョブのカスタム逆シリアライザーが正常に実装されました。 このチュートリアルでは、カスタム逆シリアライザーをローカルでテストしました。 実際のデータの場合は、入力と出力を適切に構成してください。 その後、Visual Studio から Azure にジョブを送信し、実装したカスタム逆シリアライザーを使用してクラウドでジョブを実行します。

## <a name="debug-your-deserializer"></a>逆シリアライザーをデバッグする

標準の.NET コードをデバッグするのと同じ方法で、.NET 逆シリアライザーをローカルでデバッグできます。

1. **ProtobufCloudDeserializer** プロジェクト名を右クリックし、[スタートアップ プロジェクト] として設定します。

2. 関数にブレークポイントを追加します。

3. **F5** キーを押してデバッグを開始します。 期待どおりに、プログラムはブレークポイントで停止します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このチュートリアルで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで **[リソース グループ]** を選択し、作成したリソースの名前を選択します。  

2. リソース グループのページで **[削除]** を選択し、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プロトコル バッファー入力のシリアル化に対して、カスタム .NET 逆シリアライザーを実装する方法について学習しました。 カスタム逆シリアライザーの作成の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics ジョブ用の異なる .NET 逆シリアライザーを作成する](custom-deserializer-examples.md)