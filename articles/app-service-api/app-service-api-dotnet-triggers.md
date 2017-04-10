---
title: "App Service API アプリ トリガー | Microsoft Docs"
description: "Azure App Service で API アプリにトリガーを実装する方法"
services: logic-apps
documentationcenter: .net
author: guangyang
manager: erikre
editor: jimbe
ms.assetid: 493c3703-786d-4434-9dca-8f77744b2f5d
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 015ca80c952110d3289888ed82d7a543be29950c
ms.openlocfilehash: cf020b0f5f14d73635cf44e0157b53b35eb00d60
ms.lasthandoff: 12/07/2016


---
# <a name="azure-app-service-api-app-triggers"></a>Azure App Service API アプリ トリガー
> [!NOTE]
> 本記事は、API アプリの 2014-12-01-preview スキーマ バージョンを対象としています。
>
>

## <a name="overview"></a>Overview
この記事では、API アプリ トリガーを実装して、ロジック アプリからそれらを使用する方法について説明します。

このトピック内のすべてのコード スニペットは、 [FileWatcher API アプリのサンプル コード](http://go.microsoft.com/fwlink/?LinkId=534802)からコピーしたものです。

この記事のコードをビルドして実行するには、次の nuget パッケージをダウンロードする必要がある点に注意してください: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/)。

## <a name="what-are-api-app-triggers"></a>API アプリ トリガーとは
API アプリでイベントを発生させることは一般的なシナリオなので、API アプリのクライアントはイベントに応答して適切な処理を行うことができます。 このシナリオをサポートする REST API ベースのメカニズムは API アプリ トリガーと呼ばれます。

たとえば、クライアント コードで [Twitter Connector API アプリ](../connectors/connectors-create-api-twitter.md) が使用されていて、コードが特定の単語を含む新しいツイートに基づいて処理を実行する必要があるとします。 この場合、このニーズを満たすために、ポーリング トリガーかプッシュ トリガーを設定する場合があります。

## <a name="poll-trigger-versus-push-trigger"></a>ポーリング トリガーとプッシュ トリガー
現在、2 種類のトリガーがサポートされています。

* ポーリング トリガー - 発生したイベントの通知に対して、クライアントが API アプリをポーリングします。
* プッシュ トリガー - イベントの発生時に、クライアントは API アプリから通知を受け取ります。

### <a name="poll-trigger"></a>ポーリング トリガー
ポーリング トリガーは、標準の REST API として実装され、そのクライアント (ロジック アプリなど) が通知を取得するために API をポーリングすることを想定しています。 クライアントは状態を保持できますが、ポーリング トリガー自体はステートレスです。

要求パケットと応答パケットに関する次の情報は、ポーリング トリガー コントラクトのいくつかの重要な側面を示しています。

* 要求
  * HTTP メソッド: GET
  * パラメーター
    * triggerState - このオプション パラメーターでは、指定された状態に基づいて通知が返されたかどうかをポーリング トリガーが適切に判断できるように、クライアントが自身の状態を指定できます。
    * API 固有のパラメーター
* 応答
  * 状態コード **200** - 要求は有効で、トリガーからの通知があります。 通知の内容は応答本文です。 応答内の "Retry-After" ヘッダーは、後続の要求呼び出しで追加の通知データを取得する必要があることを示しています。
  * 状態コード **202** - 要求は有効ですが、トリガーからの新しい通知はありません。
  * 状態コード **4xx** - 要求は無効です。 クライアントは要求を再試行できません。
  * 状態コード **5xx** - 要求の結果、内部サーバー エラーまたは一時的な問題が発生しました。 クライアントは要求を再試行する必要があります。

次のコード スニペットは、ポーリング トリガーを実装する方法の例を示します。

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

このポーリング トリガーをテストするには、次の手順に従います。

