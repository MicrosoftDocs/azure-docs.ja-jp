---
title: データを処理するための Azure Functions を設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる Azure Functions を作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930164"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>データを処理するために Azure Functions アプリを接続する

プレビュー段階では、データに基づくデジタル ツインの更新は、[Azure Functions](../azure-functions/functions-overview.md) などのコンピューティング リソースを介し、[**イベント ルート**](concepts-route-events.md)を使用して処理されます。 Azure Functions を使用すると、以下に応じてデジタル ツインを更新することができます。
* IoT Hub からのデバイス テレメトリ データ
* ツイン グラフ内の別のデジタル ツインから取得されたプロパティ変更または他のデータ

この記事では、Azure Digital Twins で使用する Azure Functions の作成について順を追って説明します。 

説明する手順の概要は次のとおりです。

1. Visual Studio で Azure Functions アプリを作成する
2. [Event Grid](../event-grid/overview.md) トリガーを使用して Azure Functions を作成する
3. 認証コードを関数に追加する (Azure Digital Twins にアクセスできるようにするため)
4. 関数アプリを Azure に発行する
5. [セキュリティ](concepts-security.md) アクセスを設定する。 Azure Functions で実行時にアクセス トークンを受信してサービスにアクセスできるようにするには、関数アプリのマネージド サービス ID を構成する必要があります。

この記事の残りの部分では、Azure Functions の設定手順を 1 つずつ順を追って説明します。

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Visual Studio で Azure Functions アプリを作成する

Visual Studio 2019 で、 *[ファイル] > [新規作成] > [プロジェクト]* を選択します。 *Azure Functions* テンプレートを検索して選択し、[次へ] を選択します。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: 新しいプロジェクト ダイアログ":::

関数アプリの名前を指定して、[作成] を選択します。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: プロジェクトの構成ダイアログ":::

*[イベント グリッド トリガー]* を選択し、[作成] を選択します。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: Azure Functions のプロジェクトのトリガー ダイアログ":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Event Grid トリガーを使用して Azure Functions を作成する

次のコードでは、イベントのログ記録に使用できる簡単な Azure Functions を作成します。 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

これは基本的な Azure Functions です。

### <a name="run-and-debug-the-azure-function-app"></a>Azure Functions アプリを実行してデバッグする

これで、関数をコンパイルして実行できるようになりました。 Azure Functions は最終的にクラウドで実行することを目的としていますが、ローカルで Azure Functions を実行およびデバッグすることもできます。

この詳細については、「[*イベント グリッド トリガーをローカルにデバッグ*](../azure-functions/functions-debug-event-grid-trigger-local.md)」を参照してください。

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Azure Digital Twins SDK を Azure Functions アプリに追加する

