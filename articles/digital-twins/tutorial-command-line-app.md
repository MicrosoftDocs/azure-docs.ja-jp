---
title: チュートリアル:サンプル クライアント アプリを使用した基本事項の確認"
titleSuffix: Azure Digital Twins
description: サンプル コマンド ライン アプリケーションを使用して Azure Digital Twins SDK を試すためのチュートリアル
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: a1dc42815167da308fd87b541c0f21d02b47329b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022515"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>チュートリアル:サンプル クライアント アプリを使用して Azure Digital Twins を試す

このチュートリアルでは、Azure Digital Twins インスタンスとやり取りするためのコマンド ライン クライアント アプリケーションを実装したサンプル アプリケーションを紹介します。 このクライアント アプリは、"[*クライアント アプリのコーディングに関するチュートリアル*](tutorial-code.md)" で作成したアプリに似ています。

このサンプルを使用して、モデルのアップロード、ツインの作成と変更、リレーションシップの作成など、基本的な Azure Digital Twins アクションを実行することができます。 サンプル コードを見て、Azure Digital Twins の API について学んだり、サンプル プロジェクトに自由に変更を加えながら、独自のコマンドを実装する練習をしたりすることもできます。

このチュートリアルでは次のことを行います。
> [!div class="checklist"]
> * Azure Digital Twins インスタンスを設定する
> * そのインスタンスとやり取りするようにサンプル コマンド ライン アプリを構成する
> * コマンド ライン アプリを使用して Azure Digital Twins を試す (**モデル**、**デジタル ツイン**、**リレーションシップ**、**クエリ**など)

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>サンプル ソリューションを試す

インスタンスとサンプル アプリの構成が済んだら、サンプル プロジェクトと事前に作成してあるいくつかのサンプル コードを使用して、基本的な Azure Digital Twins ソリューションをビルドして試してみましょう。 主なソリューション コンポーネントは、**モデル**、**デジタル ツイン**、**リレーションシップ**です。結果的に、環境に対するクエリ可能な**ツイン グラフ**が得られます。

### <a name="model-a-physical-environment-with-dtdl"></a>DTDL を使用して物理環境をモデル化する

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、対象となる環境のツイン [**モデル**](concepts-models.md)の定義です。 

モデルは、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、[デジタル ツイン](concepts-twins-graph.md)のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 モデルは、**Digital Twins Definition Language (DTDL)** という JSON に似た言語で記述され、ツインの "*プロパティ*"、"*テレメトリ*"、"*リレーションシップ*"、"*コンポーネント*" を定義することができます。

> [!NOTE]
> デジタル ツインに対する "*コマンド*" も DTDL で定義できます。 ただし Azure Digital Twins サービスでは現在、コマンドがサポートされていません。

_**AdtE2ESample**_ プロジェクトを開いた Visual Studio ウィンドウで、 *[ソリューション エクスプローラー]* ペインを使用して *AdtSampleApp\SampleClientApp\Models* フォルダーに移動します。 このフォルダーにサンプル モデルが格納されています。

*[Room.json]* を選択して編集ウィンドウで開き、次のように変更を加えます。

* **バージョン番号を更新する**。このモデルのバージョンを更新することを示します。 そのためには、`@id` 値の末尾にある *1* を *2* に変更します。 現在のバージョン番号よりも大きい数値であれば、何でもかまいません。
* **プロパティを編集する**。 `Humidity` プロパティの名前を *HumidityLevel* に変更します (必要であれば、他の名前でもかまいません。 *HumidityLevel* 以外の名前を使用した場合は、その名前を覚えておいて、このチュートリアル全体の *HumidityLevel* の代わりにそれを使用し続けてください)。
* **プロパティを追加する**。 `HumidityLevel` プロパティの最後 (15 行目) に続けて、次のコードを貼り付け、`RoomName` プロパティを room に追加します。

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **リレーションシップを追加する**。 先ほど追加した `RoomName` プロパティの下に次のコードを貼り付けて、このタイプのツインが他のツインとの間で *contains* のリレーションシップを形成できるようにします。

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

