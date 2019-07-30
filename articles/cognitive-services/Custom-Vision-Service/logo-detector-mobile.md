---
title: チュートリアル:カスタム ロゴ検出器を使用して Azure サービスを認識する - Custom Vision
titlesuffix: Azure Cognitive Services
description: このチュートリアルでは、ロゴ検出シナリオの一部として Azure Custom Vision を使用するサンプル アプリについて見ていきます。 Custom Vision を他のコンポーネントと共に使用してエンド ツー エンドのアプリケーションを提供する方法を学習します。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b4b10591069b71a4e70769f5bdcd6149768c5007
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604017"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>チュートリアル:写真内にある Azure サービスのロゴを認識する

このチュートリアルでは、より大きなシナリオの一部として Azure Custom Vision を使用するサンプル アプリについて見ていきます。 モバイル プラットフォーム用の Xamarin.Forms アプリである AI Visual Provision アプリは、Azure サービスのロゴの写真を分析した後、実際のサービスをユーザーの Azure アカウントにデプロイします。 ここでは、このアプリで Custom Vision を他のコンポーネントと共に使用して便利なエンド ツー エンドのアプリケーションを提供する方法を学習します。 自分でアプリのシナリオ全体を実行するか、設定の Custom Vision 部分のみを完成させて、アプリでこの部分がどのように使用されているかを調べることができます。

ここでは、次の操作方法について説明します。

> [!div class="checklist"]
> - Azure サービスのロゴを認識するカスタム オブジェクト検出器を作成する。
> - アプリを Azure Computer Vision と Custom Vision に接続する。
> - アプリから Azure サービスをデプロイするための Azure サービス プリンシパル アカウントを作成する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017 以降](https://www.visualstudio.com/downloads/)
- Visual Studio 向けの Xamarin ワークロード ([Xamarin のインストール](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows)に関するページを参照してください)
- Visual Studio 向けの iOS または Android のエミュレーター
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (オプション)

## <a name="get-the-source-code"></a>ソース コードを入手する

提供されている Web アプリを使用する場合は、GitHub 上の [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) リポジトリからアプリのソース コードを複製またはダウンロードします。 Visual Studio で *Source/VisualProvision.sln* ファイルを開きます。 後で、アプリを実行できるように、一部のプロジェクト ファイルを編集します。

## <a name="create-an-object-detector"></a>オブジェクト検出器を作成する

[Custom Vision Web サイト](https://customvision.ai/)にサインインし、新しいプロジェクトを作成します。 オブジェクト検出プロジェクトを指定し、Logo ドメインを使用します。これにより、このサービスにおいてロゴ検出用に最適化されたアルゴリズムが使用されます。 

![Chrome ブラウザーに表示された Custom Vision Web サイトの新しいプロジェクト ウィンドウ](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

次に、Azure サービスのロゴの画像をアップロードして、手動でタグ付けすることで、ロゴ検出アルゴリズムをトレーニングします。 AIVisualProvision リポジトリには、開発者が使用できるトレーニング画像のセットが含まれています。 Web サイトで、 **[Training Images]\(トレーニング画像\)** タブの **[Add images]\(画像の追加\)** を選択します。次に、リポジトリの **Documents/Images/Training_DataSet** フォルダーに移動します。 各画像内のロゴに手動でタグを付ける必要があるため、このプロジェクトをテストするだけの場合は画像のサブセットのみをアップロードしてもかまいません。 使用する予定のタグごとに、少なくとも 15 個のインスタンスをアップロードします。

トレーニング画像をアップロードしたら、ディスプレイ上の最初の 1 つを選択します。 タグ付けウィンドウが表示されます。 各画像内の各ロゴに対して四角形を描画し、タグを割り当てます。 

![Custom Vision Web サイトでのロゴのタグ付け](media/azure-logo-tutorial/tag-logos.png)

このアプリは、特定のタグ文字列で機能するように構成されています。 *Source\VisualProvision\Services\Recognition\RecognitionService.cs* ファイル内の定義を参照してください。

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

1 つの画像にタグを付けたら、右へ移動して次の画像にタグを付けます。 完了したら、タグ付けウィンドウを閉じます。

## <a name="train-the-object-detector"></a>オブジェクト検出器をトレーニングする

左側のウィンドウで、 **[Tags]\(タグ\)** スイッチを **[Tagged]\(タグ付き\)** に設定し、画像を表示します。 次に、モデルをトレーニングするために、ページ上部にある緑色のボタンを選択します。 このアルゴリズムによって、新しい画像内の同じタグを認識するようにトレーニングされます。 さらに、既存の画像のいくつかでモデルがテストされ、精度スコアが生成されます。

![Custom Vision Web サイトの [Training Images]\(トレーニング画像\) タブ。このスクリーンショットでは、トレーニング ボタンが枠で囲まれている](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>予測 URL を取得する

モデルのトレーニングが完了したら、それをアプリに統合する準備が整ったことになります。 エンドポイント URL (アプリでクエリが実行されるモデルのアドレス) と (アプリに予測要求へのアクセスを許可するための) 予測キーを取得する必要があります。 **[Performance]\(パフォーマンス\)** タブで、ページの上部にある **[Prediction URL]\(予測 URL\)** を選択します。

![URL アドレスと API キーを示す Prediction API ウィンドウが表示されている Custom Vision Web サイト](media/azure-logo-tutorial/cusvis-endpoint.png)

画像ファイルの URL と **Prediction-Key** の値を *Source\VisualProvision\AppSettings.cs* ファイル内の適切なフィールドにコピーします。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Custom Vision の使用方法を調べる

*Source/VisualProvision/Services/Recognition/CustomVisionService.cs* ファイルを開き、Custom Vision キーとエンドポイント URL がアプリでどのように使用されているかを確認します。 **PredictImageContentsAsync** メソッドは、画像ファイルのバイト ストリームを (非同期タスク管理用の) キャンセル トークンと共に受け取ると、Custom Vision の予測 API を呼び出して、その予測の結果を返します。 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

この結果は **PredictionResult** インスタンスの形式になっていて、これ自体に **Prediction** インスタンスのリストが含まれます。 **Prediction** には、検出されたタグと、その画像内の境界ボックスの位置が含まれます。

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

アプリによるこのデータの処理方法について学ぶ場合は、**GetResourcesAsync** メソッドから始めてください。 このメソッドは、*Source/VisualProvision/Services/Recognition/RecognitionService.cs* ファイルで定義されています。  

## <a name="add-computer-vision"></a>Computer Vision を追加する

このチュートリアルの Custom Vision 部分が完了しました。 このアプリを実行するには、Computer Vision サービスも統合する必要があります。 このアプリでは、Computer Vision のテキスト認識機能を使用して、ロゴの検出プロセスが補完されます。 Azure のロゴは、その外観 "*または*" その近くに印刷されたテキストによって認識されます。 Computer Vision は、Custom Vision モデルとは異なり、画像や動画に対して特定の操作を実行するように事前にトレーニングされています。

Computer Vision サービスに登録して、キーとエンドポイント URL を取得します。 この手順に関してサポートが必要な場合は、「[サブスクリプション キーを取得する方法](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe)」を参照してください。

![[クイック スタート] メニューが選択された、Azure portal の Computer Vision サービス。 キーへのリンクと、API エンドポイント URL が枠線で囲まれている](media/azure-logo-tutorial/comvis-keys.png)

次に、*Source\VisualProvision\AppSettings.cs* ファイルを開き、`ComputerVisionEndpoint` および `ComputerVisionKey` 変数に適切な値を入力します。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

このアプリでは、Azure サブスクリプションにサービスをデプロイするために、Azure サービス プリンシパル アカウントが必要です。 サービス プリンシパルを使用すると、ロールベースのアクセス制御を使用して特定のアクセス許可をアプリに委任できます。 詳細については、[サービス プリンシパルのガイド](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals)を参照してください。

サービス プリンシパルは、ここで示すように、Azure Cloud Shell または Azure CLI を使用して作成できます。 開始するには、サインインして、使用するサブスクリプションを選択します。

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

次に、サービス プリンシパルを作成します (このプロセスは、完了するまでにしばらく時間がかかる場合があります)。

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

正常に完了すると、必要な資格情報を含む、次の JSON 出力が表示されます。

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

`clientId` と `tenantId` の値を書き留めておきます。 これらを *Source\VisualProvision\AppSettings.cs* ファイルの適切なフィールドに追加します。

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>アプリの実行

これで、以下に対するアクセス権がアプリに付与されました。

- トレーニング済みの Custom Vision モデル
- Computer Vision サービス
- サービス プリンシパル アカウント

アプリを実行するには、次の手順に従います。

1. Visual Studio ソリューション エクスプローラーで、**VisualProvision.Android** プロジェクトまたは **VisualProvision.iOS** プロジェクトを選択します。 メイン ツール バーのドロップダウン メニューで、対応するエミュレーターまたは接続されているモバイル デバイスを選択します。 その後、アプリを実行します。

    > [!NOTE]
    > iOS エミュレーターを実行するには、MacOS デバイスが必要になります。

1. 最初の画面で、サービス プリンシパル クライアント ID、テナント ID、およびパスワードを入力します。 **[Login]\(ログイン\)** を選択します。

    > [!NOTE]
    > 一部のエミュレーターでは、この手順で **[Login]\(ログイン\)** ボタンがアクティブにならない場合があります。 その場合はアプリを停止し、*Source/VisualProvision/Pages/LoginPage.xaml* ファイルを開きます。**LOGIN BUTTON** というラベルの付いた `Button` 要素を見つけて、次の行を削除し、アプリを再実行します。
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![サービス プリンシパルの資格情報のフィールドが表示されているアプリの画面](media/azure-logo-tutorial/app-credentials.png)

1. 次の画面で、ドロップダウン メニューから自分の Azure サブスクリプションを選択します (このメニューには、サービス プリンシパルでアクセスできるすべてのサブスクリプションが含まれているはずです)。 **[Continue]\(続行\)** を選択します。 この時点で、デバイスのカメラと写真ストレージへのアクセスを許可するように求めるメッセージがアプリに表示される場合があります。 アクセス許可を付与します。

    ![ターゲット Azure サブスクリプションのドロップダウン フィールドが表示されているアプリの画面](media/azure-logo-tutorial/app-az-subscription.png)


1. デバイスのカメラがアクティブになります。 トレーニングしたいずれかの Azure サービス ロゴの写真を撮影します。 デプロイ ウィンドウで、新しいサービスのリージョンとリソース グループを選択するように求められます (Azure portal からデプロイする場合と同様に)。 

    ![Azure ロゴが含まれている 2 枚の紙片に焦点を合わせたスマートフォンのカメラ画面](media/azure-logo-tutorial/app-camera-capture.png)

    ![デプロイ リージョンとリソース グループのフィールドが表示されているアプリの画面](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このシナリオのすべての手順を実行し、アプリを使用して Azure サービスを自分のアカウントにデプロイした場合は、[Azure portal](https://ms.portal.azure.com/) に移動します。 そこで、使用しないサービスを取り消します。

今後 Custom Vision を使用して独自のオブジェクト検出プロジェクトを作成する予定がある場合、このチュートリアルで作成したロゴ検出プロジェクトを削除したくなることがあるかもしれません。 Custom Vision の無料試用版では、利用できるプロジェクトは 2 つだけです。 ロゴ検出プロジェクトを削除するには、[Custom Vision Web サイト](https://customvision.ai)で **[Projects]\(プロジェクト\)** を開き、 **[My New Project]\(自分用の新しいプロジェクト\)** のごみ箱アイコンを選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Custom Vision サービスを使用してモバイル カメラの画像内のロゴを検出する、フル機能の Xamarin.Forms アプリを設定し、詳しく調べました。 次に、自分のアプリ用に作成する Custom Vision モデルを強力かつ正確なモデルにできるように、Custom Vision モデルを構築するためのベスト プラクティスを学習してください。

> [!div class="nextstepaction"]
> [分類子を改善する方法](getting-started-improving-your-classifier.md)
