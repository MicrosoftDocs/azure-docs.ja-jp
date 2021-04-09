---
title: クイック スタート - サンプル シナリオを精査する
titleSuffix: Azure Digital Twins
description: クイック スタート - Azure Digital Twins Explorer サンプルを使用して、事前構築済みのシナリオを視覚化し、精査します。
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: feeb3e63375e918498e704add6dac672d6a46de3
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574579"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>クイック スタート - Azure Digital Twins Explorer を使用して、Azure Digital Twins のサンプル シナリオを精査する

Azure Digital Twins を使用すると、現実の環境のライブ モデルを作成して対話的に操作することができます。 最初に、個々の要素を "*デジタル ツイン*" としてモデル化します。 次に、ライブ イベントに応答して情報を照会できるナレッジ "*グラフ*" にそれらを接続します。

このクイックスタートでは、[Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) というサンプル アプリケーションを利用して、事前構築済みの Azure Digital Twins グラフを精査します。 Azure Digital Twins Explorer は、次の目的で使用します。

- 環境のデジタル表現をアップロードする。
- Azure Digital Twins で環境を表すために作成されたツインとグラフのビジュアル イメージを表示する。
- ブラウザー ベースの視覚的な操作を通じて、他の管理アクティビティを実行する。

このクイックスタートには主に次の手順が含まれています。

1. Azure Digital Twins インスタンスと Azure Digital Twins Explorer を設定する。
1. 事前構築済みのモデルとグラフ データをアップロードしてサンプル シナリオを構築する。
1. 作成されたシナリオ グラフを精査する。
1. グラフに変更を加える。

ここで使用するサンプル グラフは、2 つのフロアと 2 つの部屋から成る建物を表します。 グラフは次の画像のようになります。

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="矢印で接続された 4 つの円形ノードから成るグラフの画面。&quot;Floor1&quot; という円が &quot;contains&quot; という矢印によって &quot;Room1&quot; という円に接続されている。&quot;Floor0&quot; という円が &quot;contains&quot; という矢印によって &quot;Room0&quot; という円に接続されている。&quot;Floor1&quot; と &quot;Floor0&quot; は接続されていない。":::

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Azure サブスクリプションが必要です。 まだお持ちでない場合は、すぐに [無料で作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) できます。

