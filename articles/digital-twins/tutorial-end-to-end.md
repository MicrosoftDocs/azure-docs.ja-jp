---
title: チュートリアル:エンド ツー エンドのソリューションを接続する"
titleSuffix: Azure Digital Twins
description: デバイス データによって駆動されるエンド ツー エンドの Azure Digital Twins ソリューションを構築するチュートリアル。
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: b22505d5152b005a054d36fafb965006d04b201e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401777"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>チュートリアル:エンド ツー エンドのソリューションを構築する

Azure Digital Twins インスタンスは、環境のライブ データによって駆動される完全なエンド ツー エンドのソリューションを設定するために、デバイスとデータの管理を目的とした他の Azure サービスに接続することができます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * Azure Digital Twins インスタンスを設定する
> * サンプル ビルディング シナリオを把握し、事前に作成されたコンポーネントをインスタンス化する
> * [Azure Functions](../azure-functions/functions-overview.md) アプリを使用して、[IoT Hub](../iot-hub/about-iot-hub.md) デバイスから、シミュレートしたテレメトリをデジタル ツインのプロパティにルーティングする
> * Azure Functions、エンドポイント、ルートを使用してデジタル ツインの通知を処理することにより、**ツイン グラフ**全体に変更を反映させる

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>ビルディングのシナリオの概要

このチュートリアルで使用するサンプル プロジェクトは、フロア、部屋、サーモスタット デバイスを含んだ実世界の**ビルディングのシナリオ**を表しています。 Azure Digital Twins インスタンスでは、これらのコンポーネントがデジタルで表現されます。ここでは、データの移動を促すために、Azure Digital Twins インスタンスを、[IoT Hub](../iot-hub/about-iot-hub.md) と [Event Grid](../event-grid/overview.md)、そして 2 つの [Azure Functions](../azure-functions/functions-overview.md) に接続しています。

次の図は、このシナリオ全体を表したものです。 

ここではまず、Azure Digital Twins インスタンス (図の**セクション A**) を作成します。その後、デジタル ツインへのテレメトリ データ フロー (**矢印 B**) を設定し、さらに、ツイン グラフ全体へのデータの反映 (**矢印 C**) を設定します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="ビルディング シナリオ全体を表す図。データがデバイスから IoT Hub へと流れ、Azure Functions を経て (矢印 B) Azure Digital Twins インスタンス (セクション A) に到達した後、Event Grid を介して別の Azure Functions に到達して処理 (矢印 C) されるようすを表しています":::

このシナリオに取り組むためには、先ほどダウンロードしておいた、あらかじめ作成されたサンプル アプリのコンポーネントとやり取りする必要があります。

ビルディング シナリオの *AdtSampleApp* サンプル アプリに実装されているコンポーネントは次のとおりです。
* デバイスの認証 
* [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) の使用例 (*CommandLoop.cs* 内で確認可能)
* Azure Digital Twins API を呼び出すためのコンソール インターフェイス
* *SampleClientApp* - サンプル Azure Digital Twins ソリューション
* *SampleFunctionsApp* - IoT Hub および Azure Digital Twins のイベントから受け取ったテレメトリの結果として Azure Digital Twins グラフを更新する Azure Functions アプリ

このサンプル プロジェクトには、対話型の承認コンポーネントも含まれています。 プロジェクトを起動するたびに、ブラウザー ウィンドウが開いて、自分の Azure アカウントへのログインを求められます。

### <a name="instantiate-the-pre-created-twin-graph"></a>事前に作成されたツイン グラフをインスタンス化する

まず、サンプル プロジェクトの *AdtSampleApp* ソリューションを使用して、エンド ツー エンド シナリオの Azure Digital Twins 部分 (**セクション A**) を構築します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="ビルディング シナリオの全体図からセクション A (Azure Digital Twins インスタンス) を抜粋して強調したもの":::

_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウで、ツール バーにあるこのボタンでプロジェクトを実行します。

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio のスタート ボタン (SampleClientApp プロジェクト)":::

コンソール ウィンドウが表示されて認証が行われ、コマンドを待つ状態となります。 このコンソールで、次のコマンドを実行して、サンプル Azure Digital Twins ソリューションをインスタンス化します。

