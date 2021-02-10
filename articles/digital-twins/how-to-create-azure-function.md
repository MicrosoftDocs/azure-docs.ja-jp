---
title: データを処理するための関数を Azure で設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる関数を Azure で作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1178b0ab5af3642026fe78c7de788f354691b13a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701164"
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

関数アプリに SDK を追加することによって、関数を作成できます。 関数アプリと Azure Digital Twins のやり取りには、[.NET (C#) 用の Azure Digital Twin SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用します。 

SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。 Visual Studio の NuGet パッケージ マネージャーを使用してパッケージをインストールするか、コマンドライン ツールで `dotnet` を使用してパッケージを追加することができます。 お好みの方法で、次の手順に従ってください。

**オプション 1: Visual Studio パッケージ マネージャーを使用してパッケージを追加する:**
    
プロジェクトを右クリックし、一覧から _[NuGet パッケージの管理]_ を選択します。 次に、開いたウィンドウで _[参照]_ タブを選択し、次のパッケージを検索します。 _[インストール]_ を選択し、使用許諾契約に _[同意]_ してパッケージをインストールします。

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**オプション 2: `dotnet` コマンドライン ツールを使用してパッケージを追加する:**

または、コマンド ライン ツールで次の `dotnet add` コマンドを使用することもできます。

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

次に、Visual Studio のソリューション エクスプローラーで、サンプル コードが含まれる _Function1.cs_ ファイルを開きます。その関数に以下の `using` ステートメントを追加します。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>関数に認証コードを追加する

次に、クラス レベルの変数を宣言し、関数から Azure Digital Twins にアクセスできるようにする認証コードを追加します。 次のものを _Function1.cs_ ファイル内の関数に追加します。

* Azure Digital Twins サービスの URL を環境変数として読み取るコード。 関数内にハードコーディングするのではなく、環境変数からサービス URL を読み取ることをお勧めします。

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

## <a name="set-up-security-access-for-the-function-app"></a>関数アプリのセキュリティ アクセスを設定する

Azure CLI または Azure portal のいずれかを使用して、関数アプリに対するセキュリティ アクセスを設定できます。 お好みのオプションで、次の手順に従ってください。

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>オプション 1: CLI を使用して関数アプリのセキュリティ アクセスを設定する

前の例の関数スケルトンでは、Azure Digital Twins で認証できるようにするために、ベアラー トークンを渡す必要があります。 このベアラー トークンが確実に渡されるようにするには、関数アプリの [マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) を設定する必要があります。 これは、関数アプリごとに 1 回だけ実行する必要があります。

システム マネージド ID を作成し、関数アプリの ID を Azure Digital Twins インスタンスの _**Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)**_ ロールに割り当てることができます。 これにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が、関数アプリに与えられます。 次に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

コマンドを実行するには、[Azure Cloud Shell](https://shell.azure.com) を使用します。

次のコマンドを使用して、システムによって管理される ID を作成します。 出力の _principalId_ フィールドを書き留めてください。

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
_principalId_ 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの _Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)_ ロールに割り当てます。

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
最後に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 環境変数の設定の詳細については、「[*環境変数*](/sandbox/functions-recipes/environment-variables)」を参照してください。 

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、Azure Digital Twins インスタンスの *hostName* の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共に hostName を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>オプション 2:Azure portal を使用して関数アプリのセキュリティ アクセスを設定する

システム割り当てマネージド ID によって、コード内に資格情報を格納せずに、Azure リソースをクラウド サービス (Azure Key Vault など) に対して認証させることができます。 有効にすると、Azure ロールベースのアクセス制御を介して必要なすべてのアクセス許可を付与できます。 この種類のマネージド ID のライフサイクルは、このリソースのライフサイクルに関連付けられています。 また、各リソース (仮想マシンなど) は、システム割り当てマネージド ID を 1 つしか持つことができません。

[Azure portal](https://portal.azure.com/) で、前に作成した関数アプリの名前を使用して、検索バーで "_関数アプリ_" を検索します。 一覧から "*関数アプリ*" を選択します。 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット:関数アプリの名前をポータルの検索バーで検索し、検索結果が強調表示されています。":::

関数アプリ ウィンドウの左側のナビゲーション バーで _[ID]_ を選択して、マネージド ID を有効にします。
_[システム割り当て済み]_ タブで、 _[状態]_ を [オン] に切り替えて、"_保存_" します。 _[システム割り当てマネージド ID を有効化する]_ というポップアップが表示されます。
_[はい]_ ボタンを選択します。 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure portal のスクリーンショット:関数アプリの ID ページで、システム割り当てマネージド ID を有効にするオプションが [はい] に設定されています。[状態] オプションが [オン] に設定されています。":::

関数が Azure Active Directory に正常に登録されたことを通知で確認できます。

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure portal のスクリーンショット: ポータルの上部バーにあるベルのアイコンが選択されて、通知の一覧が表示されています。ユーザーがシステム割り当てマネージド ID を有効にしたことを示す通知があります。":::

また、次のセクションで使用するので、 _[ID]_ ページに表示される **オブジェクト ID** を記録しておきます。

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Azure portal のスクリーンショット:Azure Functions の ID ページの &quot;オブジェクト ID&quot; フィールドの周囲が強調表示されています。":::

### <a name="assign-access-roles-using-azure-portal"></a>Azure portal を使用してアクセス ロールを割り当てる

_[Azure ロールの割り当て]_ ボタンを選択し、 *[Azure ロールの割り当て]* ページを開きます。 次に、 _[+ ロールの割り当ての追加 (プレビュー)]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure portal のスクリーンショット:Azure Functions の ID ページの [アクセス許可] の下にある [Azure ロールの割り当て] ボタンの周囲が強調表示されています。":::

_[ロールの割り当ての追加 (プレビュー)]_ ページが開かれたら、以下を選択します。

* _スコープ_: リソース グループ
* _[サブスクリプション]_ : Azure サブスクリプションを選択します
* _[リソース グループ]_ : ドロップダウンからリソース グループを選択します
* _[ロール]_ : ドロップダウンから _[Azure Digital Twins Data Owner]\(Azure Digital Twins データ所有者\)_ を選択します

次に、 _[保存]_ ボタンを押して詳細を保存します。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure portal のスクリーンショット:新しいロールの割り当てを追加するためのダイアログ (プレビュー)。スコープ、サブスクリプション、リソース グループ、ロールのフィールドがあります。":::

### <a name="configure-application-settings-using-azure-portal"></a>Azure portal を使用してアプリケーションの設定を構成する

環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 詳細については、「[*環境変数*](/sandbox/functions-recipes/environment-variables)」を参照してください。 Digital Twins インスタンスにアクセスするためのアプリケーション設定は、環境変数として公開されます。 

環境変数にインスタンスの URL を設定するには、まず、Azure Digital Twins インスタンスのホスト名を検索して URL を取得します。 [Azure portal](https://portal.azure.com) の検索バーでそのインスタンス名を検索します。 次に、左側のナビゲーション バーの _[概要]_ を選択して、 _[ホスト名]_ を表示します。 この値をコピーします。

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure portal のスクリーンショット:Azure Digital Twins インスタンスの [概要] ページで、ホスト名の値が強調表示されています。":::

これで、次の手順に従って、アプリケーションの設定を作成できるようになりました。

1. ポータルの検索バーで関数アプリを検索し、結果からそれを選択します
1. 左側のナビゲーション バーで _[構成]_ を選択して、新しいアプリケーション設定を作成します
1. _[アプリケーションの設定]_ タブで、 _[+ 新しいアプリケーション設定]_ を選択します

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット:関数アプリの名前をポータルの検索バーで検索し、検索結果が強調表示されています。":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure portal のスクリーンショット:関数アプリの [構成] ページで、新しいアプリケーション設定を作成するためのボタンが強調表示されています。":::

開いたウィンドウで、上でコピーしたホスト名の値を使用して、アプリケーション設定を作成します。
* **[名前]** : ADT_SERVICE_URL
* **[値]** : https://{your-azure-digital-twins-host-name}

_[OK]_ を選択して、アプリケーションの設定を作成します。

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure portal のスクリーンショット:[アプリケーション設定の追加/編集] ページで &quot;名前&quot; および &quot;値&quot; フィールドに入力した後、[OK] ボタンが強調表示されています。":::

_[名前]_ フィールドでアプリケーション名を使用して、アプリケーションの設定を確認できます。 その後、 _[保存]_ ボタンを選択して、アプリケーション設定を保存します。

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure portal のスクリーンショット:[アプリケーション設定] ページで、新しい ADT_SERVICE_URL 設定が強調表示されています。[保存] ボタンも強調表示されている。":::

アプリケーション設定への変更を有効にするには、アプリケーションを再起動する必要があります。 _[続行]_ を選択して、アプリケーションを再起動します。

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure portal のスクリーンショット:アプリケーション設定を変更するとアプリケーションが再起動されるという注意書きが表示されています。[続行] ボタンが強調表示されています。":::

_[通知]_ アイコンを選択することで、アプリケーションの設定が更新されたことを確認できます。 アプリケーションの設定が作成されていない場合は、上記のプロセスに従って、アプリケーションの設定の追加を再度試みることができます。

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure portal のスクリーンショット: ポータルの上部バーにあるベルのアイコンが選択されて、通知の一覧が表示されています。Web アプリの設定が正常に更新されたことを示す通知があります。":::

## <a name="next-steps"></a>次のステップ

この記事では、Azure Digital Twins で使用する関数アプリを Azure 設定する手順を実行しました。

次に、IoT Hub データを Azure Digital Twins に取り込む基本的な関数を構築する方法を確認します。
* [*方法: IoT Hub からテレメトリを取り込む*](how-to-ingest-iot-hub-data.md)
