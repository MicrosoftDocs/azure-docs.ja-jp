---
title: データを処理するための関数を Azure で設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる関数を Azure で作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f1ed4b9beda9848bba8fb12783f49dcf8016d3dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590621"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>データを処理するために Azure Functions アプリを接続する

データに基づくデジタル ツインの更新は、[Azure Functions](../azure-functions/functions-overview.md) を使用して作成された関数などのコンピューティング リソースを介し、[**イベント ルート**](concepts-route-events.md)を使用して処理されます。 関数を使用すると、以下に対応してデジタル ツインを更新することができます。
* IoT Hub からのデバイス テレメトリ データ
* ツイン グラフ内の別のデジタル ツインから取得されたプロパティ変更または他のデータ

この記事では、Azure Digital Twins で使用する関数を Azure で作成する方法について順を追って説明します。 

説明する手順の概要は次のとおりです。

1. Visual Studio で Azure Functions プロジェクトを作成する
2. [Event Grid](../event-grid/overview.md) トリガーを使用して関数を作成する
3. 認証コードを関数に追加する (Azure Digital Twins にアクセスできるようにするため)
4. 関数アプリを Azure に発行する
5. 関数アプリの[セキュリティ](concepts-security.md) アクセスを設定する

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>前提条件:Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio で関数アプリを作成する

Visual Studio 2019 で、 _[ファイル] > [新規作成] > [プロジェクト]_ を選択し、_Azure Functions_ テンプレートを検索します。 _[次へ]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio の新規プロジェクト ダイアログを示すスクリーンショット。Azure Functions のプロジェクト テンプレートが強調表示されています。":::

関数アプリの名前を指定して、 _[作成]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="新しいプロジェクトを構成するためのダイアログが表示されている Visual Studio のスクリーンショット (プロジェクト名、保存場所、新しいソリューションを作成するための選択肢、ソリューション名など)。":::

関数アプリの種類で *[イベント グリッド トリガー]* を選択して、 _[作成]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="新しい Azure Functions アプリケーションを作成するためのダイアログが表示されている Visual Studio のスクリーンショット。[イベント グリッド トリガー] オプションが強調表示されています。":::

関数アプリを作成すると、Visual Studio によってプロジェクト フォルダー内の **Function1.cs** ファイルにコード サンプルが生成されます。 この短い関数は、イベントをログに記録するために使用されます。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="作成された新しいプロジェクトがプロジェクト ウィンドウに表示されている Visual Studio のスクリーンショット。Function1 という名前のサンプル関数のコードが含まれています。" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Event Grid トリガーを使用して関数を作成する