> [!IMPORTANT]
> Azure Digital Twins インスタンスに既にデジタル ツインとリレーションシップが存在する場合、このコマンドを実行すると、それらは削除されて、サンプル シナリオのツインおよびリレーションシップに置き換えられます。

```cmd/sh
SetupBuildingScenario
```

このコマンドは、Azure Digital Twins に 3 つの[**デジタル ツイン**](concepts-twins-graph.md) インスタンス (*floor1* という名前のフロア、*room21* という名前の部屋、*thermostat67* という名前の温度センサー) を作成、接続すると共に、一連の確認メッセージを出力します。 これらのデジタル ツインは、実世界の環境に存在するであろうエンティティを表します。

それらがリレーションシップを介して接続され、以下の[**ツイン グラフ**](concepts-twins-graph.md)が形成されます。 ツイン グラフは、エンティティの相互作用や相互関係を含め、環境を全体として表します。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="floor1 に room21 が含まれ、room21 に thermostat67 が含まれていることを示すグラフ" border="false":::

作成されたツインは、次のコマンドを実行し、接続されている Azure Digital Twins インスタンスに、そこに含まれるすべてのデジタル ツインを照会することで確認できます。

```cmd/sh
Query
```

それが済んだら、プロジェクトの実行を停止してかまいません。 ただし、ソリューションは Visual Studio で開いたままにしてください。この後、チュートリアルが終わるまで使用することになります。

## <a name="set-up-the-sample-function-app"></a>サンプル関数アプリを設定する

次に、このチュートリアル全体を通してデータを処理する目的で使用される [Azure Functions アプリ](../azure-functions/functions-overview.md)を設定します。 この関数アプリ (*SampleFunctionsApp*) には、次の 2 つの関数が含まれています。
* *ProcessHubToDTEvents*: 受信した IoT Hub データを処理し、それに応じて Azure Digital Twins を更新します。
* *ProcessDTRoutedData*: デジタル ツインからのデータを処理し、それに応じて Azure Digital Twins 内の親ツインを更新します。

このセクションでは、事前に作成された関数アプリを発行し、Azure Active Directory (Azure AD) ID を割り当てることで、関数アプリから Azure Digital Twins に確実にアクセスできるようにします。 これらの手順が完了すると、このチュートリアルで以降、関数アプリに含まれている関数を使用できるようになります。 

_**AdtE2ESample**_ プロジェクトが開かれた状態の Visual Studio ウィンドウに戻ります。関数アプリは、_**SampleFunctionsApp**_ プロジェクト ファイルにあります。 *[ソリューション エクスプローラー]* ペインに表示されています。

### <a name="update-dependencies"></a>依存関係を更新する

アプリを発行する前に、依存関係が最新であることを確認するようにお勧めします。含まれているすべてのパッケージが最新バージョンであることを確認してください。

*[ソリューション エクスプローラー]* ペインで、 *[SampleFunctionsApp] > [依存関係]* の順に展開します。 *[パッケージ]* を右クリックし、 *[NuGet パッケージの管理]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: SampleFunctionsApp プロジェクトの [NuGet パッケージの管理]" border="false":::

これで、NuGet パッケージ マネージャーが開かれます。 *[更新]* タブを選択し、更新するパッケージがある場合は、 *[すべてのパッケージを選択]* ボックスをオンにします。 次に、 *[更新]* をクリックします。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: NuGet パッケージ マネージャーですべてのパッケージを更新するように選択する":::

### <a name="publish-the-app"></a>アプリの発行

_**AdtE2ESample**_ プロジェクトが開いた状態の Visual Studio ウィンドウに戻り、 *[ソリューション エクスプローラー]* ペインから _**[SampleFunctionsApp]**_ プロジェクト ファイルを右クリックして **[発行]** をクリックします。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: プロジェクトの発行":::

表示された *[発行]* ページで、既定のターゲット (**Azure**) を選択したままにし、 *[次へ]* をクリックします。 

特定のターゲットで **[Azure Function App (Windows)]** を選択し、 *[次へ]* をクリックします。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Visual Studio で Azure 関数を発行する: 特定のターゲット":::

*[Functions インスタンス]* ページで、該当するサブスクリプションを選択します。 これで、サブスクリプション内の "*リソース グループ*" がボックスに反映されます。

