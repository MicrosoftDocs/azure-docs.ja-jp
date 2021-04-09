---
title: 'チュートリアル: Azure Digital Twins でグラフを作成する (クライアント アプリ)'
titleSuffix: Azure Digital Twins
description: サンプル コマンド ライン アプリケーションを使用して Azure Digital Twins シナリオを作成するチュートリアル
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493708"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>チュートリアル: サンプル クライアント アプリを使用して Azure Digital Twins グラフを作成する

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

このチュートリアルでは、モデル、ツイン、およびリレーションシップを使用して、Azure Digital Twins でグラフを作成します。 このチュートリアルのツールは、Azure Digital Twins インスタンスを操作するための **サンプル コマンドライン クライアント アプリケーション** です。 このクライアント アプリは、"[*クライアント アプリのコーディングに関するチュートリアル*](tutorial-code.md)" で作成したアプリに似ています。

このサンプルを使用して、モデルのアップロード、ツインの作成と変更、リレーションシップの作成など、基本的な Azure Digital Twins アクションを実行することができます。 [サンプルのコード](https://github.com/Azure-Samples/digital-twins-samples/tree/master/)を見て、Azure Digital Twins の API について学んだり、サンプル プロジェクトに自由に変更を加えながら、独自のコマンドを実装する練習をしたりすることもできます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * 環境をモデル化する
> * デジタル ツインの作成
> * リレーションシップを追加して、グラフを形成する
> * グラフのクエリを実行して、質問に回答する

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>サンプル プロジェクトを実行する

アプリと認証が設定されたので、ツール バーの次のボタンを使用してプロジェクトを実行します。

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Visual Studio のスタート ボタン (SampleClientApp プロジェクト) のスクリーンショット。" lightbox="media/tutorial-command-line/app/start-button-sample.png":::

コンソール ウィンドウが表示されて認証が行われ、コマンドを待つ状態となります。 
* 認証はブラウザーを通じて処理されます。つまり既定の Web ブラウザーが開いて、認証プロンプトが表示されます。 このプロンプトから Azure の資格情報を使用してサインインしてください。 その後は、ブラウザーのタブまたはウィンドウを閉じてかまいません。

次に示したのは、プロジェクト コンソールの画面例を示すスクリーンショットです。

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="コマンドライン アプリからのウェルカム メッセージのスクリーンショット。" lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> このプロジェクトで使用できる全コマンドの一覧を確認するには、プロジェクト コンソールで「`help`」と入力し、Return キーを押します。

以降このチュートリアルの手順に取り組む間、プロジェクト コンソールは実行したままにしてください。

## <a name="model-a-physical-environment-with-dtdl"></a>DTDL を使用して物理環境をモデル化する

Azure Digital Twins インスタンスとサンプル アプリが設定されたので、シナリオのグラフの作成を開始できます。 

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、対象となる環境のツイン [**モデル**](concepts-models.md)の定義です。 

モデルは、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、[デジタル ツイン](concepts-twins-graph.md)のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 モデルは、**Digital Twins Definition Language (DTDL)** という JSON に似た言語で記述され、ツインの "*プロパティ*"、"*テレメトリ*"、"*リレーションシップ*"、"*コンポーネント*" を定義することができます。

> [!NOTE]
> デジタル ツインに対する "*コマンド*" も DTDL で定義できます。 ただし Azure Digital Twins サービスでは現在、コマンドがサポートされていません。

_**AdtE2ESample**_ プロジェクトを開いた Visual Studio ウィンドウで、 *[ソリューション エクスプローラー]* ペインを使用して "*AdtSampleApp\SampleClientApp\Models フォルダー*" に移動します。 このフォルダーにサンプル モデルが格納されています。

*[Room.json]* を選択して編集ウィンドウで開き、次のように変更を加えます。

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins にモデルをアップロードする

モデルを設計したら、Azure Digital Twins インスタンスにアップロードする必要があります。 そうすることで、カスタム ドメインの独自のボキャブラリを使用して、Azure Digital Twins サービス インスタンスが構成されます。 モデルをアップロードしたら、そのモデルを使用するツイン インスタンスを作成できます。

1. プロジェクト コンソール ウィンドウで、次のコマンドを実行して、更新済みの *Room* モデルと、次のセクションで異なるタイプのツインを作成するために使用する *Floor* モデルをアップロードします。

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    出力結果を見ると、モデルが正常に作成されたことがわかります。

1. `GetModels true` コマンドを実行して、モデルが作成されたことを確認します。 アップロード済みのすべてのモデルが Azure Digital Twins インスタンスに対して照会され、そのすべての情報が出力されます。 この結果から、編集済みの *Room* モデルを探してみましょう。

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="GetModels の結果 (更新済みの Room モデルを表示) のスクリーンショット。" lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>エラー

このサンプル アプリケーションは、サービスからのエラーも処理します。 

`CreateModels` コマンドを再実行して、もう一度、先ほどアップロードしたものと同じモデルをどれか再アップロードしてみてください。

```cmd/sh
CreateModels Room
```

モデルを上書きすることはできないので、今度はサービス エラーが返されます。
既存のモデルを削除する方法について詳しくは、[*方法: DTDL モデルの管理*](how-to-manage-model.md)に関する記事を参照してください。
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>デジタル ツインを作成する

Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に基づいて [**デジタル ツイン**](concepts-twins-graph.md)を作成できます。 デジタル ツインは、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 

デジタル ツインを作成するには、`CreateDigitalTwin` コマンドを使用します。 ツインのベースとなるモデルを参照する必要があります。モデルのプロパティには、必要に応じて初期値を定義することができます。 この段階では、リレーションシップ情報を渡す必要はありません。

1. 実行中のプロジェクト コンソールでこのコードを実行すると、いくつかのツインが作成されます。先ほど更新した *Room* モデルに基づくツインと、もう 1 つのモデル (*Floor*) に基づくツインです。 *Room* には 3 つのプロパティがあったことを思い出してください。それらの初期値を引数で指定することができます。 (プロパティ値の初期化は一般に省略可能ですが、このチュートリアルでは必要です。)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    コマンドから返される出力を見ると、ツインが正常に作成されたことがわかります。 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="CreateDigitalTwin コマンドの結果からの抜粋 (floor0、floor1、room0、room1 を含む) を示すスクリーンショット。" lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. `Query` コマンドを実行すると、ツインが作成されたことを確認できます。 Azure Digital Twins インスタンスに対し、そこに含まれるすべてのデジタル ツインがこのコマンドによって照会されます。 その結果から、*room0*、*room1*、*floor0*、*floor1* のツインを探します。

### <a name="modify-a-digital-twin"></a>デジタル ツインに変更を加える

作成したツインのプロパティを変更することもできます。 

> [!NOTE]
> 基になる REST API では、ツインに対する更新を定義するために、[JSON Patch](http://jsonpatch.com/) 形式が使用されます。 また、コマンドライン アプリでは、この形式を使用して、基になる API が予期する内容を含む真のエクスペリエンスを提供します。

1. 次のコマンドを実行して、*room0* の RoomName を *Room0* から *PresidentialSuite* に変更します。
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    出力結果を見ると、ツインが正常に更新されたことがわかります。

1. 次のコマンドを実行して *room0* の情報を表示すると、更新が成功したことを確認できます。

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    更新後の名前が出力結果に反映されているはずです。


## <a name="create-a-graph-by-adding-relationships"></a>リレーションシップを追加してグラフを作成する

次に、ツイン間にいくつかの **リレーションシップ** を作成することで、それらのツインを接続し、[**ツイン グラフ**](concepts-twins-graph.md)を形成することができます。 ツイン グラフは、環境全体を表すために使用されます。 

あるツインから別のものへと作成できるリレーションシップの種類は、前にアップロードした[モデル](#model-a-physical-environment-with-dtdl)内に定義されています。 [*Floor* のモデル定義](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)では、フロアに *contains* という種類のリレーションシップを設定できることが指定されています。 これにより、各 *Floor* ツインから、それに含まれる対応する部屋への *contains* の種類のリレーションシップを作成できるようになります。

リレーションシップを追加するには、`CreateRelationship` コマンドを使用します。 リレーションシップの接続元となるツインと、リレーションシップの種類、リレーションシップの接続先のツインを指定します。 最後に、リレーションシップに一意の ID を指定します。

1. 次のコードを実行すると、先ほど作成した各 *Floor* ツインから対応する *Room* ツインへの "contains" リレーションシップが追加されます。 リレーションシップには、*relationship0* と *relationship1* という名前が付けられます。

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >また、[*Floor* モデル](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)内の *contains* リレーションシップは、`ownershipUser` と `ownershipDepartment` の 2 つの文字列プロパティを指定して定義されていたため、リレーションシップを作成するときに、これらの初期値を引数として指定することもできます。
    > *relationship0* を作成する上記のコマンドの代替バージョンを次に示します。これは、これらのプロパティの初期値も指定しています。
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    リレーションシップが正常に作成されたことが、コマンドから返される出力で確認できます。
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="CreateRelationship コマンドの結果からの抜粋 (relationship0 と relationship1 を含む) のスクリーンショット。" lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. 次のいずれかのコマンドを使用し、Azure Digital Twins インスタンスにリレーションシップのクエリを実行することで、リレーションシップを確認できます。
    * 各フロアを接続元とするすべてのリレーションシップを確認するには (一方の側からリレーションシップを表示):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * 各部屋を接続先とするすべてのリレーションシップを表示するには ("もう一方の" 側からリレーションシップを表示):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * これらのリレーションシップを ID で個別に検索するには:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

このチュートリアルで設定したツインとリレーションシップによって、次の概念グラフが形成されます。

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="概念グラフを示す図 (floor0 は relationship0 によって room0 に接続され、floor1 は relationship1 によって room1 に接続されている)。" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>環境についての質問をツイン グラフに照会する

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。 

サンプル環境に関するいくつかの質問に回答するには、実行中のプロジェクト コンソールで次のコマンドを実行します。

1. **Azure Digital Twins で表されている自分の環境内のすべてのエンティティを知りたい** (すべて照会)

    ```cmd/sh
    Query
    ```

    これで環境の概要を調べ、必要な事柄がすべて Azure Digital Twins 内で表現されていることを確認できます。 このコマンドを実行すると、個々のデジタル ツインとその詳細を含んだ出力が返されます。 その抜粋を次に示します。

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="ツイン クエリの部分的な結果 (room0 と floor1 を含む) を示すスクリーンショット。":::

    >[!NOTE]
    >サンプル プロジェクトでは、引数が追加されていない `Query` コマンドは、`Query SELECT * FROM DIGITALTWINS` に相当します。 [クエリ API](/rest/api/digital-twins/dataplane/query) または [CLI コマンド](how-to-use-cli.md)を使用して、インスタンス内のすべてのツインに対してクエリを実行するには、より長い (完全な) クエリを使用します。

1. **環境内に存在する部屋をすべて知りたい** (モデルで照会)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    クエリを特定のタイプのツインに制限することで、表現されている内容についての、より具体的な情報を取得することができます。 このコマンドを実行すると、*room0* と *room1* は表示されますが、*floor0* と *floor1* は (room ではなく floor であるため) **表示されません**。
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="モデル クエリの結果 (room0 と room1 のみ表示) のスクリーンショット。":::

1. ***floor0* に存在するすべての部屋を知りたい** (リレーションシップで照会)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    グラフ内のリレーションシップに基づいてクエリを実行すると、ツインの関係性についての情報を入手したり、クエリを特定のエリアに制限したりすることができます。 *floor0* に存在するのは *room0* のみです。したがって、結果に含まれる部屋は room0 のみとなります。

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="リレーションシップ クエリの結果 (room0 を表示) のスクリーンショット。":::

1. **自分の環境に存在するツインのうち、温度が 75 度を超えるツインをすべて知りたい** (プロパティで照会)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    プロパティに基づいてグラフを照会することにより、さまざまな質問への答えを得ることができます。たとえば、環境内で注意すべき外れ値を見つけることもできます。 その他の比較演算子 ( *<* , *>* 、 *=* 、 *!=* ) もサポートされます。 ここでは、温度が 80 である *room1* が結果として表示されます。

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="プロパティ クエリの結果 (room1 のみ表示) のスクリーンショット。":::

1. ***floor0* 上で温度が 75 度を超えるすべての部屋を知りたい** (複合クエリ)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    SQL と同様、結合演算子 (`AND`、`OR`、`NOT` など) を使用して、先行するクエリを結合することもできます。 このクエリは、`AND` を使用して、ツインの温度に関する先行するクエリを絞り込んでいます。 結果には、*floor0* 上の部屋のうち、温度が 75 度を超える部屋のみが表示されます。このケースでは、該当する部屋はありません。 結果セットは空になります。

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="複合クエリの結果 (結果なし) のスクリーンショット。" lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを終えたら、次に行う作業に応じて、削除するリソースを選択できます。

* **次のチュートリアルに進む場合** は、ここで設定したリソースを残しておいてください。この Azure Digital Twins インスタンスと構成済みのサンプル アプリを引き続き次のチュートリアルで使用します。

* **Azure Digital Twins インスタンスは引き続き使用するものの、そのモデル、ツイン、関係をすべて削除する場合** は、サンプル アプリの `DeleteAllTwins` コマンドと `DeleteAllModels` コマンドをそれぞれ使用して、インスタンスからツインとモデルを削除することができます。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

さらに、プロジェクト フォルダーもローカル コンピューターから削除してください。

## <a name="next-steps"></a>次のステップ 

このチュートリアルでは、サンプル クライアント アプリケーションを使用してインスタンスにグラフを作成することで、Azure Digital Twins の使用を開始しました。 モデル、デジタル ツイン、およびリレーションシップを作成して、グラフを形成しました。 また、Azure Digital Twins によって回答できる環境についての質問の種類を理解するために、グラフに対していくつかのクエリを実行しました。

次のチュートリアルに進み、Azure Digital Twins を他の Azure サービスと組み合わせて、データ ドリブンのエンド ツー エンドのシナリオを完了してください。
> [!div class="nextstepaction"]
> [*チュートリアル:エンド ツー エンドのソリューションを接続する*](tutorial-end-to-end.md)"