作業が終わったら、更新後のモデルは次のようになります。

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="編集後の Room.json (バージョン番号、HumidityLevel プロパティと RoomName プロパティ、contains リレーションシップが更新されている)" border="false":::

次に進む前に、必ずファイルを保存してください。

> [!TIP]
> 独自のモデルを作成してみたい場合は、*Room* モデルのコードを新しいファイルに貼り付けて、 *.json* という拡張子で *AdtSampleApp\SampleClientApp\Models* フォルダーに保存してください。 その後、望みのものを表現するために、自由にプロパティやリレーションシップを追加してみましょう。 このフォルダーにある他のサンプル モデルも参考にしてください。

> [!TIP] 
> あらゆる言語に対応した [DTDL Validator サンプル](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)を利用してモデル ドキュメントをチェックし、DTDL が有効であることを確認できます。 これは DTDL パーサー ライブラリをベースに構築されています。詳細については、"[*モデルを解析および検証する方法*](how-to-parse-models.md)" に関するページを参照してください。

### <a name="get-started-with-the-command-line-app"></a>コマンド ライン アプリを使ってみる

モデルを定義したら、残りの手順として、サンプル アプリを使って Azure Digital Twins インスタンスとやり取りしてみましょう。 ツール バーにある次のボタンでプロジェクトを実行します。

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Visual Studio のスタート ボタン (SampleClientApp プロジェクト)":::

コンソール ウィンドウが表示されて認証が行われ、コマンドを待つ状態となります。 
* 認証はブラウザーを通じて処理されます。つまり既定の Web ブラウザーが開いて、認証プロンプトが表示されます。 このプロンプトから Azure の資格情報を使用してサインインしてください。 その後は、ブラウザーのタブまたはウィンドウを閉じてかまいません。

次に示したのは、プロジェクト コンソールの画面例を示すスクリーンショットです。

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="コマンド ライン アプリからのウェルカム メッセージ":::

> [!TIP]
> このプロジェクトで使用できる全コマンドの一覧を確認するには、プロジェクト コンソールで「`help`」と入力し、Return キーを押します。
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="help コマンドの出力":::

以降このチュートリアルの手順に取り組む間、プロジェクト コンソールは実行したままにしてください。

#### <a name="upload-models-to-azure-digital-twins"></a>Azure Digital Twins にモデルをアップロードする

モデルを設計したら、Azure Digital Twins インスタンスにアップロードする必要があります。 そうすることで、カスタム ドメインの独自のボキャブラリを使用して、Azure Digital Twins サービス インスタンスが構成されます。 モデルをアップロードしたら、そのモデルを使用するツイン インスタンスを作成できます。

プロジェクト コンソール ウィンドウで、次のコマンドを実行して、更新済みの *Room* モデルと、次のセクションで異なるタイプのツインを作成するために使用する *Floor* モデルをアップロードします。

```cmd/sh
CreateModels Room Floor
```

出力結果を見ると、モデルが正常に作成されたことがわかります。

> [!TIP]
> 先ほど独自のモデルを設計した場合は、そのモデルもここでアップロードできます。そのためには、上記コマンドの `Room Floor` リストに、対応するファイル名 (拡張子は省略可) を追加してください。

`GetModels true` コマンドを実行して、モデルが作成されたことを確認します。 アップロード済みのすべてのモデルが Azure Digital Twins インスタンスに対して照会され、そのすべての情報が出力されます。 この結果から、編集済みの *Room* モデルを探してみましょう。

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="GetModels の結果 (更新済みの Room モデルが表示されている)":::

#### <a name="errors"></a>エラー

このサンプル アプリケーションは、サービスからのエラーも処理します。 

`CreateModels` コマンドを再実行して、もう一度、先ほどアップロードしたものと同じモデルをどれか再アップロードしてみてください。

```cmd/sh
CreateModels Room
```

モデルを上書きすることはできないので、今度はサービス エラーが返されます。
既存のモデルを削除する方法について詳しくは、"[*カスタム モデルを管理する方法*](how-to-manage-model.md)" に関するページを参照してください。
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

### <a name="create-digital-twins"></a>デジタル ツインを作成する

Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に基づいて[**デジタル ツイン**](concepts-twins-graph.md)を作成できます。 デジタル ツインは、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 