お使いのコンピューターには **Node.js** も必要になります。 最新バージョンを入手するには、「[Node.js](https://nodejs.org/)」を参照してください。

最後に、クイックスタートの間に使用するサンプルをダウンロードする必要もあります。 サンプル アプリケーションは **Azure Digital Twins Explorer** です。 このサンプルには、クイックスタートで Azure Digital Twins シナリオを読み込んで精査する際に使用するアプリが含まれています。 また、サンプル シナリオ ファイルも含まれています。 サンプルを入手するには、[Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) に関するページに移動してください。 **[Download ZIP]\(ZIP のダウンロード\)** ボタンを選択して、このサンプル コードの .zip ファイルをご自分のマシンにダウンロードします。 **Azure_Digital_Twins__ADT__explorer.zip** フォルダーを解凍し、ファイルを抽出します。

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Azure Digital Twins と Azure Digital Twins Explorer を設定する

Azure Digital Twins を操作する際の最初の手順は、Azure Digital Twins インスタンスを設定することです。 サービスのインスタンスを作成し、Azure Digital Twins Explorer で認証する資格情報を設定した後、Azure Digital Twins Explorer でインスタンスに接続し、このクイックスタートの後半でサンプル データを設定できます。

このセクションの残りの部分では、これらの手順について説明します。

### <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

Azure Digital Twins Explorer アプリケーションは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (`Azure.Identity` ライブラリの一部) を使用してユーザーの認証を行います。 Azure Digital Twins に対してクライアント アプリの認証を行う各種の方法について詳しくは、「[アプリ認証コードを作成する](how-to-authenticate-client.md)」を参照してください。

この種の認証では、Azure Digital Twins Explorer によってローカル環境内で資格情報が検索されます。たとえば、ローカル [Azure CLI](/cli/azure/install-azure-cli) や Visual Studio または Visual Studio Code での Azure サインインなどです。 このため、Azure Digital Twins Explorer アプリの資格情報を設定するために、これらのメカニズムのいずれかを使用して、"*Azure にローカルでサインイン*" する必要があります。

これらの方法のいずれかを使用して既に Azure にサインインしている場合は、[次のセクション](#run-and-configure-azure-digital-twins-explorer)に進むことができます。

それ以外の場合は、これらの手順を使用してローカル Azure CLI をインストールできます。

1. [こちらのインストール リンク](/cli/azure/install-azure-cli)のプロセスに従って、お使いの OS に対応したインストールを完了します。
1. お使いのコンピューターでコンソール ウィンドウを開きます。
1. `az login` を実行し、認証プロンプトに従って Azure アカウントにサインインします。
1. 考えられる最後の手順:このアカウントで複数の Azure サブスクリプションを使用する場合は、`az account set --subscription "<your-subscription-name-or-ID>"` (サブスクリプションの名前または ID 値のいずれか) を実行して、Azure Digital Twins インスタンスを含む Azure サブスクリプションに認証コンテキストを設定します。

サインイン後、次のセクションで Azure Digital Twins Explorer を実行すると、Azure 資格情報が自動的に取得されます。

認証コンソール ウィンドウは、必要に応じて閉じてかまいません。 または、次の手順で使用するために開いたままでもかまいません。

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer を実行して構成する

次に、Azure Digital Twins Explorer アプリケーションを実行し、自分の Azure Digital Twins インスタンス用に構成します。

1. ダウンロードして解凍した **Azure_Digital_Twins__ADT__explorer** フォルダーに移動します。
コンソール ウィンドウを開いて、**Azure_Digital_Twins__ADT__explorer/client/src** フォルダーの場所に移動します。

1. `npm install` を実行して、必須の依存関係をすべてダウンロードします。

1. `npm run start` を実行してアプリを起動します。

   数秒後、ブラウザー ウィンドウが開き、ブラウザーにアプリが表示されます。

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは Azure Digital Twins Explorer と呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが含まれる。画面上にはまだデータがない。" lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. 設定したインスタンスを操作できるように Azure Digital Twins Explorer を構成するために、下の図に示されているように、ウィンドウ右上隅の **[サインイン]** ボタンを選択します。

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer のウィンドウ上部付近にあるサインイン アイコンが強調表示されている。このアイコンには、人のシンプルなシルエットに鍵のシルエットが重なるように表示されている。" lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. 「[Azure Digital Twins インスタンスを設定する](#set-up-an-azure-digital-twins-instance)」セクションで先ほど収集した Azure Digital Twins インスタンスの URL を *https://{インスタンス ホスト名}* の形式で入力します。

> [!TIP]
> 接続時に `SignalRService.subscribe` エラー メッセージが表示される場合は、Azure Digital Twins の URL が *https://* で始まっていることを確認してください。
>
> 認証エラーが表示された場合は、**環境変数** に含まれている資格情報が Azure Digital Twins に対して有効であることを確認してください。 `DefaultAzureCredential` は、[特定の順序](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)で資格情報の種類に対して認証を試み、環境変数が最初に評価されます。

Microsoft の **[要求されているアクセス許可]** ポップアップ ウィンドウが表示された場合は、このアプリケーションに同意し、承諾して続行します。

>[!NOTE]
> 同じアイコンを選択して **[サインイン]** ボックスを再び開くことで、この情報はいつでも再表示または編集できます。 指定した値は維持されます。

## <a name="add-the-sample-data"></a>サンプル データを追加する

次に、Azure Digital Twins Explorer にサンプル シナリオとグラフをインポートします。 サンプル シナリオは、先ほどダウンロードした **Azure_Digital_Twins__ADT__explorer** フォルダーにもあります。

### <a name="models"></a>モデル

Azure Digital Twins ソリューションで実行する最初の手順は、対象となる環境のボキャブラリを定義することです。 カスタム [モデル](concepts-models.md)を作成し、実際の環境に存在するエンティティのタイプを記述します。

各モデルは、デジタル ツイン定義言語 (DTDL) と呼ばれる JSON-LD のような言語で記述します。 各モデルで、"*プロパティ*"、"*テレメトリ*"、"*リレーションシップ*"、"*コンポーネント*" の観点から、1 種類のエンティティを記述します。 これらのモデルは、これらのタイプの特定インスタンスを表すデジタル ツインの基礎として後で使用します。

通常、モデルを作成する場合、次の 3 つの手順を実行します。

1. モデルの定義を作成する。 このクイックスタートでは、この手順は、サンプル ソリューションの一環として既に完了しています。
1. それを検証して構文が正確であることを確認する。 このクイックスタートでは、この手順は、サンプル ソリューションの一環として既に完了しています。
1. それを自分の Azure Digital Twins インスタンスにアップロードする。
 
このクイックスタートでは、モデル ファイルが既に作成および検証されています。 これらは、ダウンロードしたソリューションと共に含まれています。 このセクションでは、事前に作成された 2 つのモデルを自分のインスタンスにアップロードして、建物の環境を構成するこちらの要素を定義します。

* 床
* ルーム

#### <a name="upload-models"></a>モデルのアップロード

次の手順に従いモデルをアップロードします。

1. **[モデル ビュー]** ボックスの **[Upload a Model]\(モデルのアップロード\)** アイコンを選択します。

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="[モデル ビュー] ボックスの中央のアイコンが強調表示されている。クラウドに向かう矢印を示している。" lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. 表示されたファイル セレクター ボックスで、ダウンロードしたリポジトリの **Azure_Digital_Twins__ADT__explorer/client/examples** フォルダーに移動します。
1. **Room.json** と **Floor.json** を選択し、 **[OK]** を選択します。 必要に応じて別のモデルもアップロードできますが、このクイックスタートでは使用しません。
1. Azure アカウントへのサインインを求めるポップアップ ダイアログ ボックスに従います。

>[!NOTE]
>次のようなエラー メッセージが表示されることがあります。:::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="&quot;Error: Error fetching models: ClientAuthError: Error opening popup window. This can happen if you are using IE or if popups are blocked in the browser. &quot; (エラー: モデルをフェッチしているときにエラーが発生しました: ClientAuthError: ポップアップ ウィンドウを開いているときにエラーが発生しました。これは、IE を使用しているか、ポップアップがブラウザーでブロックされている場合に発生します。) という、一番下に [閉じる] ボタンを備えたポップアップ ボックスが表示される。" border="false"::: 
> ポップアップ ブロッカーを無効にするか、別のブラウザーを使用してみてください。

これで、Azure Digital Twins Explorer によって、これらのモデル ファイルが Azure Digital Twins インスタンスにアップロードされます。 これらは、フレンドリ名と完全なモデル ID と共に **[モデル ビュー]** ボックスに表示されます。 **[モデルの表示]** という情報アイコンを選択すると、それらの背後にある DTDL コードが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Floor (dtmi:example:Floor;1) と Room (dtmi:example:Room;1) という 2 つのモデルの定義が一覧表示されている [モデル ビュー] ボックス。各モデルの [モデルの表示] 情報アイコン (円の中にアルファベットの &quot;i&quot;) が強調表示されている。" lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>ツインとツイン グラフ

自分の Azure Digital Twins インスタンスにいくつかのモデルをアップロードしたら、そのモデルの定義に準拠した[デジタル ツイン](concepts-twins-graph.md)を追加できます。

デジタル ツインは、ビジネス環境内の実際のエンティティを表します。 たとえば、農場のセンサーや車内の照明などです。このクイックスタートでは、建物のフロアにある部屋です。 特定のモデル タイプのツインを複数作成できます (同じ *Room* モデルを使用する複数の部屋など)。 これらをリレーションシップで接続し、環境全体を表す *ツイン グラフ* に反映することができます。

このセクションでは、接続された事前作成済みのツインをアップロードして、事前作成済みのグラフに反映します。 このグラフには、2 つのフロアと 2 つの部屋が含まれていて、次のレイアウトで接続されています。

* Floor0
    - Room0 を含む
* Floor1
    - Room1 を含む

#### <a name="import-the-graph"></a>グラフをインポートする

こちらの手順に従いグラフをインポートします。

1. **[グラフ ビュー]** ボックスの **[Import Graph]\(グラフのインポート\)** アイコンを選択します。

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="[グラフ ビュー] ボックスで強調表示されているアイコン。クラウドに向かう矢印を示している。" lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. ファイル セレクター ボックスで **Azure_Digital_Twins__ADT__explorer/client/examples** フォルダーに移動し、**buildingScenario.xlsx** スプレッドシート ファイルを選択します。 このファイルには、サンプル グラフの説明が含まれています。 **[OK]** を選択します。

   数秒後、Azure Digital Twins Explorer で **[インポート]** ビューが開き、読み込まれるグラフのプレビューが表示されます。

3. グラフのアップロードを確認するには、 **[グラフ ビュー]** ボックスの右上隅にある **[保存]** アイコンを選択します。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="[Graph Preview]\(グラフのプレビュー\) ペインで強調表示されている [保存] アイコン。" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. これで、アップロードしたファイルを使用して、要求したツインとそれらの間のリレーションシップが Azure Digital Twins Explorer によって作成されます。 終了すると、ダイアログ ボックスが表示されます。 **[閉じる]** を選択します。

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="グラフのインポート成功を示すダイアログ ボックス。インポートに成功し、4 つのツイン、2 つのリレーションシップがインポートされたことを示している。" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. これでグラフが Azure Digital Twins Explorer にアップロードされました。 グラフを表示するには、Azure Digital Twins Explorer ウィンドウの上部付近にある **Graph エクスプローラー** ボックスの **[クエリの実行]** ボタンを選択します。

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンが強調表示されている。" lightbox="media/quickstart-adt-explorer/run-query.png":::

このアクションにより、すべてのデジタル ツインを選択して表示する既定のクエリが実行されます。 Azure Digital Twins Explorer によって、サービスからすべてのツインとリレーションシップが取得されます。 これらによって定義されたグラフが **[グラフ ビュー]** ボックスに描画されます。

## <a name="explore-the-graph"></a>グラフを精査する

次は、アップロードしたサンプル シナリオのグラフを確認しましょう。

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="ツイン グラフが存在する [グラフ ビュー] ボックスのビュー。&quot;floor1&quot; という円が &quot;contains&quot; という矢印によって &quot;room1&quot; という円に接続されている。&quot;floor0&quot; という円が &quot;contains&quot; という矢印によって &quot;room0&quot; という円に接続されている。":::

円 (グラフの "ノード") は、デジタル ツインを表します。 線はリレーションシップを表します。 **Floor0** ツインには **Room0** が含まれており、**Floor1** ツインには **Room1** が含まれています。

マウスを使用する場合、グラフの要素をドラッグしてあちこちに動かすことができます。

### <a name="view-twin-properties"></a>ツインのプロパティを表示する

ツインを選択すると、そのプロパティと値を **[PROPERTY EXPLORER]\(プロパティ エクスプローラー\)** ボックスに一覧表示できます。

次に示すのは、Room0 のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="枠で強調表示された [Property Explorer]\(プロパティ エクスプローラー\) ボックスに Room0 のプロパティが表示されている。たとえば、$dtId フィールドは Room0、Temperature フィールドは 70、Humidity フィールドは 30 となっている。" lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 の温度は 70 度です。

次に示すのは、Room1 のプロパティです。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="枠で強調表示された [Property Explorer]\(プロパティ エクスプローラー\) ボックスに Room1 のプロパティが表示されている。たとえば、$dtId フィールドは Room1、Temperature フィールドは 80、Humidity フィールドは 60 となっている。" lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 の温度は 80 度です。

### <a name="query-the-graph"></a>グラフのクエリを行う

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。

グラフ内のツインに対してクエリを実行する方法の 1 つは、それらの "*プロパティ*" を条件にすることです。 プロパティに基づいてクエリを実行すると、さまざまな質問への回答を得やすくなります。 たとえば、注意が必要な場合がある環境内の外れ値を見つけることができます。

このセクションでは、クエリを実行して、温度が 75 度を上回っている環境内のツインの数はいくつかという質問への回答を得ます。

回答を得るためには、 **[QUERY EXPLORER]\(クエリ エクスプローラー\)** ボックスで次のクエリを実行します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

先ほどツインのプロパティを確認したことを思い出してください。Room0 の温度は 70 度、Room1 の温度は 80 度です。 したがって、ここで結果として返されるのは Room1 のみです。
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="プロパティ クエリの結果 (Room1 のみが表示されている)。" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> 前述のクエリ内では、その他の比較演算子 (<、>、=、!=) もサポートされます。 これらの演算子、さまざまな値、さまざまなツイン プロパティをクエリに組み込んで、独自の質問の答えを導き出してみてください。

## <a name="edit-data-in-the-graph"></a>グラフのデータを編集する

グラフに表されたツインのプロパティは、Azure Digital Twins Explorer を使用して編集できます。 このセクションでは、Room0 の温度を 76 度に上げます。

最初に、 **[PROPERTY EXPLORER]\(プロパティ エクスプローラー\)** ボックスで **Room0** を選択し、そのプロパティ リストを呼び出します。

このリストのプロパティは編集することができます。 温度の値 **70** を選択して、新しい値を入力できるようにします。 「**76**」と入力し、 **[保存]** アイコンを選択して、温度を **76** 度に更新します。

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Room0 のプロパティを表示する [Property Explorer]\(プロパティ エクスプローラー\) ボックス。温度の値が編集可能な状態で &quot;76&quot; と表示されている。また、[保存] アイコンが強調表示されている。" lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これで、 **[パッチ情報]** ウィンドウが表示され、更新を実行するための Azure Digital Twins [API](how-to-use-apis-sdks.md) と共に、バックグラウンドで使用されたパッチ コードが表示されます。 **[閉じる]** を選択します。

### <a name="query-to-see-the-result"></a>クエリを実行して結果を確認する

グラフでの更新内容が Room0 の温度に正常に登録されたことを確認するには、先ほどのクエリを再実行して、温度が 75 度を超えるすべてのツインを環境から取得します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Room0 の温度が 70 度から 76 度に変更されたので、今度は、両方のツインが結果に表示されているはずです。

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="プロパティ クエリの結果 (Room0 と Room1 の両方が表示されている)。" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>学習内容の復習とまとめ

このクイックスタートでは、Azure Digital Twins インスタンスを作成して Azure Digital Twins Explorer に接続し、それにサンプル シナリオを設定しました。

その後、次のようにしてグラフを精査しました。

* クエリを使用して、シナリオについての質問の回答を得る。
* デジタル ツインのプロパティを編集する。
* 更新の結果、どのように回答が変化したかを調べるために同じクエリを再実行しました。

この演習の意図は、Azure Digital Twins のグラフをどのように使用すれば、環境が絶えず変化する中で、対象となる環境についての質問への回答が得られるかを示すことです。

このクイックスタートでは、温度を手動で更新しました。 Azure Digital Twins では、デジタル ツインがテレメトリ データに基づいて自動的に更新を受信できるよう、それらを実際の IoT デバイスに接続するのが一般的です。 このようにすると、環境の実際の状態を常に反映するライブ グラフを作成できます。 クエリを使用すると、環境内で起きていることについての情報をリアルタイムで取得できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートの作業を終了するために、まず、実行中のコンソール アプリを終了してください。 この操作により、ブラウザーで Azure Digital Twins Explorer アプリへの接続が切断されます。 ブラウザーでライブ データを表示できなくなります。 ブラウザー タブは閉じてかまいません。

次に行う作業に応じて、削除するリソースを選択できます。

* **Azure Digital Twins のチュートリアルを続行する場合**、このクイックスタートのインスタンスをそれらの記事で再利用できるため、インスタンスを削除する必要はありません。

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

さらに、プロジェクト フォルダーもローカル コンピューターから削除してください。

## <a name="next-steps"></a>次のステップ

引き続き Azure Digital Twins のチュートリアルに進んで、独自の Azure Digital Twins シナリオとインタラクション ツールを作成しましょう。

> [!div class="nextstepaction"]
> [チュートリアル:クライアント アプリをコーディングする](tutorial-code.md)