インスタンスのリソース グループを選択し、 *[+ 新しい Azure 関数の作成]* をクリックします。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Visual Studio で Azure 関数を発行する: Functions インスタンス (関数アプリの前)":::

*[Function App (Windows) - 新規作成]* ウィンドウの各フィールドに次のように入力します。
* **[名前]** は、Azure Functions アプリをホストするために Azure で使用される従量課金プランの名前です。 実際の関数を収容する関数アプリの名前としても使用されます。 一意の値を独自に選択するか、候補として表示された既定値をそのまま使用します。
* **[サブスクリプション]** は、使用するサブスクリプションと一致させます。 
* **[リソース グループ]** は、使用するリソース グループと合わせます。
* **[プランの種類]** は "*従量課金プラン*" のままにします。
* リソース グループの場所と一致する **[場所]** を選択します。
* *[新規]* リンクを使用して、新しい **Azure Storage** リソースを作成します。 場所の設定はリソース グループに合わせてください。それ以外は既定値のままにし、[OK] をクリックします。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Visual Studio で Azure 関数を発行する: Function App (Windows) - 新規作成":::

そのうえで **[Create]\(作成\)** を選択します。

*[Functions インスタンス]* ページが再び表示されます。今度は、リソース グループの下に新しい関数アプリが確認できます。 *[終了]* をクリックします。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio で Azure 関数を発行する: Functions インスタンス (関数アプリの後)":::

Visual Studio のメイン ウィンドウに再度 *[発行]* ペインが表示されるので、そこですべての情報が正しいことを確認し、 **[発行]** を選択します。

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Visual Studio で Azure 関数を発行する: 発行":::

> [!NOTE]
> 次のようなポップアップが表示される場合は、以下のようにします。:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Visual Studio で Azure 関数を発行する: 資格情報" border="false":::
> **[Attempt to retrieve credentials from Azure]\(Azure から資格情報の取得を試みる\)** 、 **[保存]** の順に選択します。
>
> "*Azure で関数のバージョンをアップグレード*" するようにという警告や、"*関数ランタイムのバージョンが Azure で実行されているバージョンと一致しない*" という警告が表示される場合は、次のようにしてください。
>
> 画面の指示に従って、最新の Azure Functions ランタイム バージョンにアップグレードします。 この問題は、このチュートリアルの冒頭にある「*前提条件*」で推奨されているバージョンよりも古いバージョンの Visual Studio を使用している場合に発生する可能性があります。

### <a name="assign-permissions-to-the-function-app"></a>関数アプリにアクセス許可を割り当てる

次の手順では、関数アプリから Azure Digital Twins にアクセスできるよう、アプリの設定を構成し、システムによって管理される Azure AD ID をアプリに割り当てて、その ID に Azure Digital Twins インスタンスの "*Azure Digital Twins 所有者 (プレビュー)* " ロールを付与します。 このロールは、インスタンスに対して多くのデータ プレーン アクティビティを実行するすべてのユーザーまたは関数に必要です。 セキュリティとロールの割り当ての詳細については、[*概念: Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)に関するページを参照してください。

Azure Cloud Shell で次のコマンドを使用して、関数アプリが Azure Digital Twins インスタンスを参照する目的で使用するアプリケーション設定を構成します。

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

次のコマンドを使用して、システムによって管理される ID を作成します。 出力の *principalId* フィールドを書き留めてください。

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

出力からの *principalId* 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの "*Azure Digital Twins 所有者 (プレビュー)* " ロールに割り当てます。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

このコマンドを実行すると、作成したロールの割り当てに関する情報が出力されます。 これで関数アプリに、Azure Digital Twins インスタンスへのアクセス許可が付与されました。

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>IoT Hub デバイスからのシミュレートされたテレメトリを処理する

Azure Digital Twins グラフは、実デバイスからのテレメトリによって駆動するように作られています。 

このステップでは、[IoT Hub](../iot-hub/about-iot-hub.md) に登録されたサーモスタットのシミュレーション デバイスを、Azure Digital Twins におけるサーモスタット デバイスを表すデジタル ツインに接続します。 シミュレートされたデバイスがテレメトリを出力すると、そのデータが Azure 関数 (*ProcessHubToDTEvents*) に誘導され、そこでデジタル ツインへの対応する更新がトリガーされます。 こうして、デジタル ツインが、実デバイスのデータとの間で常に最新の状態に保たれます。 Azure Digital Twins では、イベント データを別の場所に誘導するプロセスが[**イベントのルーティング**](concepts-route-events.md)と呼ばれます。