関数アプリに SDK を追加することによって、関数を作成できます。 関数アプリと Azure Digital Twins のやり取りには、[.NET (C#) 用の Azure Digital Twin SDK](/dotnet/api/overview/azure/digitaltwins/client) を使用します。 

SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。 Visual Studio の NuGet パッケージ マネージャーを使用してパッケージをインストールするか、コマンドライン ツールで `dotnet` を使用してパッケージを追加することができます。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

次に、Visual Studio のソリューション エクスプローラーで、サンプル コードが含まれる _Function1.cs_ ファイルを開きます。その関数にこれらのパッケージの以下の `using` ステートメントを追加します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>関数に認証コードを追加する

次に、クラス レベルの変数を宣言し、関数から Azure Digital Twins にアクセスできるようにする認証コードを追加します。 次のものを _Function1.cs_ ファイル内の関数に追加します。

* Azure Digital Twins サービスの URL を **環境変数** として読み取るコード。 関数内にハードコーディングするのではなく、環境変数からサービス URL を読み取ることをお勧めします。 この環境変数の値は、[この記事の後半で](#set-up-security-access-for-the-function-app)設定します。 環境変数の詳細については、「[*お使いの関数アプリの管理*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* HttpClient インスタンスを保持する静的変数。 HttpClient は作成に比較的コストがかかるため、すべての関数呼び出しでこれを実行する必要がないようにします。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Azure Functions では、マネージド ID の資格情報を使用できます。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 関数の内部にローカル変数 _DigitalTwinsClient_ を追加して、Azure Digital Twins クライアント インスタンスを保持します。 クラス内でこの変数を static に "*しないでください*"。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* _adtInstanceUrl_ の null チェックを追加し、関数のロジックを try と catch のブロックで囲んで例外をキャッチします。

これらの変更を行った後、関数コードは次のようになります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

これでアプリケーションを作成できました。次のセクションの手順を使用して、これを Azure に発行できます。

## <a name="publish-the-function-app-to-azure"></a>関数アプリを Azure に発行する

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>関数の発行を確認する

1. [Azure portal](https://portal.azure.com/) でご自分の資格情報を使用してサインインします。
2. ウィンドウの上部にある検索バーで、目的の **関数アプリの名前** を検索します。

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure portal で関数アプリをその名前で検索する。" lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. 表示された *[関数アプリ]* ページの左側のメニュー オプションで *[関数]* を選択します。 関数が正常に発行されると、リストに関数名が表示されます。
発行された関数のリストに関数が表示されるようになるまでに、数分待つか、ページを数回更新する必要がある場合があります。

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure portal で発行された関数の表示。" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

関数アプリから Azure Digital Twins にアクセスできるようにするには、Azure Digital Twins インスタンスにアクセスできるアクセス許可を備えたシステム マネージド ID を割り当てる必要があります。 次はその設定をします。

## <a name="set-up-security-access-for-the-function-app"></a>関数アプリのセキュリティ アクセスを設定する

Azure CLI または Azure portal のいずれかを使用して、関数アプリに対するセキュリティ アクセスを設定できます。 お好みのオプションで、次の手順に従ってください。

# <a name="cli"></a>[CLI](#tab/cli)

これらのコマンドは、[Azure Cloud Shell](https://shell.azure.com) または[ローカルにインストールされた Azure CLI](/cli/azure/install-azure-cli) で実行できます。
関数アプリのシステム マネージド ID を使用して、それに Azure Digital Twins インスタンスの "_**Digital Twins データ所有者**_" ロールを付与できます。 これにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が、関数アプリに与えられます。 次に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。

### <a name="assign-access-role"></a>アクセス ロールの割り当て

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

前の例の関数スケルトンでは、Azure Digital Twins で認証できるようにするために、ベアラー トークンを渡す必要があります。 このベアラー トークンが確実に渡されるようにするには、Azure Digital Twins にアクセスするための[マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) アクセス許可を関数アプリに設定する必要があります。 これは、関数アプリごとに 1 回だけ実行する必要があります。


1. 次のコマンドを使用して、関数のシステム マネージド ID の詳細を確認します。 出力の _principalId_ フィールドを書き留めてください。

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > 結果が空の場合は、ID の詳細を表示する代わりに、次のコマンドを使用して関数の新しいシステム マネージド ID を作成します。
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > これで、出力には、次の手順で必要な _principalId_ 値を含む、ID の詳細が表示されます。 

1. _principalId_ 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの _Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)_ ロールに割り当てます。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>アプリケーション設定の構成

最後に、**環境変数** を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 環境変数の詳細については、「[*お使いの関数アプリの管理*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、Azure Digital Twins インスタンスの "*ホスト名*" の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共にホスト名を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[Azure portal](https://portal.azure.com/) で次の手順を実行します。

### <a name="assign-access-role"></a>アクセス ロールの割り当て

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

システム割り当てマネージド ID によって、コード内に資格情報を格納せずに、Azure リソースをクラウド サービス (Azure Key Vault など) に対して認証させることができます。 有効にすると、Azure ロールベースのアクセス制御を介して必要なすべてのアクセス許可を付与できます。 この種類のマネージド ID のライフサイクルは、このリソースのライフサイクルに関連付けられています。 また、各リソースは、システム割り当てマネージド ID を 1 つしか持つことができません。

1. [Azure portal](https://portal.azure.com/) で、検索バーに名前を入力して関数アプリを検索します。 結果からアプリを選択します。 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット:関数アプリの名前をポータルの検索バーで検索し、検索結果が強調表示されています。":::

1. 関数アプリのページで、左側のナビゲーション バーの _[ID]_ を選択して、関数のマネージド ID を操作します。 _[システム割り当て済み]_ ページで、 _[状態]_ が **[オン]** に設定されていることを確認します (そうなっていない場合は、この時点で設定して、変更を "*保存*" します)。

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure portal のスクリーンショット: 関数アプリの [ID] ページで、[状態] オプションが [オン] に設定されています。" lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. _[Azure ロールの割り当て]_ ボタンを選択し、 *[Azure ロールの割り当て]* ページを開きます。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure portal のスクリーンショット:Azure Functions の ID ページの [アクセス許可] の下にある [Azure ロールの割り当て] ボタンの周囲が強調表示されています。" lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    _[+ ロールの割り当ての追加 (プレビュー)]_ を選択します。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure portal のスクリーンショット: [Azure ロールの割り当て] ページで [+ ロールの割り当ての追加 (プレビュー)] の周囲が強調表示されています。" lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. _[ロールの割り当ての追加 (プレビュー)]_ ページが開かれたら、次の値を選択します。

    * **スコープ**: リソース グループ
    * **[サブスクリプション]** : Azure サブスクリプションを選択します
    * **[リソース グループ]** : ドロップダウンからリソース グループを選択します
    * **[ロール]** : ドロップダウンから _[Azure Digital Twins Data Owner]\(Azure Digital Twins データ所有者\)_ を選択します

    次に、 _[保存]_ ボタンを押して詳細を保存します。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Azure portal のスクリーンショット:新しいロールの割り当てを追加するためのダイアログ (プレビュー)。スコープ、サブスクリプション、リソース グループ、ロールのフィールドがあります。":::

### <a name="configure-application-settings"></a>アプリケーション設定の構成

関数から Azure Digital Twins インスタンスの URL にアクセスできるようにする場合は、そのための **環境変数** を設定できます。 環境変数の詳細については、「[*お使いの関数アプリの管理*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 Azure Digital Twins インスタンスにアクセスするためのアプリケーション設定は、環境変数として公開されます。 

環境変数にインスタンスの URL を設定するには、まず、Azure Digital Twins インスタンスのホスト名を検索して URL を取得します。 [Azure portal](https://portal.azure.com) の検索バーでそのインスタンス名を検索します。 次に、左側のナビゲーション バーの _[概要]_ を選択して、 _[ホスト名]_ を表示します。 この値をコピーします。

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure portal のスクリーンショット:Azure Digital Twins インスタンスの [概要] ページで、ホスト名の値が強調表示されています。":::

これで、次の手順を使用してアプリケーション設定を作成できます。

1. ポータルの検索バーで関数アプリを検索し、結果からそれを選択します。

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット:関数アプリの名前をポータルの検索バーで検索し、検索結果が強調表示されています。":::

1. 左側のナビゲーション バーで _[構成]_ を選択します。 _[アプリケーションの設定]_ タブで、 _[+ 新しいアプリケーション設定]_ を選択します。

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure portal のスクリーンショット:関数アプリの [構成] ページで、新しいアプリケーション設定を作成するためのボタンが強調表示されています。":::

1. 開いたウィンドウで、上でコピーしたホスト名の値を使用して、アプリケーション設定を作成します。
    * **[名前]** : ADT_SERVICE_URL
    * **[値]** : https://{your-azure-digital-twins-host-name}
    
    _[OK]_ を選択して、アプリケーションの設定を作成します。
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure portal のスクリーンショット:[アプリケーション設定の追加/編集] ページで &quot;名前&quot; および &quot;値&quot; フィールドに入力した後、[OK] ボタンが強調表示されています。":::

1. 設定を作成すると、先ほどの _[アプリケーションの設定]_ タブにその内容が表示されます。一覧に *ADT_SERVICE_URL* が表示されていることを確認し、 _[保存]_ ボタンを選択して新しいアプリケーション設定を保存します。

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure portal のスクリーンショット:[アプリケーション設定] ページで、新しい ADT_SERVICE_URL 設定が強調表示されています。[保存] ボタンも強調表示されている。":::

1. アプリケーション設定に対する変更を有効にするには、アプリケーションの再起動が必要です。そのため、確認のメッセージが表示されたら _[続行]_ を選択してアプリケーションを再起動します。

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure portal のスクリーンショット:アプリケーション設定を変更するとアプリケーションが再起動されるという注意書きが表示されています。[続行] ボタンが強調表示されています。":::

---

## <a name="next-steps"></a>次のステップ

この記事では、Azure Digital Twins で使用する関数アプリを Azure 設定する手順を実行しました。

次に、IoT Hub データを Azure Digital Twins に取り込む基本的な関数を構築する方法を確認します。
* [*方法: IoT Hub からテレメトリを取り込む*](how-to-ingest-iot-hub-data.md)