デジタル ツインを作成するには、`CreateDigitalTwin` コマンドを使用します。 ツインのベースとなるモデルを参照する必要があります。モデルのプロパティには、必要に応じて初期値を定義することができます。 この段階では、リレーションシップ情報を渡す必要はありません。

実行中のプロジェクト コンソールでこのコードを実行すると、いくつかのツインが作成されます。先ほど更新した *Room* モデルに基づくツインと、もう 1 つのモデル (*Floor*) に基づくツインです。 *Room* には 3 つのプロパティがあったことを思い出してください。それらの初期値を引数で指定することができます。

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> 先ほど独自のモデルをアップロードした場合は、上記のコマンドを参考に `CreateDigitalTwin` コマンドをカスタマイズし、独自のモデル タイプのツインを追加してください。

コマンドから返される出力を見ると、ツインが正常に作成されたことがわかります。 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="CreateDigitalTwin コマンドから返された結果の抜粋 (floor0、floor1、room0、room1)":::

ツインが作成されたことは、`Query` コマンドを実行することによって確認することもできます。 Azure Digital Twins インスタンスに対し、そこに含まれるすべてのデジタル ツインがこのコマンドによって照会されます。 その結果から、*floor0*、*floor1*、*room0*、*room1* の各ツインを探してみましょう。

#### <a name="modify-a-digital-twin"></a>デジタル ツインに変更を加える

作成したツインのプロパティに変更を加えることもできます。 次のコマンドを実行して、*room0* の RoomName を *Room0* から *PresidentialSuite* に変更してみましょう。

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

出力結果を見ると、ツインが正常に更新されたことがわかります。

次のコマンドを実行して *room0* の情報を表示することで確認することもできます。

```cmd/sh
GetDigitalTwin room0
```

更新後の名前が出力結果に反映されているはずです。

> [!NOTE]
> 基になる REST API では、ツインに対する更新を定義するために、JSON パッチが使用されます。 その形式がコマンド ライン アプリにも反映されているので、基になる API で実際に必要となる形式を試すことができます。

### <a name="create-a-graph-by-adding-relationships"></a>リレーションシップを追加してグラフを作成する

次に、ツイン間にいくつかの**リレーションシップ**を作成することで、それらのツインを接続し、[**ツイン グラフ**](concepts-twins-graph.md)を形成することができます。 ツイン グラフは、環境全体を表すために使用されます。 

リレーションシップを追加するには、`CreateRelationship` コマンドを使用します。 リレーションシップの接続元となるツインと、追加するリレーションシップの種類、リレーションシップの接続先のツインを指定してください。 最後に、リレーションシップの名前 (ID) を指定します。

次のコードを実行すると、先ほど作成した各 *Floor* ツインから対応する *Room* ツインへの "contains" リレーションシップが追加されます。 そのためには、*Floor* モデルに *contains* リレーションシップが定義されていなければならない点に注意してください。

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

リレーションシップが正常に作成されたことが、コマンドから返される出力で確認できます。

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="CreateRelationship コマンドの結果の抜粋 (relationship0 および relationship1)":::

次のいずれかのコマンドを使用し、Azure Digital Twins インスタンスにリレーションシップを照会することで、リレーションシップを確認することもできます。
* 各フロアを接続元とするすべてのリレーションシップを確認する (一方の側からリレーションシップを表示)
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* 各部屋を接続先とするすべてのリレーションシップを表示する ("もう一方の" 側からリレーションシップを表示)
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* リレーションシップを個別に照会する 
    ```cmd/sh
    GetRelationship floor0 contains relationship0
    GetRelationship floor1 contains relationship1
    ```

このチュートリアルで設定したツインとリレーションシップによって、次の概念グラフが形成されます。

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="グラフ (floor0 は relationship0 によって room0 に接続され、floor1 は relationship1 によって room1 に接続されている)" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>環境についての質問をツイン グラフに照会する

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。 イメージしやすいよう、実行中のプロジェクト コンソールで次のコマンドを実行してみましょう。