これが行われるのは、エンド ツー エンド シナリオの以下の部分 (**矢印 B**) です。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="ビルディング シナリオの全体図から矢印 B (Azure Digital Twins に至る前の要素、つまりデバイス、IoT Hub、1 つ目の Azure 関数) を抜粋して強調したもの":::

このデバイス接続を設定するためには、次の操作を行う必要があります。
1. シミュレートされたデバイスを管理する IoT ハブを作成します。
2. イベント サブスクリプションを設定して IoT ハブを適切な Azure 関数に接続します。
3. シミュレートされたデバイスを IoT ハブに登録します。
4. シミュレートされたデバイスを実行してテレメトリを生成します。
5. Azure Digital Twins にクエリを実行して結果をライブで確認します。

### <a name="create-an-iot-hub-instance"></a>IoT Hub インスタンスを作成する

Azure Digital Twins は、デバイスとそのデータを管理するための Azure サービス、[IoT Hub](../iot-hub/about-iot-hub.md) と連携するように設計されています。 このステップでは、このチュートリアルのサンプル デバイスを管理する IoT ハブを設定します。

Azure Cloud Shell で、次のコマンドを使用して新しい IoT ハブを作成します。

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

このコマンドからは、作成された IoT ハブについての情報が出力されます。

IoT ハブに付けた名前は保存しておいてください。 これは後で使用します。

### <a name="connect-the-iot-hub-to-the-azure-function"></a>IoT ハブを Azure 関数に接続する

次に、先ほど発行した関数アプリの Azure 関数 *ProcessHubToDTEvents* に IoT ハブを接続します。これで、IoT Hub 内のデバイスから関数へとデータが流れることができ、関数によって Azure Digital Twins が更新されます。

そのためには、Azure 関数をエンドポイントとする**イベント サブスクリプション**を IoT Hub に作成します。 これで、IoT Hub 内で発生したイベントを関数が "サブスクライブ" するようになります。

[Azure portal](https://portal.azure.com/) の上部の検索バーで、新しく作成した IoT ハブの名前を検索して、その IoT ハブに移動します。 ハブ メニューから *[イベント]* を選択し、 *[+ イベント サブスクリプション]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure portal: IoT Hub のイベント サブスクリプション":::

*[イベント サブスクリプションの作成]* ページが表示されます。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure portal: イベント サブスクリプションの作成":::

各フィールドに次のように入力します (既定値が入力されるフィールドは省略しています)。
* *[イベント サブスクリプションの詳細]*  >  **[名前]** : イベント サブスクリプションに名前を付けます。
* *[トピックの詳細]*  >  **[システム トピック名]** : システム トピックに使用する名前を指定します。 
* *[イベントの種類]*  >  **[イベントの種類のフィルター]** : メニュー オプションから *[Device Telemetry]\(デバイス テレメトリ\)* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイントのタイプ]** : メニュー オプションから *[Azure 関数]* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイント]** : *[エンドポイントの選択]* リンクを選択します。 *[Azure 関数の選択]* ウィンドウが開きます。:::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure portal のイベント サブスクリプション: Azure 関数の選択" border="false":::
    - **サブスクリプション**、**リソース グループ**、**関数アプリ**、**関数** (*ProcessHubToDTEvents*) を入力します。 そのいくつかは、サブスクリプションの選択後に自動的に入力されます。
    - **[選択の確認]** をクリックします。

再び *[イベント サブスクリプションの作成]* ページで、 **[作成]** をクリックします。

### <a name="register-the-simulated-device-with-iot-hub"></a>シミュレートされたデバイスを IoT Hub に登録する 

このセクションでは、IoT Hub におけるデバイスの表現を *thermostat67* という ID で作成します。 これがシミュレートされたデバイスの接続先となります。こうして、テレメトリ イベントがデバイスから IoT Hub に伝えられ、前の手順でサブスクライブした Azure 関数は、IoT Hub でリッスンしながらイベントを収集し、処理を続行します。

Azure Cloud Shell で、IoT Hub にデバイスを作成するには、次のコマンドを使用します。

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

