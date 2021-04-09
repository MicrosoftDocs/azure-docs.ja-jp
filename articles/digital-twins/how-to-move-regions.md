---
title: インスタンスを別の Azure リージョンに移動する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins インスタンスを Azure リージョン間で移動する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049850"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure Digital Twins インスタンスを別の Azure リージョンに移動する

Azure Digital Twins インスタンスをリージョン間で移動する必要がある場合、現在のプロセスでは、新しいリージョンでリソースを再作成してから元のリソースを削除します。 このプロセスの終わりには、更新された場所を除いて最初のものと同じである新しい Azure Digital Twins インスタンスを操作することになります。

この記事では、新しいインスタンスを元のものと一致させるために必要なすべてを完全に移動して、コピーする方法についてのガイダンスを提供します。

このプロセスには、次の手順が含まれます。

1. 準備:元のモデル、ツイン、グラフをダウンロードします。
1. 移動:新しい Azure Digital Twins インスタンスを新しいリージョンに作成します。
1. 移動:新しい Azure Digital Twins インスタンスにデータを再入力します。
    - 元のモデル、ツイン、グラフをアップロードします。
    - エンドポイントとルートを再作成します。
    - 接続されたリソースを再リンクします。
1. ソース リソースのクリーンアップ: 元のインスタンスを削除します。

## <a name="prerequisites"></a>前提条件

Azure Digital Twins インスタンスを再作成しようとしている場合は、事前に元のインスタンスのコンポーネントを確認し、再作成する必要があるすべての要素を明確に把握しておいてください。

考慮すべきいくつかの質問を次に示します。

* どのような *モデル* がインスタンスにアップロードされているか? いくつあるか?
* インスタンス内の *ツイン* は何か? いくつあるか?
* インスタンス内の "*グラフ*" の一般的な形状はどのようなものか? リレーションシップはいくつあるか?
* どのような *エンドポイント* がインスタンスにあるか?
* どのような *ルート* がインスタンスにあるか? フィルターはあるか?
* インスタンスはどこで *他の Azure サービスに接続* するか? 一般的な統合ポイントをいくつか以下に示します。

    - Azure Event Grid、Azure Event Hubs、または Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* インスタンスに接続する *個人または会社のアプリ* には、他にどのようなものがあるか?

