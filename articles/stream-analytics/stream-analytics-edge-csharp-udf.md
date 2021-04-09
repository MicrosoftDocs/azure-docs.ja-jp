---
title: チュートリアル - Visual Studio で Azure Stream Analytics ジョブ用の C# ユーザー定義関数を作成する (プレビュー)
description: このチュートリアルでは、Visual Studio で Stream Analytics ジョブ用の C# ユーザー定義関数を作成する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020470"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>チュートリアル:Azure Stream Analytics ジョブの C# ユーザー定義関数を記述する (プレビュー)

Visual Studio で作成した C# ユーザー定義関数 (UDF) を使用すると、独自の関数で Azure Stream Analytics のクエリ言語を拡張できます。 既存のコード (DLL を含む) を再利用し、算術演算ロジックや複雑なロジックを C# で使用することができます。 UDF は次の 3 つの方法で実装できます。Stream Analytics プロジェクト内の分離コード ファイル、ローカル C# プロジェクトからの UDF、ストレージ アカウントの既存パッケージからの UDF です。 このチュートリアルでは、分離コードの方法を使用して、基本的な C# 関数を実装します。 Stream Analytics ジョブの UDF 機能は現在プレビュー段階にあります。運用環境のワークロードでは使用しないでください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 分離コードを使用して C# のユーザー定義関数を作成する。
> * Stream Analytics のジョブをローカルでテストする。
> * ジョブを Azure に発行する。

## <a name="prerequisites"></a>前提条件

始める前に、以下の前提条件が完了していることを確認してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Visual Studio の Stream Analytics ツール](stream-analytics-tools-for-visual-studio-install.md)と、**Azure 開発** ワークロードまたは **データの保存と処理** ワークロードをインストールします。
* IoT Edge ジョブを構築している場合は、既存の [Stream Analytics Edge 開発ガイド](stream-analytics-tools-for-visual-studio-edge-jobs.md)を参照してください。

## <a name="create-a-container-in-your-azure-storage-account"></a>Azure ストレージ アカウントでコンテナーを作成する

作成したコンテナーは、コンパイルされた C# パッケージを格納するために使用されます。 Edge ジョブを作成した場合、このストレージ アカウントは IoT Edge デバイスへのパッケージのデプロイにも使用されます。 Stream Analytics ジョブごとに専用のコンテナーを使用します。 複数の Stream Analytics Edge ジョブで同じコンテナーを再利用することはサポートされていません。 既存のコンテナーを含むストレージ アカウントが既にある場合は、それを使用できます。 ない場合は、[新しいコンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md)必要があります。 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Visual Studio で Stream Analytics プロジェクトを作成する

1. Visual Studio を起動します。

2. **[ファイル] > [新規] > [プロジェクト]** を選択します。

3. 左側のテンプレートの一覧で **[Stream Analytics]** を選択し、 **[Azure Stream Analytics Edge アプリケーション]** または **[Azure Stream Analytics アプリケーション]** を選択します。

4.  プロジェクトの **名前**、**場所**、および **ソリューション名** を入力し、 **[OK]** を選択します。

    ![Visual Studio で Azure Stream Analytics Edge プロジェクトを作成する](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>アセンブリ パッケージのパスを構成する

1. Visual Studio を開き、**ソリューション エクスプローラー** に移動します。

2. ジョブ構成ファイル `EdgeJobConfig.json` をダブルクリックします。

3. **[User-Defined Code Configuration]\(ユーザー定義コードの構成\)** セクションを展開し、構成に次の推奨値を入力します。

   |**設定**|**推奨値**|
   |-------|---------------|
   |グローバル ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |グローバル ストレージ設定のサブスクリプション| <お客様のサブスクリプション>|
   |グローバル ストレージ設定のストレージ アカウント| <お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |カスタム コード ストレージ設定のストレージ アカウント|<お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のコンテナー|<お客様のストレージ コンテナー>|


## <a name="write-a-c-udf-with-codebehind"></a>分離コードを使用して C# の UDF を作成する
分離コード ファイルは、1 つの ASA クエリ スクリプトに関連付けられている C# ファイルです。 Visual Studio Tools は、自動的に分離コード ファイルを圧縮して、送信時に Azure ストレージ アカウントにアップロードします。 すべてのクラスを *public* として定義し、すべてのオブジェクトを *static public* として定義する必要があります。

1. **ソリューション エクスプローラー** で **Script.asql** を展開し、**Script.asaql.cs** 分離コード ファイルを探します。

2. コードを次のサンプルに置き換えます。

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>UDF を実装する

1. **ソリューション エクスプローラー** で、**Script.asaql** ファイルを開きます。

2. 既存のクエリを次のクエリに置き換えます。

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>ローカル テスト

1. [温度シミュレーター サンプル データ ファイル](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)をダウンロードします。

2. **ソリューション エクスプローラー** で、 **[入力]** を展開し、**Input.json** を右クリックして、 **[ローカル入力の追加]** を選択します。

   ![Visual Studio で Stream Analytics ジョブにローカル入力を追加する](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. ダウンロードしたサンプル データのローカル入力ファイル パスを指定し、 **[保存]** を選択します。

    ![Visual Studio での Stream Analytics ジョブに対するローカル入力構成](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. スクリプト エディターで **[ローカルで実行]** をクリックします。 ローカル実行で出力結果が正常に保存された後、任意のキーを押して、表形式で結果を表示します。 

    ![Visual Studio で Azure Stream Analytics ジョブをローカルに実行する](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. **[結果フォルダーを開く]** を選択して、JSON および CSV 形式で生ファイルを表示することもできます。

    ![Visual Studio でローカルな Azure Stream Analytics ジョブの結果を表示する](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>UDF をデバッグする
標準の C# コードをデバッグするのと同じ方法で、C# UDF をローカルにデバッグできます。 

1. C# 関数にブレークポイントを追加します。

    ![Visual Studio で Stream Analytics ユーザー定義関数にブレークポイントを追加する](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. **F5** キーを押してデバッグを開始します。 期待どおりに、プログラムはブレークポイントで停止します。

    ![Stream Analytics ユーザー定義関数のデバッグ結果を表示する](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>ジョブを Azure に発行する
ローカルでクエリをテストした後、スクリプト エディターで **[Azure に送信]** を選択して、ジョブを Azure に発行します。

![Visual Studio から Azure に Stream Analytics Edge ジョブを送信する](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>IoT Edge デバイスにデプロイする
Stream Analytics Edge ジョブを作成することを選択した場合に、これを IoT Edge モジュールとしてデプロイできるようになりました。 [IoT Edge クイック スタート](../iot-edge/quickstart.md)に従って、IoT ハブを作成し、IoT Edge デバイスを登録し、デバイスに IoT Edge ランタイムをインストールして開始します。 次に「[ジョブのデプロイ](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job)」チュートリアルに従って、Stream Analytics ジョブを IoT Edge モジュールとしてデプロイします。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、分離コードを使用して簡単な C# ユーザー定義関数を作成し、ジョブを Azure に発行し、Azure または IoT Edge デバイスにジョブをデプロイしました。 

Stream Analytics ジョブで C# のユーザー定義関数を使用する別の方法の詳細については、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics 用の C# 関数を作成する](stream-analytics-edge-csharp-udf-methods.md)