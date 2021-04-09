---
title: 'チュートリアル: Azure Digital Twins でグラフを作成する (CLI)'
titleSuffix: Azure Digital Twins
description: Azure CLI を使用して Azure Digital Twins シナリオを作成するチュートリアル
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d155d0c4a18b254f66ff5fb58ea91dbee22d2c34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496611"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>チュートリアル: Azure CLI を使用して Azure Digital Twins グラフを作成する

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

このチュートリアルでは、モデル、ツイン、およびリレーションシップを使用して、Azure Digital Twins でグラフを作成します。 このチュートリアルのツールは、[**Azure CLI** 用の Azure Digital Twins コマンド セット](how-to-use-cli.md)です。 

CLI コマンドを使用して、モデルのアップロード、ツインの作成と変更、リレーションシップの作成など、基本的な Azure Digital Twins の操作を実行できます。 また、[*az dt* コマンド セットのリファレンス ドキュメント](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)を参照して、CLI コマンドの完全なセットを確認することもできます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * 環境をモデル化する
> * デジタル ツインの作成
> * リレーションシップを追加して、グラフを形成する
> * グラフのクエリを実行して、質問に回答する

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を実行するには、まず次の前提条件を満たしておく必要があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に **[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成** してください。

### <a name="download-the-sample-models"></a>サンプル モデルをダウンロードする

このチュートリアルでは、Azure Digital Twins の C# [エンドツーエンド サンプル プロジェクト](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)の一部である事前に作成された 2 つのモデルを使用します。 モデル ファイルは以下にあります。 
* [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

お使いのマシンにこれらのファイルを用意するには、上記のナビゲーション リンクを使用して、ファイル本文をマシン上の同じ名前 (*Room.json* と *Floor.json*) のローカル ファイルにコピーします。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

この記事で Azure Digital Twins を操作するには、まず **Azure Digital Twins インスタンス** とそれを使用するために必要なアクセス許可を設定する必要があります。 以前の作業で Azure Digital Twins インスタンスが既に設定されている場合は、そのインスタンスを使用できます。

それ以外の場合は、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-cli.md)に関するページを参照してください。 説明には、各手順が正常に完了し、新しいインスタンスを使用する準備ができていることを確認するための手順も含まれています。

Azure Digital Twins インスタンスを設定したら、後でインスタンスに接続するために必要な次の値をメモしておきます。
* インスタンスの **"_ホスト名_"**
* インスタンスの作成に使用した **Azure サブスクリプション**。 

インスタンスのこれらの両方の値は、次の Azure CLI コマンドの出力から取得できます。 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="az dt show コマンドの出力を示す Cloud Shell ブラウザー ウィンドウのスクリーンショット。hostName フィールドとサブスクリプション ID (id フィールドの一部) が強調表示されています。":::

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL を使用して物理環境をモデル化する

CLI と Azure Digital Twins インスタンスが設定されたので、シナリオのグラフの作成を開始できます。 

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、対象となる環境のツイン [**モデル**](concepts-models.md)の定義です。 

モデルは、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、[デジタル ツイン](concepts-twins-graph.md)のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 モデルは、**Digital Twins Definition Language (DTDL)** という JSON に似た言語で記述され、ツインの "*プロパティ*"、"*テレメトリ*"、"*リレーションシップ*"、"*コンポーネント*" を定義することができます。

> [!NOTE]
> デジタル ツインに対する "*コマンド*" も DTDL で定義できます。 ただし Azure Digital Twins サービスでは現在、コマンドがサポートされていません。

お使いのマシンで、「[前提条件](#prerequisites)」セクションで作成した *Room.json* ファイルに移動します。 それをコード エディターで開き、次の方法で変更します。

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins にモデルをアップロードする

モデルを設計したら、Azure Digital Twins インスタンスにアップロードする必要があります。 そうすることで、カスタム ドメインの独自のボキャブラリを使用して、Azure Digital Twins サービス インスタンスが構成されます。 モデルをアップロードしたら、そのモデルを使用するツイン インスタンスを作成できます。

1. Cloud Shell を使用してモデルを追加するには、モデル ファイルを使用する Cloud Shell コマンドを実行したときに使用できるように、それらのファイルを Cloud Shell のストレージにアップロードする必要があります。 これを行うには、[ファイルのアップロード/ダウンロード] アイコンを選択し、[アップロード] を選択します。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="[アップロード] アイコンの選択を示す Cloud Shell ブラウザー ウィンドウのスクリーンショット。":::
    
    マシン上の *Room.json* ファイルに移動し、[開く] を選択します。 その後、*Floor.json* についてもこの手順を繰り返します。

1. 次に、下に示す [**az dt model create**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create) コマンドを使用して、更新した *Room* モデルを Azure Digital Twins インスタンスにアップロードします。 2 番目のコマンドでは、別のモデルである *Floor* をアップロードします。これは、さまざまな種類のツインを作成するために次のセクションでも使用します。

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    各コマンドの出力には、正常にアップロードされたモデルに関する情報が表示されます。

    >[!TIP]
    >model create コマンドの `--from-directory` オプションを使用して、ディレクトリ内のすべてのモデルを同時にアップロードすることもできます。 詳細については、[*az dt model create* のオプション パラメーター](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create-optional-parameters)に関する記事を参照してください。

1. 下に示すように、[**az dt model list**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_list) コマンドを使用して、モデルが作成されたことを確認します。 これにより、Azure Digital Twins インスタンスにアップロードされたすべてのモデルの一覧が、その完全な情報と共に出力されます。 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    この結果から、編集済みの *Room* モデルを探してみましょう。
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="model list コマンドの結果 (更新した Room モデルを含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>エラー

CLI では、サービスからのエラーも処理されます。 

`az dt model create` コマンドを再実行して、もう一度、先ほどアップロードしたものと同じモデルをどれか再アップロードしてみてください。

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

モデルは上書きできないので、これにより `ModelIdAlreadyExists` のエラー コードが返されます。

## <a name="create-digital-twins"></a>デジタル ツインを作成する

Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に基づいて [**デジタル ツイン**](concepts-twins-graph.md)を作成できます。 デジタル ツインは、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 

デジタル ツインを作成するには、[**az dt twin create**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_create) コマンドを使用します。 ツインのベースとなるモデルを参照する必要があります。モデルのプロパティには、必要に応じて初期値を定義することができます。 この段階では、リレーションシップ情報を渡す必要はありません。

1. Cloud Shell でこのコードを実行して、先ほど更新した *Room* モデルと、もう 1 つのモデル (*Floor*) に基づいて、いくつかのツインを作成します。 *Room* には 3 つのプロパティがあったことを思い出してください。それらの初期値を引数で指定することができます。 (プロパティ値の初期化は一般に省略可能ですが、このチュートリアルでは必要です。)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > PowerShell 環境で Cloud Shell を使用している場合は、`--properties` JSON 値が正しく解析されるように、引用符文字のエスケープが必要になる場合があります。 この編集を行うと、部屋のツインを作成するコマンドは次のようになります。
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > これは、下のスクリーンショットに反映されています。
    
    各コマンドの出力には、正常に作成されたツインに関する情報 (部屋ツインと共に初期化されたそれらのプロパティを含みます) が表示されます。

1. 下に示すように、[**az dt twin query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) コマンドを使用してツインが作成されたことを確認できます。 示されたクエリにより、Azure Digital Twins インスタンス内のすべてのデジタル ツインが検索されます。
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    その結果から、*room0*、*room1*、*floor0*、*floor1* のツインを探します。 このクエリの結果の一部を示す抜粋を次に示します。
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="ツイン クエリの部分的な結果 (room0 と room1 を含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>デジタル ツインに変更を加える

作成したツインのプロパティを変更することもできます。 

1. こちらの [**az dt twin update**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_update) コマンドを実行して、*room0* の RoomName を *Room0* から *PresidentialSuite* に変更します。

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > PowerShell 環境で Cloud Shell を使用している場合は、`--json-patch` JSON 値が正しく解析されるように、引用符文字のエスケープが必要になる場合があります。 この編集を行うと、ツインを更新するコマンドは次のようになります。
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > これは、下のスクリーンショットに反映されています。
    
    このコマンドの出力には、ツインの現在の情報が表示され、結果には `RoomName` の新しい値が表示されています。

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="update コマンドの結果 (PresidentialSuite の RoomName を含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. [**az dt twin show**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_show) コマンドを実行して *room0* の情報を表示すると、更新が成功したことを確認できます。

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    更新後の名前が出力結果に反映されているはずです。

## <a name="create-a-graph-by-adding-relationships"></a>リレーションシップを追加してグラフを作成する

次に、ツイン間にいくつかの **リレーションシップ** を作成することで、それらのツインを接続し、[**ツイン グラフ**](concepts-twins-graph.md)を形成することができます。 ツイン グラフは、環境全体を表すために使用されます。 

あるツインから別のものへと作成できるリレーションシップの種類は、前にアップロードした[モデル](#model-a-physical-environment-with-dtdl)内に定義されています。 [*Floor* のモデル定義](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)では、フロアに *contains* という種類のリレーションシップを設定できることが指定されています。 これにより、各 *Floor* ツインから、それに含まれる対応する部屋への *contains* の種類のリレーションシップを作成できるようになります。

リレーションシップを追加するには、[**az dt twin relationship create**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_create) コマンドを使用します。 リレーションシップの接続元となるツインと、リレーションシップの種類、リレーションシップの接続先のツインを指定します。 最後に、リレーションシップに一意の ID を指定します。 プロパティを持つようにリレーションシップが定義されていた場合は、このコマンドでリレーションシップのプロパティを初期化することもできます。

1. 次のコードを実行すると、先ほど作成した各 *Floor* ツインから対応する *Room* ツインへの *contains* の種類のリレーションシップが追加されます。 リレーションシップには、*relationship0* と *relationship1* という名前が付けられます。

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >また、[*Floor* モデル](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)内の *contains* リレーションシップは、`ownershipUser` と `ownershipDepartment` の 2 つのプロパティを指定して定義されていたため、リレーションシップを作成するときに、これらの初期値を引数として指定することもできます。
    > これらのプロパティを初期化してリレーションシップを作成するには、このように、上記のいずれかのコマンドに `--properties` オプションを追加します。
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > PowerShell 環境で Cloud Shell を使用している場合は、`--properties` JSON 値が正しく解析されるように、引用符文字のエスケープが必要になる場合があります。
    
    各コマンドの出力には、正常に作成されたリレーションシップに関する情報が表示されます。

1. 次のいずれかのコマンドを使用し、Azure Digital Twins インスタンスでリレーションシップのクエリを実行することで、リレーションシップを確認できます。
    * 各フロアを接続元とするすべてのリレーションシップを確認するには (一方の側からリレーションシップを表示):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * 各部屋を接続先とするすべてのリレーションシップを表示するには ("もう一方の" 側からリレーションシップを表示):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * これらのリレーションシップを ID で個別に検索するには:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

このチュートリアルで設定したツインとリレーションシップによって、次の概念グラフが形成されます。

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="概念グラフを示す図 (floor0 は relationship0 によって room0 に接続され、floor1 は relationship1 によって room1 に接続されている)。" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>環境についての質問をツイン グラフに照会する

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。 Azure CLI では、[**az dt twin query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) コマンドを使用してこれを行います。

サンプル環境に関するいくつかの質問に回答するには、Cloud Shell で次のクエリを実行します。

1. **Azure Digital Twins で表されている自分の環境内のすべてのエンティティを知りたい** (すべて照会)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    これで環境の概要を調べ、必要な事柄がすべて Azure Digital Twins 内で表現されていることを確認できます。 このコマンドを実行すると、個々のデジタル ツインとその詳細を含んだ出力が返されます。 その抜粋を次に示します。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="ツイン クエリの部分的な結果 (room0 と room1 を含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >お気づきかもしれませんが、これはインスタンス内のすべての Azure Digital Twins を検索するために前の「[*デジタル ツインを作成する*](#create-digital-twins)」セクションで使用したのと同じコマンドです。

1. **環境内に存在する部屋をすべて知りたい** (モデルで照会)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    クエリを特定のタイプのツインに制限することで、表現されている内容についての、より具体的な情報を取得することができます。 このコマンドを実行すると、*room0* と *room1* は表示されますが、*floor0* と *floor1* は (room ではなく floor であるため) **表示されません**。
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="モデル クエリの結果 (room0 と room1 のみを含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. ***floor0* に存在するすべての部屋を知りたい** (リレーションシップで照会)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    グラフ内のリレーションシップに基づいてクエリを実行すると、ツインの関係性についての情報を入手したり、クエリを特定のエリアに制限したりすることができます。 *floor0* に存在するのは *room0* のみです。したがって、結果に含まれる部屋は room0 のみとなります。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="リレーションシップ クエリの結果 (room0 を含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > ツインの ID (上記のクエリの *floor0* など) には、メタデータ フィールド `$dtId` を使用してクエリが実行されることに注意してください。 
    >
    >Cloud Shell を使用して、このような `$` で始まるメタデータ フィールドでクエリを実行する場合は、バッククォートで `$` をエスケープして、それが変数ではなく、クエリ テキスト内のリテラルとして使用する必要があることを CloudShell に認識させる必要があります。 これは、上のスクリーンショットに反映されています。

1. **自分の環境に存在するツインのうち、温度が 75 度を超えるツインをすべて知りたい** (プロパティで照会)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    プロパティに基づいてグラフを照会することにより、さまざまな質問への答えを得ることができます。たとえば、環境内で注意すべき外れ値を見つけることもできます。 その他の比較演算子 ( *<* , *>* 、 *=* 、 *!=* ) もサポートされます。 ここでは、温度が 80 である *room1* が結果として表示されます。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="プロパティ クエリの結果 (room1 のみを含む) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. ***floor0* 上で温度が 75 度を超えるすべての部屋を知りたい** (複合クエリ)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    SQL と同様、結合演算子 (`AND`、`OR`、`NOT` など) を使用して、先行するクエリを結合することもできます。 このクエリは、`AND` を使用して、ツインの温度に関する先行するクエリを絞り込んでいます。 結果には、*floor0* 上の部屋のうち、温度が 75 度を超える部屋のみが表示されます。このケースでは、該当する部屋はありません。 結果セットは空になります。

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="複合クエリの結果 (項目を含まない) を示す Cloud Shell のスクリーンショット。" lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを終えたら、次に行う作業に応じて、削除するリソースを選択できます。

* **次のチュートリアルに進む場合** は、ここで設定したリソースを保持し、その間に何もクリアすることなく、Azure Digital Twins インスタンスを再利用できます。

* **Azure Digital Twins インスタンスは引き続き使用するものの、そのモデル、ツイン、およびリレーションシップをすべてクリアする場合** は、[**az dt twin relationship delete**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_delete)、[**az dt twin delete**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_delete)、[**az dt model delete**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_delete) の各コマンドを使用して、インスタンスのリレーションシップ、ツイン、モデルをそれぞれクリアできます。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

また、ローカル コンピューターに作成したモデル ファイルを削除することもできます。

## <a name="next-steps"></a>次のステップ 

このチュートリアルでは、Azure CLI を使用してインスタンスにグラフを作成することで、Azure Digital Twins の使用を開始しました。 モデル、デジタル ツイン、およびリレーションシップを作成して、グラフを形成しました。 また、Azure Digital Twins で回答できる環境についての質問の種類を理解するために、グラフに対していくつかのクエリを実行しました。

次のチュートリアルに進み、Azure Digital Twins を他の Azure サービスと組み合わせて、データ ドリブンのエンド ツー エンドのシナリオを完了してください。
> [!div class="nextstepaction"]
> [*チュートリアル:エンド ツー エンドのソリューションを接続する*](tutorial-end-to-end.md)"