* **Azure Digital Twins で表されている環境内のすべてのエンティティを知りたい** (すべて照会)

    ```cmd/sh
    Query
    ```

    これで環境の概要を調べ、必要な事柄がすべて Azure Digital Twins 内で表現されていることを確認できます。 このコマンドを実行すると、個々のデジタル ツインとその詳細を含んだ出力が返されます。 その抜粋を次に示します。

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="ツイン クエリの実行結果の抜粋 (room0 および floor1 が表示されている)":::

    >[!NOTE]
    >引数が追加されていない `Query` コマンドは、`Query SELECT * FROM DIGITALTWINS` に相当します。

* **環境内に存在する部屋をすべて知りたい** (モデルで照会)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    クエリを特定のタイプのツインに制限することで、表現されている内容についての、より具体的な情報を取得することができます。 このコマンドを実行すると、*room0* と *room1* は表示されますが、*floor0* と *floor1* は (room ではなく floor であるため) **表示されません**。
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="モデル クエリの結果 (room0 と room1 のみが表示されている)":::

* ***floor0* に存在するすべての部屋を知りたい** (リレーションシップで照会)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    グラフ内のリレーションシップに基づいてクエリを実行すると、ツインの関係性についての情報を入手したり、クエリを特定のエリアに制限したりすることができます。 *floor0* に存在するのは *room0* のみです。したがって、結果に含まれる部屋は room0 のみとなります。

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="リレーションシップ クエリの結果 (room0 が表示されている)":::

* **自分の環境に存在するツインのうち、温度が 75 度を超えるツインをすべて知りたい** (プロパティで照会)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    プロパティに基づいてグラフを照会することにより、さまざまな質問への答えを得ることができます。たとえば、環境内で注意すべき外れ値を見つけることもできます。 その他の比較演算子 ( *<* , *>* 、 *=* 、 *!=* ) もサポートされます。 ここでは、温度が 80 である *room1* が結果として表示されます。

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="プロパティ クエリの結果 (room1 のみが表示されている)":::

* ***floor0* 上で温度が 75 度を超えるすべての部屋を知りたい** (複合クエリ)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    SQL と同様、結合演算子 (`AND`、`OR`、`NOT` など) を使用して、先行するクエリを結合することもできます。 このクエリは、`AND` を使用して、ツインの温度に関する先行するクエリを絞り込んでいます。 結果には、*floor0* 上の部屋のうち、温度が 75 度を超える部屋のみが表示されます。このケースでは、該当する部屋はありません。 結果セットは空になります。

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="複合クエリの結果 (結果なし)":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルのプロジェクトは、次のチュートリアル ("[*エンド ツー エンドのソリューションを接続する方法のチュートリアル*](tutorial-end-to-end.md)") の土台となっています。 次のチュートリアルに進む場合は、ここで設定したリソースを残しておいてください。この Azure Digital Twins インスタンスと構成済みのサンプル アプリを引き続き使用します。
* この場合、サンプル アプリの `DeleteAllTwins` コマンドと `DeleteAllModels` コマンドをそれぞれ使用して、ツインとモデルをインスタンスから消去してください。 次のチュートリアルに備えて白紙の状態にすることができます。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後に、ローカル コンピューターにダウンロードしたプロジェクトのサンプル フォルダーを削除します。

## <a name="next-steps"></a>次のステップ 

このチュートリアルでは、Azure Digital Twins インスタンスを設定し、そのインスタンスとやり取りするためのクライアント アプリケーションをセットアップして、その基本的な使い方を学びました。 クライアント アプリを使用して Azure Digital Twins に触れながら、モデルやデジタル ツイン、リレーションシップを作成しました。 また、環境についての質問への回答として、どのような類の情報を Azure Digital Twins から取得できるのかが、このソリューションにいくつかのクエリを実行することでイメージしていただけたかと思います。

次のチュートリアルに進み、サンプル コマンド ライン アプリと他の Azure サービスとを組み合わせて、データ ドリブンのエンド ツー エンドのシナリオに取り組んでください。
> [!div class="nextstepaction"]
> [*チュートリアル:エンド ツー エンドのソリューションを接続する*](tutorial-end-to-end.md)"