1. 認証設定に **public anonymous**を使用して API アプリをデプロイします。
2. **touch** 操作を呼び出し、ファイルにタッチします。 次の図は、Postman を介した要求の例を示しています。
   ![Postman を使用したタッチ操作の呼び出し](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. 手順 2. の前に **triggerState** パラメーターがタイム スタンプに設定されたポーリング トリガーを呼び出します。 次の図は、Postman を介した要求の例を示しています。
   ![Postman を使用したポーリング トリガーの呼び出し](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>プッシュ トリガー
プッシュ トリガーは、特定のイベントの発生時に通知を受け取るように登録されているクライアントに通知をプッシュする標準の REST API として実装されます。

要求パケットと応答パケットに関する次の情報は、プッシュ トリガー コントラクトのいくつかの重要な側面を示しています。

* 要求
  * HTTP メソッド: PUT
  * パラメーター
    * triggerId: 必須 - プッシュ トリガーの登録を表す不明瞭な文字列 (GUID など)。
    * callbackUrl: 必須 - イベントの発生時に呼び出されるコールバックの URL。 呼び出しは、単純な POST HTTP 呼び出しです。
    * API 固有のパラメーター
* 応答
  * 状態コード **200** - クライアントを登録するための要求が成功しました。
  * 状態コード **4xx** - 要求は無効です。 クライアントは要求を再試行できません。
  * 状態コード **5xx** - 要求の結果、内部サーバー エラーまたは一時的な問題が発生しました。 クライアントは要求を再試行する必要があります。
* コールバック
  * HTTP メソッド: POST
  * 要求本文: 通知の内容です。

次のコード スニペットは、プッシュ トリガーを実装する方法の例を示します。

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

このポーリング トリガーをテストするには、次の手順に従います。

1. 認証設定に **public anonymous**を使用して API アプリをデプロイします。
2. [http://requestb.in/](http://requestb.in/) を参照して、コールバック URL として機能する RequestBin を作成します。
3. GUID を **triggerId**、RequestBin URL を **callbackUrl** として使用してプッシュ トリガーを呼び出します。
   ![Postman を使用したプッシュ トリガーの呼び出し](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. **touch** 操作を呼び出し、ファイルにタッチします。 次の図は、Postman を介した要求の例を示しています。
   ![Postman を使用したタッチ操作の呼び出し](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. RequestBin をチェックし、プロパティの出力でプッシュ トリガーのコールバックが呼び出されることを確認します。
   ![Postman を使用したポーリング トリガーの呼び出し](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>API 定義でのトリガーの記述
トリガーを実装して API アプリを Azure にデプロイした後、Azure プレビュー ポータルの **[API の定義]** ブレードに移動すると、トリガーが自動的に UI で認識されることがわかります。これは API アプリの Swagger 2.0 API 定義によって起こっています。

![[API の定義] ブレード](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

**[Swagger のダウンロード]** をクリックして JSON ファイルを開くと、次のような結果が表示されます。

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

拡張機能プロパティ **x-ms-schedular-trigger** は、API の定義におけるトリガーの記述方法を示し、要求が次の条件のいずれかを満たした場合、ゲートウェイ経由で API の定義を要求するときに API アプリ ゲートウェイによって自動的に追加されます (このプロパティは手動で追加することもできます)。

* ポーリング トリガー
  * HTTP メソッドが **GET**かどうか
  * **operationId** プロパティに文字列 **trigger** が含まれているかどうか
  * **parameters** プロパティに、**name** プロパティが **triggerState** に設定されたパラメーターが含まれているかどうか
* プッシュ トリガー
  * HTTP メソッドが **PUT**かどうか
  * **operationId** プロパティに文字列 **trigger** が含まれているかどうか
  * **parameters** プロパティに、**name** プロパティが **triggerId** に設定されたパラメーターが含まれているかどうか

## <a name="use-api-app-triggers-in-logic-apps"></a>ロジック アプリでの API アプリ トリガーの使用
### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>ロジック アプリ デザイナーでの API アプリ トリガーの一覧表示と構成
API アプリと同じリソース グループにロジック アプリを作成すると、それをクリックするだけで、デザイナー キャンバスに追加できます。 次の図では、これを示します。

![ロジック アプリ デザイナーにおけるトリガー](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![ロジック アプリ デザイナーでのポーリング トリガーの構成](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![ロジック アプリ デザイナーでのプッシュ トリガーの構成](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>ロジック アプリ向けの API アプリ トリガーの最適化
API アプリにトリガーを追加した後は、ロジック アプリ内で API アプリを使用するときのエクスペリエンスを改善するためにできることがいくつかあります。

たとえば、ポーリング トリガーの **triggerState** パラメーターは、ロジック アプリで次の式に設定されています。 この式は、ロジック アプリからのトリガーの最後の呼び出しを評価して、その値を返します。  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

注: 前の式で使用されている関数の詳細については、 [ロジック アプリのワークフロー定義言語](https://msdn.microsoft.com/library/azure/dn948512.aspx)に関するドキュメントを参照してください。

ロジック アプリ ユーザーは、トリガーの使用時に、 **triggerState** パラメーターに上記の式を入力する必要があります。 この値は、拡張機能プロパティ **x-ms-scheduler-recommendation**を使用してロジック アプリ デザイナーによって事前設定しておくことができます。  パラメーター自体がデザイナーに表示されないように、 **x-ms-visibility** 拡張機能プロパティの値を *internal* に設定できます。  次のスニペットは、そのことを示しています。

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

プッシュ トリガーの場合、 **triggerId** パラメーターはロジック アプリを一意に識別する必要があります。 ベスト プラクティスとして、次の式を使用して、このプロパティをワークフローの名前に設定することをお勧めします。

    @workflow().name

API の定義で **x-ms-scheduler-recommendation** および **x-ms-visibility** 拡張機能プロパティを使用すると、API アプリは、そのユーザーに対してこの式を自動的に設定するようにロジック アプリ デザイナーに通知できます。

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>API の定義での拡張機能プロパティの追加
拡張機能プロパティ **x-ms-scheduler-recommendation** や **x-ms-visibility** などの追加のメタ情報は、静的または動的の 2 種類の方法のいずれかで API の定義に追加できます。

静的なメタデータの場合は、プロジェクト内の */metadata/apiDefinition.swagger.json* ファイルを直接編集して、プロパティを手動で追加できます。

動的メタデータを使用する API アプリの場合は、SwaggerConfig.cs ファイルを編集して、これらの式を追加できる操作フィルターを追加できます。

    GlobalConfiguration.Configuration
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


このクラスを実装して、動的メタデータのシナリオを容易にする方法の例を次に示します。

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }

