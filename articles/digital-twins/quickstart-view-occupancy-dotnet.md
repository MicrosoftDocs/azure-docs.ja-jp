---
title: 空き部屋を探す - Azure Digital Twins | Microsoft Docs
description: このクイック スタートでは、2 つの .NET Core サンプル アプリケーションを実行して、Azure Digital Twins の空間にシミュレートされたモーションと二酸化炭素のテレメトリを送信します。 目標は、クラウドで計算処理した後に、管理 API から空気の良い空き部屋を探すことです。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 08/16/2019
ms.author: alinast
ms.openlocfilehash: 40a98f915bc11ad17eae27596519cc78539fb2bb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640302"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>クイック スタート: Azure Digital Twins を使用して空き部屋を探す

Azure Digital Twins サービスを使用すると、物理環境のデジタル イメージを再作成できます。 その後、環境内のイベントによって通知を受け取り、それらのイベントへの応答をカスタマイズすることができます。

このクイック スタートでは、[1 組の .NET サンプル ](https://github.com/Azure-Samples/digital-twins-samples-csharp) を使用して、架空のオフィス ビルをデジタル化します。 それによって、そのビルの空き部屋を探す方法を示します。 Digital Twins を使用すると、多くのセンサーを環境に関連付けることができます。 また、シミュレートされた二酸化炭素センサーを利用して、空き部屋の空気の質が最適かどうかを調べることもできます。 サンプル アプリケーションの 1 つでは、このシナリオの視覚化に役立つランダムなセンサー データを生成します。

クイック スタートのセットアップについては、次のビデオで要約しています。

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>前提条件

1. Azure アカウントをお持ちでない場合は、開始する前に [ 無料アカウント ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

1. このクイック スタートで実行する 2 つのコンソール アプリケーションは、C# を使って記述されています。 開発用マシンに [.NET Core SDK バージョン 2.1.403 以降 ](https://www.microsoft.com/net/download) をインストールしてください。 .NET Core SDK がインストールされている場合は、開発用マシン上の C# の現在のバージョンを確認してください。 コマンド プロンプトで、`dotnet --version` を実行します。

1. [ サンプル C# プロジェクト ](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) をダウンロードします。 digital-twins-samples-csharp-master.zip アーカイブを展開します。

## <a name="create-a-digital-twins-instance"></a>Digital Twins インスタンスの作成

このセクションの手順に従って、[ ポータル ](https://portal.azure.com) で Digital Twins の新しいインスタンスを作成します。

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>アプリのアクセス許可の設定

このセクションでは、サンプル アプリケーションを Azure Active Directory (Azure AD) に登録して、これが Digital Twins インスタンスにアクセスできるようにします。 既に Azure AD アプリが登録されている場合は、それをサンプルに再利用します。 このセクションで説明されているとおりにそれが構成されていることを確認します。

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>アプリケーションのビルド

次の手順に従って、空間利用率アプリケーションをビルドします。

1. コマンド プロンプトを開きます。 `digital-twins-samples-csharp-master.zip` ファイルの展開先フォルダーに移動します。
1. `cd occupancy-quickstart/src` を実行します。
1. `dotnet restore` を実行します。
1. [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) を編集して、次の変数を更新します。
    - **ClientId**: 前のセクションでメモした、Azure AD アプリ登録のアプリケーション ID を入力します。
    - **Tenant**: Azure AD テナントのディレクトリ ID を入力します。これも、前のセクションでメモしたものです。
    - **BaseUrl**: Digital Twins インスタンスの、`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/` という形式の Management API URL。 この URL 内のプレースホルダーを、前のセクションにあるインスタンスの値に置き換えます。

## <a name="provision-graph"></a>グラフのプロビジョニング

この手順では、Digital Twins 空間グラフを次のようにプロビジョニングします。

- いくつかの空間。
- 1 つのデバイス。
- 2 つのセンサー。
- カスタム関数。
- 1 つのロール割り当て。

空間グラフは、[provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ファイルを使用してプロビジョニングします。

1. `dotnet run ProvisionSample` を実行します。

    >[!NOTE]
    >Device Login Azure CLI ツールを使用して、ユーザーを Azure AD に対して認証します。 [Microsoft のログイン ](https://microsoft.com/devicelogin) ページを使用して認証するには、ユーザーが特定のコードを入力する必要があります。 コードを入力した後、手順に従って認証します。 ユーザーは、ツールの実行中に認証する必要があります。

    >[!TIP]
    > この手順の実行時に、`EXIT: Unexpected error: The input is not a valid Base-64 string ...` というエラー メッセージが表示される場合は、変数が正しくコピーされていることを確認してください。

1. プロビジョニングの手順には数分かかることがあります。 また、Digital Twins インスタンス内の IoT ハブもプロビジョニングされます。 IoT ハブで Status=`Running` と表示されるまで、ループ処理が行われます。

    [![サンプルをプロビジョニングする](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png#lightbox)

1. 実行の最後に、デバイス シミュレーター サンプルで使用するデバイスの `ConnectionString` をコピーします。 下の図で示されている文字列のみをコピーします。

    [![接続文字列をコピーする](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

    >[!TIP]
    > [Azure Digital Twins グラフ ビューアー ](https://github.com/Azure/azure-digital-twins-graph-viewer) を使って、空間グラフを表示および変更できます。

## <a name="send-sensor-data"></a>センサー データの送信

次の手順に従って、センサー シミュレーター アプリケーションをビルドして実行します。

1. 新しいコマンド プロンプトを開きます。 ダウンロードしたプロジェクト (digital-twins-samples-csharp-master フォルダー内) に移動します。
1. `cd device-connectivity` を実行します。
1. `dotnet restore` を実行します。
1. [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) を編集し、前記の `ConnectionString` で **DeviceConnectionString** を更新します。
1. `dotnet run` を実行して、センサー データの送信を開始します。 次の図で示されているように、Digital Twins に送信されていることがわかります。

     [![デバイスの接続性](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. このシミュレーターを実行したままにし、次の手順の操作によって結果を並べて表示できるようにします。 このウィンドウには、Digital Twins に送信される、シミュレートされたセンサー データが表示されます。 次の手順では、リアルタイムでクエリを実行して、空気の良い空き部屋を探します。

    >[!TIP]
    > この手順の実行時に、`EXIT: Unexpected error: The input is not a valid Base-64 string ...` というエラー メッセージが表示される場合は、`DeviceConnectionString` が正しくコピーされていることを確認してください。

## <a name="find-available-spaces-with-fresh-air"></a>空気の良い空き空間の検索

このセンサー サンプルでは、2 つのセンサーのランダムなデータ値をシミュレートしています。 これらは、モーションと二酸化炭素です。 空気の良い空き部屋は、室内にだれも存在しないとサンプルで定義されています。 また、二酸化炭素濃度が 1,000 ppm 未満であるとも定義されています。 この条件が満たされない場合、その空間は利用できないか、空気の質が悪いことになります。

1. 前のプロビジョニング手順の実行に使用したコマンド プロンプトを開きます。
1. `dotnet run GetAvailableAndFreshSpaces` を実行します。
1. このコマンド プロンプトとセンサー データ コマンド プロンプトを並べて確認します。

    一方のコマンド プロンプトでは、シミュレートされたモーションと二酸化炭素のデータを 5 秒ごとに Digital Twins に送信します。 もう一方のコマンドではグラフをリアルタイムで読み取り、シミュレートされたランダムなデータに基づいて空気の良い空き部屋を探します。 最後に送信されたセンサー データに基づいて、ほぼリアルタイムで次のどちらかの状況が表示されます。
   - 空気の良い空き部屋。
   - 空いていないか、空気の質が悪い部屋。

     [![空気の良い空き空間の取得](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

このクイック スタートで何が起こったのかと、どの API が呼び出されたのかを理解するには、digital-twins-samples-csharp にあるコード ワークスペース プロジェクトを [Visual Studio Code](https://code.visualstudio.com/Download) で開いてください。 次のコマンドを使用します。

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

チュートリアルでは、コードの詳細について説明します。 構成データを変更する方法と呼び出される API について解説しています。 Management API シリーズの詳細については、Digital Twins Swagger ページを参照してください。

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | 置換後の文字列 |
| --- | --- |
| YOUR_INSTANCE_NAME | お客様の Digital Twins インスタンスの名前 |
| YOUR_LOCATION | インスタンスをホストするサーバーのリージョン |

または、便利な [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルでは、以下の方法の詳細について説明します。

- 空間利用の生産性を向上させるための施設管理者用アプリケーションを構築します。
- ビルをより効率的に運用します。

チュートリアルに進む場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 進まない場合は、このクイック スタートで作成したすべてのリソースを削除してください。

1. サンプル リポジトリのダウンロード時に作成したフォルダーを削除します。
1. [Azure portal](https://portal.azure.com) の左側のメニューで、 **[すべてのリソース]** を選択します。 次に、Digital Twins リソースを選択します。 **[すべてのリソース]** ウィンドウの上部にある **[削除]** を選択します。

    > [!TIP]
    > ご自分の Digital Twins インスタンスの削除で問題が発生していた場合は、サービス更新が修正と共にロールアウトされています。 ご自分のインスタンスの削除を再試行してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、簡単なシナリオを使用して、作業環境の良い部屋を探す方法について説明しました。 このシナリオの詳細な分析については、次のチュートリアルを参照してください。

>[!div class="nextstepaction"]
>[ チュートリアル: Azure Digital Twins をデプロイし、空間グラフを構成する ](tutorial-facilities-setup.md)