関数アプリと Azure Digital Twins のやり取りには、[.NET (C#) 用の Azure IoT Digital Twin クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)を使用します。 この SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。
* `Azure.DigitalTwins.Core` (バージョン `1.0.0-preview.2`)
* `Azure.Identity`

Azure Functions を適切に設定するように Azure SDK パイプラインを構成するには、以下も必要です。
* `Azure.Net.Http`
* `Azure.Core`

選択するツールによっては、Visual Studio パッケージ マネージャーまたは `dotnet` コマンド ライン ツールを使用して行うことができます。 

次の using ステートメントを Azure Functions に追加します。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Azure Functions に認証コードを追加する

次に、関数から Azure Digital Twins にアクセスできるようにする認証コードを追加します。

次のような値の変数を関数クラスに追加します。 
* Azure Digital Twins アプリ ID
* Azure Digital Twins インスタンスの URL。 関数内にハードコーディングするのではなく、環境変数からサービス URL を読み取ることをお勧めします。
* HttpClient インスタンスを保持する静的変数。 HttpClient は作成に比較的コストがかかるため、すべての関数呼び出しでこれを実行する必要がないようにします。

また、関数の内部にローカル変数を追加して、Azure Digital Twins クライアント インスタンスを関数プロジェクトに保持します。 これをクラス内の静的変数に "*しないでください*"。

最後に、関数シグネチャを非同期に変更します。

このような変更を加えると、関数コードは次のようになります。

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Functions アプリから Azure Digital Twins にアクセスできるようにするには、システムマネージド ID を割り当て、Azure Digital Twins インスタンスにアクセスできるアクセス許可を付与する必要があります。

次のコマンドを使用して、システムによって管理される ID を作成します。 出力の *principalId* フィールドを書き留めてください。

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

*principalId* 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの "*Azure Digital Twins 所有者 (プレビュー)* " ロールに割り当てます。 これにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が、関数アプリに与えられます。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

マネージド ID の詳細については、「[*App Service と Azure Functions でマネージド ID を使用する方法*](../app-service/overview-managed-identity.md)」を参照してください。

最後に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 詳細については、「[*環境変数*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables)」を参照してください。

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、Azure Digital Twins インスタンスの *hostName* の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共に hostName を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Azure Functions アプリを発行する

関数アプリを Azure に発行するには、ソリューション エクスプローラーで (ソリューションではなく) 関数プロジェクトを右クリックし、 *[発行]* を選択します。

次のタブが表示されます。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: 関数の発行ダイアログ、ページ 1":::

Azure Functions で使用する App Service プランを選択または作成します。 不明な場合は、既定の従量課金プランを使用して開始します。

> [!IMPORTANT] 
> Azure Functions を発行すると、Azure Digital Twins とは関係なく、サブスクリプションに追加料金が発生します。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: 関数の発行ダイアログ、ページ 2":::

次のページで、新しい関数アプリの適切な名前、リソース グループ、その他の詳細を入力します。

## <a name="set-up-security-access-for-the-azure-function-app"></a>Azure Functions アプリのセキュリティ アクセスを設定する

前の例の Azure Functions スケルトンでは、Azure Digital Twins で認証できるようにするために、ベアラー トークンを渡す必要があります。 このベアラー トークンが確実に渡されるようにするには、関数アプリの [マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) を設定する必要があります。 これは、関数アプリごとに 1 回だけ実行する必要があります。

これを設定するには、[Azure portal](https://portal.azure.com/) に移動し、関数アプリに移動します。

*[プラットフォーム機能]* タブで、 *[ID]* を選択します。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure portal: Azure Functions の ID の選択":::

ID ページで、 *[状態]* トグルを *[オン]* に設定します。 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure portal: ID の状態をオンにする":::

また、次のセクションで使用するため、このページに表示される**オブジェクト ID** をメモします。

### <a name="assign-access-roles"></a>アクセス ロールを割り当てる

Azure Digital Twins では、ロールベースのアクセス制御を使用してアクセスを管理するため (詳細については「[*概念: Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)」を参照してください)、各関数アプリ用に Azure Digital Twins へのアクセスを許可するロールも追加する必要があります。

ロールを割り当てるには、作成した Azure Digital Twins インスタンスの**リソース ID** が必要です。 インスタンスを作成したときにメモしなかった場合は、次のコマンドを使用して取得できます。

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
リソース ID は、"/subscriptions/…" で始まる "id" という名前の長い文字列として出力に含まれます。

次のコマンドで、リソース ID と、前の Azure Functions のオブジェクト ID を使用します。

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Digital Twins で使用する Azure Functions を設定する手順を実行しました。 次に、Azure Functions を Event Grid にサブスクライブして、エンドポイントでリッスンできるようにします。 このエンドポイントは次のようになります。
* Azure Digital Twins 自体から送信されるメッセージ (プロパティ変更メッセージ、ツイン グラフの[デジタル ツイン](concepts-twins-graph.md)によって生成されるテレメトリ メッセージ、ライフサイクル メッセージなど) を処理するために、Azure Digital Twins にアタッチされた Event Grid エンドポイント
* テレメトリやその他のデバイス イベントを送信するために IoT Hub が使用する IoT システム トピック
* 他のサービスからメッセージを受信する Event Grid エンドポイント

次に、IoT Hub データを Azure Digital Twins に取り込む基本的な Azure Functions を構築する方法を確認します。
* [*方法: IoT Hub からテレメトリを取り込む*](how-to-ingest-iot-hub-data.md)