作成されたデバイスについての情報が出力されます。

### <a name="configure-and-run-the-simulation"></a>シミュレーションを構成して実行する

次に、作成した IoT Hub インスタンスにデータを送信するようにデバイス シミュレーターを構成します。

まず、次のコマンドで "*IoT ハブの接続文字列*" を取得します。

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

次に、"*デバイスの接続文字列*" を次のコマンドで取得します。

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

ローカル プロジェクトで、これらの値をデバイス シミュレーターのコードに挿入し、この IoT ハブと IoT ハブ デバイスにシミュレーターを接続します。

新しい Visual Studio ウィンドウで、(ダウンロードしたソリューションのフォルダーから) _[Device Simulator] > **[DeviceSimulator.sln]**_ を開きます。

>[!NOTE]
> これで、Visual Studio ウィンドウは 2 つになりました。_**DeviceSimulator.sln**_ のウィンドウと、先ほど開いた _**AdtE2ESample.sln**_ のウィンドウです。

この新しい Visual Studio ウィンドウの *[ソリューション エクスプローラー]* ペインから、_DeviceSimulator/**AzureIoTHub.cs**_ を選択して編集ウィンドウで開きます。 次の接続文字列の値は、先ほど収集した値に置き換えてください。

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

ファイルを保存します。

今度は、設定したデータ シミュレーションの結果を確認するために、ツール バーにある次のボタンで **DeviceSimulator** プロジェクトを実行します。

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio のスタート ボタン (DeviceSimulator プロジェクト)":::

コンソール ウィンドウが開いて、シミュレートされた温度のテレメトリ メッセージが表示されます。 これらのメッセージは IoT Hub に送信され、そこで Azure 関数によって収集されて処理されます。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="送信中の温度テレメトリを示すデバイス シミュレーターのコンソール出力":::

このコンソールで行うべき作業は他にありませんが、次のステップに取り組む間、コンソールは実行したままにしておいてください。

### <a name="see-the-results-in-azure-digital-twins"></a>Azure Digital Twins で結果を確認する

先ほど発行した *ProcessHubToDTEvents* 関数は、IoT Hub データをリッスンし、Azure Digital Twins API を呼び出して、*thermostat67* ツインの *Temperature* プロパティを更新します。

Azure Digital Twins 側からデータを確認するには、_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

プロジェクトのコンソール ウィンドウが開いたら、次のコマンドを実行して、デジタル ツイン *thermostat67* からレポートされる温度を取得します。

```cmd
ObserveProperties thermostat67 Temperature
```

"*Azure Digital Twins インスタンスから*"、ライブで更新される温度が 10 秒おきにコンソールに記録されるのを確認できます。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="デジタル ツイン thermostat67 からの温度メッセージのログを表示するコンソール出力":::

正しく動作していることを確認したら、どちらのプロジェクトも実行を停止してかまいません。 Visual Studio のウィンドウは開いたままにしてください。以降、引き続きこのチュートリアルで使用します。

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure Digital Twins のイベントをグラフ全体に反映させる

このチュートリアルではこれまで、外部のデバイス データから Azure Digital Twins を更新する方法を見てきました。 今度は、ある 1 つのデジタル ツインから Azure Digital Twins グラフ全体に変更を反映させる方法、つまり、サービス内部のデータからツインを更新する方法について見ていきます。

そのために、ここでは *ProcessDTRoutedData* という Azure 関数を使用して、*Thermostat* ツインが更新されたときに、接続された *Room* ツインを更新します。 これが行われるのは、エンド ツー エンド シナリオの以下の部分 (**矢印 C**) です。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="ビルディング シナリオの全体図から矢印 C (Azure Digital Twins の後の要素、つまり Event Grid と 2 つ目の Azure 関数) を抜粋して強調したもの":::

このデータ フローを設定するためには、次の操作を行う必要があります。
1. Azure Digital Twins インスタンスを Event Grid に接続するエンドポイントを作成します。
2. そのエンドポイントにツインのプロパティ変更イベントを送信するためのルートを Azure Digital Twins 内に設定します。
3. エンドポイントで ([Event Grid](../event-grid/overview.md) を使用して) リッスンし、他のツインを適宜更新する Azure Functions アプリをデプロイします。
4. シミュレートされたデバイスを実行し、Azure Digital Twins にクエリを実行して結果をライブで確認します。

### <a name="set-up-endpoint"></a>エンドポイントを設定する

[Event Grid](../event-grid/overview.md) は、Azure サービスから生じたイベントを Azure 内の別の場所にルーティングして配信できる Azure サービスです。 [Event Grid トピック](../event-grid/concepts.md)を作成すると、特定のイベントをソースから収集することができ、また、サブスクライバーは、トピックをリッスンすることで、配信されたイベントを受信することができます。

このセクションでは、Event Grid トピックを作成した後、そのトピックを参照する (トピックにイベントを送信する) エンドポイントを Azure Digital Twins 内に作成します。 

Event Grid トピックを作成するには、Azure Cloud Shell から次のコマンドを実行します。

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Azure CLI のコマンドに渡すことのできる Azure リージョン名のリストを出力するには、次のコマンドを実行します。
> ```azurecli
> az account list-locations -o table
> ```

このコマンドからは、作成した Event Grid トピックについての情報が出力されます。

次に、その Event Grid トピックを参照する Azure Digital Twins エンドポイントを作成します。 使用するコマンドは次のとおりです。プレースホルダーのフィールドは適宜置き換えてください。

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

このコマンドからは、作成したエンドポイントについての情報が出力されます。

エンドポイントの作成に成功したかどうかは、次のコマンドを実行して、Azure Digital Twins インスタンスにそのエンドポイントを照会することで確認できます。

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

出力から `provisioningState` フィールドを探し、その値が "Succeeded" になっていることを確認します。 また、エンドポイントがまだ作成されているという意味で、"Provisioning" と表示される場合もあります。 この場合は、数秒待ってからコマンドを再実行して、正常に完了したことを確認します。

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="エンドポイントの provisioningState が Succeeded であることを示すクエリの結果":::

Event Grid トピックと Azure Digital Twins エンドポイントに付けた名前は保存しておいてください。 これらは後で使用します。

### <a name="set-up-route"></a>ルートを設定する

次に、先ほど作成した Azure Digital Twins エンドポイントにイベントを送信する Azure Digital Twins ルートを作成します。

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

このコマンドからは、作成したルートについての情報が出力されます。

>[!NOTE]
>エンドポイント (前の手順のもの) は、それらを使用するイベント ルートをセットアップする前に、プロビジョニングを完了している必要があります。 エンドポイントの準備ができていないためにルートの作成が失敗する場合は、数分待ってからやり直してください。

#### <a name="connect-the-function-to-event-grid"></a>Event Grid に関数を接続する

次に、先ほど作成した Event Grid トピックを Azure 関数 (*ProcessDTRoutedData*) からサブスクライブします。そうすることで、テレメトリ データは *thermostat67* ツインから Event Grid トピックを介して関数へと流れることができ、関数が再び Azure Digital Twins に作用して *room21* ツインを適宜更新します。

そのためには、Event Grid トピックからエンドポイントとしての Azure 関数 (*ProcessDTRoutedData*) への **Event Grid サブスクリプション**を作成します。

[Azure portal](https://portal.azure.com/) の上部の検索バーで、Event Grid トピックの名前を検索してそのトピックに移動します。 *[+ イベント サブスクリプション]* を選択します。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure portal: Event Grid イベント サブスクリプション":::

このイベント サブスクリプションを作成する手順は、このチュートリアルの中で先ほど 1 つ目の Azure 関数から IoT Hub をサブスクライブしたときと同様です。 ただし今回は、リッスンするイベントの種類として "*デバイス テレメトリ*" を指定する必要はありません。また、接続先となる Azure 関数も異なります。

*[イベント サブスクリプションの作成]* ページで、各フィールドに次のように入力します (既定値が入力されるフィールドは省略しています)。
* *[イベント サブスクリプションの詳細]*  >  **[名前]** : イベント サブスクリプションに名前を付けます。
* *[エンドポイントの詳細]*  >  **[エンドポイントのタイプ]** : メニュー オプションから *[Azure 関数]* を選択します。
* *[エンドポイントの詳細]*  >  **[エンドポイント]** : *[エンドポイントの選択]* リンクを選択します。 *[Azure 関数の選択]* ウィンドウが開きます。
    - **サブスクリプション**、**リソース グループ**、**関数アプリ**、**関数** (*ProcessDTRoutedData*) を入力します。 そのいくつかは、サブスクリプションの選択後に自動的に入力されます。
    - **[選択の確認]** をクリックします。

再び *[イベント サブスクリプションの作成]* ページで、 **[作成]** をクリックします。

### <a name="run-the-simulation-and-see-the-results"></a>シミュレーションを実行して結果を確認する

デバイス シミュレーターを実行して、これまでに設定した新しいイベント フローを開始してみましょう。 _**DeviceSimulator**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

先ほどデバイス シミュレーターを実行したときと同様に、コンソール ウィンドウが開いて、シミュレートされた温度のテレメトリ メッセージが表示されます。 これらのイベントは、前半に設定したフローをたどって *thermostat67* ツインを更新した後、後半に設定したフローをたどって *room21* ツインを thermostat67 と同じ状態に更新します。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="送信中の温度テレメトリを示すデバイス シミュレーターのコンソール出力":::

このコンソールで行うべき作業は他にありませんが、次のステップに取り組む間、コンソールは実行したままにしておいてください。

Azure Digital Twins 側からデータを確認するには、_**AdtE2ESample**_ プロジェクトが開いている Visual Studio ウィンドウに移動し、プロジェクトを実行します。

プロジェクトのコンソール ウィンドウが開いたら、次のコマンドを実行して、*thermostat67* と *room21* の**両方**のデジタル ツインによってレポートされる温度を取得します。

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

"*Azure Digital Twins インスタンスから*"、ライブで更新される温度が 10 秒おきにコンソールに記録されるのを確認できます。 *thermostat67* への変更に合わせて *room21* の温度が更新されていることがわかります。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="thermostat と room からの温度メッセージのログを示すコンソール出力":::

正しく動作していることを確認したら、どちらのプロジェクトも実行を停止してかまいません。 また、チュートリアルはこれで完了なので、Visual Studio ウィンドウも閉じてかまいません。

## <a name="review"></a>確認

以下、このチュートリアルで構築したシナリオの確認です。

1. Azure Digital Twins インスタンスは、フロア、部屋、サーモスタットをデジタルで表現します (以下の図の**セクション A**)。
2. シミュレートされたデバイスのテレメトリが IoT Hub に送信されます。IoT Hub では、Azure 関数 *ProcessHubToDTEvents* がテレメトリ イベントをリッスンします。 Azure 関数 *ProcessHubToDTEvents* は、これらのイベント内の情報を使用して、*thermostat67* の *Temperature* プロパティを設定します (図の**矢印 B**)。
3. Azure Digital Twins のプロパティ変更イベントが Event Grid トピックにルーティングされます。Event Grid トピックでは、Azure 関数 *ProcessDTRoutedData* がイベントをリッスンしています。 Azure 関数 *ProcessDTRoutedData* は、これらのイベント内の情報を使用して、*room21* の *Temperature* プロパティを設定します (図の**矢印 C**)。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="ビルディング シナリオ全体を表す図。データがデバイスから IoT Hub へと流れ、Azure Functions を経て (矢印 B) Azure Digital Twins インスタンス (セクション A) に到達した後、Event Grid を介して別の Azure Functions に到達して処理 (矢印 C) されるようすを表しています":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースが不要であれば、次の手順に従って削除してください。 

[Azure Cloud Shell](https://shell.azure.com) から [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 これにより、リソース グループが削除され、Azure Digital Twins インスタンス、IoT ハブとハブ デバイスの登録、Event Grid トピックとそれに関連するサブスクリプション、Azure Functions アプリが、両方の機能やストレージなどの関連するリソースを含めて削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

次に、クライアント アプリ用に作成した Azure AD アプリの登録を次のコマンドで削除します。

```azurecli
az ad app delete --id <your-application-ID>
```

最後に、ローカル コンピューターにダウンロードしたプロジェクトのサンプル フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デバイスのライブ データによって駆動される Azure Digital Twins を紹介するエンド ツー エンドのシナリオを作成しました。

今度は、次のチュートリアルの概念ドキュメントを参照し、ここで扱った要素についてさらに理解を深めましょう。

> [!div class="nextstepaction"]
> "[*概念: カスタム モデル*](concepts-models.md)"