この情報は、[Azure portal](https://portal.azure.com)、[Azure Digital Twins の API と SDK](how-to-use-apis-sdks.md)、[Azure Digital Twins CLI コマンド](how-to-use-cli.md)、または [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを使用して収集できます。

## <a name="prepare"></a>準備

このセクションでは、元のインスタンスから元のモデル、ツイン、グラフをダウンロードすることによってインスタンスを再作成するための準備を行います。 この記事では、このタスクに [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) サンプルを使用します。

>[!NOTE]
>モデルやグラフを含むファイルがインスタンスに既に存在している場合があります。 その場合、すべてを再度ダウンロードする必要はありません。欠落している要素や、これらのファイルを最初にアップロードした後に変更された可能性があるもののみをダウンロードしてください。 たとえば、新しいデータで更新されたツインがある場合があります。

### <a name="limitations-of-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer の制限事項

[Azure Digital Twins Explorer サンプル](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)は、グラフの視覚的な表現をサポートするクライアント アプリのサンプルであり、インスタンスとの視覚的な対話機能を備えています。 この記事では、これを使用してモデル、ツイン、グラフをダウンロードし、後で再アップロードする方法を示します。

このサンプルは完全なツールではありません。 ストレス テストが行われておらず、大きなサイズのグラフを処理するように構築されていませんでした。 そのため、次のようなすぐに使えるサンプルの制限事項に留意してください。

* このサンプルは現在、最大 1,000 ノードと 2,000 のリレーションシップのグラフ サイズでのみテストされています。
* 断続的なエラーが発生した場合の再試行は、このサンプルでサポートされません。
* このサンプルでは、アップロードされたデータが不完全かどうかについて、ユーザーに通知を行いません。
* このサンプルでは、メモリなどの使用可能なリソースを超える非常に大きなグラフによって発生するエラーの処理は行いません。

お使いのグラフのサイズをこのサンプルで処理できない場合は、他の Azure Digital Twins 開発者ツールを使用してグラフをエクスポートおよびインポートできます。

* [Azure Digital Twins CLI コマンド](how-to-use-cli.md)
* [Azure Digital Twins API および SDK](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Azure Digital Twins Explorer アプリケーションを設定する

Azure Digital Twins Explorer の設定を進めるには、まず、サンプル アプリケーション コードをダウンロードして設定し、コンピューター上で実行します。

サンプルを入手するには、[Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) に関するページを参照してください。 **[ZIP のダウンロード]** ボタンを選択して、このサンプル コードの .zip ファイルを、ご利用のコンピューターに **Azure_Digital_Twins__ADT__explorer.zip** としてダウンロードします。 ファイルを解凍します。

次に、Azure Digital Twins Explorer のアクセス許可を設定し、構成します。 Azure Digital Twins クイックスタートの「[Azure Digital Twins と Azure Digital Twins Explorer を設定する](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer)」セクションの指示に従ってください。 このセクションでは、次の手順について説明します。

1. Azure Digital Twins インスタンスを設定します。 インスタンスは既にあるため、この部分はスキップしてかまいません。
1. インスタンスへのアクセスを提供するようにローカルの Azure 資格情報を設定します。
1. Azure Digital Twins Explorer を実行し、お使いのインスタンスに接続するように構成します。 移動する元の Azure Digital Twins インスタンスの "*ホスト名*" を使用します。

これで、コンピューター上のブラウザーで Azure Digital Twins Explorer サンプル アプリが実行されます。 このサンプルが、元の Azure Digital Twins インスタンスに接続していることを確認します。

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="localhost:3000 で実行中のアプリがブラウザー ウィンドウに表示されている。このアプリは Azure Digital Twins Explorer と呼ばれ、クエリ エクスプローラー、モデル ビュー、グラフ ビュー、プロパティ エクスプローラーのボックスが含まれる。画面上にはまだデータがない。" lightbox="media/how-to-move-regions/explorer-blank.png":::

接続を確認するには、 **[クエリの実行]** ボタンを選択して既定のクエリを実行します。これにより、 **[グラフ エクスプローラー]** ボックスのグラフにすべてのツインとリレーションシップが表示されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンが強調表示されている。" lightbox="media/how-to-move-regions/run-query.png":::

この記事の後半で、Azure Digital Twins Explorer を再び使用して、これらのアイテムをターゲット リージョンの新しいインスタンスに再アップロードします。このため、Azure Digital Twins Explorer は実行したままでかまいません。

### <a name="download-models-twins-and-graph"></a>モデル、ツイン、グラフのダウンロード

次に、ソリューションのモデル、ツイン、グラフをコンピューターにダウンロードします。

これらのアイテムをすべて一度にダウンロードするには、まず、 **[グラフ ビュー]** ボックスに完全なグラフが表示されていることを確認します。 完全なグラフがまだ表示されていない場合は、 **[クエリ エクスプローラー]** ボックスで `SELECT * FROM digitaltwins` の既定のクエリを再実行します。
 
その後、 **[グラフ ビュー]** ボックス内の **[グラフのエクスポート]** アイコンを選択します。

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="[グラフ ビュー] ボックスで強調表示されているアイコン。クラウドから外に向かう矢印を示している。" lightbox="media/how-to-move-regions/export-graph.png":::

この操作を行うと、 **[グラフ ビュー]** ボックスで **[ダウンロード]** リンクが有効になります。 これを選択して、モデル、ツイン、リレーションシップを含む、クエリ結果の JSON ベースの表現をダウンロードします。 この操作を行うと、.json ファイルがコンピューターにダウンロードされるはずです。

>[!NOTE]
>ダウンロードしたファイルのファイル拡張子が違っている場合は、拡張子を直接編集して .json に変更してみてください。

## <a name="move"></a>詳細ビュー

次に、ターゲット リージョンに新しいインスタンスを作成して、インスタンスの "移動" を完了します。 その後、元のインスタンスのデータとコンポーネントを設定します。

### <a name="create-a-new-instance"></a>新しいインスタンスの作成

まず、Azure Digital Twins の新しいインスタンスをターゲット リージョンに作成します。 [インスタンスと認証の設定方法](how-to-set-up-instance-portal.md)に関するページの手順に従います。 これらの点に留意してください。

* 別のリソース グループにある "*場合*" は、新しいインスタンスは同じ名前のままにすることができます。 元のインスタンスを含む同じリソース グループを使用する必要がある場合、新しいインスタンスには別個の名前が必要になります。
* 場所の入力を求められたら、新しいターゲット リージョンを入力します。

この手順が完了した後、対象のデータを使用して設定を続けるために、新しいインスタンスのホスト名が必要になります。 セットアップ中にホスト名をメモしなかった場合は、[これらの手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)に従って Azure portal から今すぐ取得してください。

### <a name="repopulate-the-old-instance"></a>古いインスタンスを再設定する

次に、新しいインスタンスを設定して、元のもののコピーになるようにします。

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Azure Digital Twins Explorer を使用して元のモデル、ツイン、グラフをアップロードする

このセクションでは、モデル、ツイン、グラフを新しいインスタンスに再アップロードできます。 モデル、ツイン、またはグラフが元のインスタンスにないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#re-create-endpoints-and-routes)に進むことができます。

それ以外の場合は、Azure Digital Twins Explorer を実行しているブラウザー ウィンドウに戻り、これらの手順に従います。

##### <a name="connect-to-the-new-instance"></a>新しいインスタンスへの接続

Azure Digital Twins Explorer は現在、元の Azure Digital Twins インスタンスに接続しています。 ウィンドウの右上隅にある **[サインイン]** ボタンを選択して、新しいインスタンスを指すように接続を切り替えます。

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer のウィンドウの右上隅にある [サインイン] アイコンが強調表示されている。このアイコンには、人のシンプルなシルエットに鍵のシルエットが重なるように表示されている。" lightbox="media/how-to-move-regions/sign-in.png":::

**ADT URL** を新しいインスタンスに置き換えます。 *https://{新しいインスタンスのホスト名}* になるように、この値を変更します。

**[接続]** を選択します。 Azure の資格情報を使用して再度サインインするか、インスタンスに対する同意をこのアプリケーションに与えるかを求められる場合があります。

##### <a name="upload-models-twins-and-graph"></a>モデル、ツイン、グラフのアップロード

次に、以前にダウンロードしたソリューション コンポーネントを新しいインスタンスにアップロードします。

モデル、ツイン、グラフをアップロードするには、 **[グラフ ビュー]** ボックスにある **[グラフのインポート]** アイコンを選択します。 このオプションを選ぶと、これら 3 つのコンポーネントがすべて一度にアップロードされます。 また、グラフで現在使用されていないモデルもアップロードされます。

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="[グラフ ビュー] ボックスで強調表示されているアイコン。クラウドに向かう矢印を示している。" lightbox="media/how-to-move-regions/import-graph.png":::

ファイル選択ボックスで、ダウンロードしたグラフに移動します。 グラフの **.json** ファイルを選び、 **[開く]** を選択します。

数秒後、Azure Digital Twins Explorer で **[インポート]** ビューが開き、読み込まれるグラフのプレビューが表示されます。

グラフのアップロードを確認するには、 **[グラフ ビュー]** ボックスの右上隅にある **[保存]** アイコンを選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="[Graph Preview]\(グラフのプレビュー\) ペインで強調表示されている [保存] アイコン。" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

これで、Azure Digital Twins Explorer で (ツインとリレーションシップを含む) モデルとグラフが新しい Azure Digital Twins インスタンスにアップロードされるようになりました。 アップロードされたモデル、ツイン、リレーションシップの数を示す成功メッセージが表示されるはずです。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="グラフのインポート成功を示すダイアログ ボックス。インポートに成功し、2 つのモデル、4 つのツイン、2 つのリレーションシップがインポートされたことを示している。" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

すべてが正常にアップロードされたことを確認するには、 **[グラフ エクスプローラー]** ボックスにある **[クエリの実行]** ボタンを選択して、すべてのツインとリレーションシップをグラフに表示する既定のクエリを実行します。 この操作を行うと、 **[モデル ビュー]** ボックスのモデルの一覧も更新されます。

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="ウィンドウの右上隅にある [クエリの実行] ボタンの周囲が強調表示されている。" lightbox="media/how-to-move-regions/run-query.png":::

すべてのツインとリレーションシップを表示したグラフが **[グラフ エクスプローラー]** ボックスに表示されます。 **[モデル ビュー]** ボックスにもモデルの一覧が表示されます。

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="[モデル ビュー] ボックス内で強調表示された 2 つのモデルと、[グラフ エクスプローラー] ボックス内で強調表示されたグラフを示す Azure Digital Twins Explorer のビュー。" lightbox="media/how-to-move-regions/post-upload.png":::

これらのビューで、モデル、ツイン、グラフがターゲット リージョンの新しいインスタンスに再アップロードされたことを確認できます。

#### <a name="re-create-endpoints-and-routes"></a>エンドポイントとルートを再作成する

元のインスタンスにエンドポイントまたはルートがある場合、新しいインスタンスでそれらを再作成する必要があります。 エンドポイントまたはルートが元のインスタンスにないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#relink-connected-resources)に進むことができます。

それ以外の場合は、[エンドポイントとルートの管理方法](how-to-manage-routes-portal.md)に関するページの手順に従います。その際に新しいインスタンスを使用します。 これらの点に留意してください。

* エンドポイントに使用している Event Grid、Event Hubs、Service Bus リソースを再作成する必要は "*ありません*"。 詳細については、エンドポイントに関する指示の「前提条件」セクションを参照してください。 必要なのは、Azure Digital Twins インスタンスでエンドポイントを再作成することだけです。
* エンドポイントとルートの名前は別のインスタンスにスコープ設定されるため、再利用できます。
* 作成するルートに必要なフィルターがあれば、必ず追加してください。

#### <a name="relink-connected-resources"></a>接続されたリソースを再リンクする

元の Azure Digital Twins インスタンスに接続している他のアプリまたは Azure リソースがある場合、代わりに新しいインスタンスに到達するように接続を編集する必要があります。 これらのリソースには、他の Azure サービスや、Azure Digital Twins と連携するように設定した個人または会社のアプリが含まれる場合があります。

元のインスタンスに接続している他のリソースがないか、それらを新しいインスタンスに移動したくない場合は、スキップして[次のセクション](#verify)に進むことができます。

それ以外の場合は、対象のシナリオで接続されているリソースを考慮してください。 接続されているリソースを削除して再作成する必要はありません。 代わりに、そのホスト名を使用して Azure Digital Twins インスタンスに接続するポイントを編集するだけで済みます。 その後、元のではなく、新しいインスタンスのホスト名を使用するようにこれらのポイントを更新します。

編集する必要があるリソースは正確にはシナリオによって異なりますが、一般的な統合ポイントの例を次に示します。

* Azure Functions。 そのコードに元のインスタンスのホスト名が含まれている Azure 関数がある場合は、この値を新しいインスタンスのホスト名に更新して関数を再発行する必要があります。
* Event Grid、Event Hubs、または Service Bus。
* Logic Apps。
* Time Series Insights。
* Azure Maps。
* IoT Hub Device Provisioning Service。
* Azure の外部の個人または会社のアプリ。このようなクライアント アプリの例としては、「[チュートリアル: クライアント アプリをコーディングする](tutorial-code.md)」で作成した、インスタンスに接続して Azure Digital Twins API を呼び出すものがあります。
* Azure AD アプリ登録を再作成する必要は "*ありません*"。 [アプリ登録](how-to-create-app-registration.md)を使用して Azure Digital Twins API に接続する場合、新しいインスタンスで同じアプリ登録を再利用できます。

この手順を完了した後、ターゲット リージョンの新しいインスタンスが元のインスタンスのコピーになるはずです。

## <a name="verify"></a>確認

新しいインスタンスが正しく設定されたことを確認するには、次のツールを使用します。

* [Azure Portal](https://portal.azure.com) このポータルは、新しいインスタンスが存在し、正しいターゲット リージョンにあることを確認するのに適しています。 また、エンドポイントとルート、および他の Azure サービスへの接続を確認するのに適しています。
* [Azure Digital Twins CLI コマンド](how-to-use-cli.md)。 これらのコマンドは、新しいインスタンスが存在し、正しいターゲット リージョンにあることを確認するのに適しています。 また、これらはインスタンス データを確認するために使用できます。
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)。 Azure Digital Twins Explorer は、モデル、ツイン、グラフなどのインスタンス データを確認するのに適しています。
* [Azure Digital Twins API および SDK](how-to-use-apis-sdks.md)。 これらのリソースは、モデル、ツイン、グラフなどのインスタンス データを確認するのに適しています。 また、エンドポイントとルートの確認にも適しています。

元のインスタンスで実行していたカスタム アプリやエンドツーエンド フローを実行してみることもでき、これは、それらが新しいインスタンスで正しく動作していることを確認するのに役立ちます。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

これで、元のインスタンスのデータと接続のコピーを使用して新しいインスタンスがターゲット リージョンに設定されたので、元のインスタンスを削除することができます。

[Azure portal](https://portal.azure.com)、[Azure CLI](how-to-use-cli.md)、または[コントロール プレーン API](how-to-use-apis-sdks.md#overview-control-plane-apis) を使用できます。

Azure portal を使用してインスタンスを削除するには、ブラウザー ウィンドウで[ポータルを開き](https://portal.azure.com)、ポータルの検索バーで元の Azure Digital Twins インスタンスの名前を検索して、そこに移動します。

**[削除]** ボタンを選択し、画面の指示に従って削除を完了します。

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Azure portal の [概要] タブでの Azure Digital Twins インスタンスの詳細のビュー。[削除] ボタンが強調表示されている。":::