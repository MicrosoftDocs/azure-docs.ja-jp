---
title: "Log Analytics のログ検索 REST API | Microsoft Docs"
description: "このガイドには、Operations Management Suite (OMS) で Log Analytics 検索 REST API を使用する方法に関する基本的な説明と、コマンドの使用方法の例が記載されています。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: b12f823d723b013755fc868b883faefa2072eb75
ms.openlocfilehash: 9b21fed003f96dbf7ebd72d6f46fff91acbf039e


---
# <a name="log-analytics-log-search-rest-api"></a>Log Analytics のログ検索 REST API
このガイドでは、Log Analytics Search REST API を使用する方法の基本的なチュートリアルについて、例を示しながら説明します。 Log Analytics は Operations Management Suite (OMS) に付属しています。

> [!NOTE]
> Log Analytics は以前、オペレーション インサイトと呼ばれていました。そのため、リソース プロバイダーにはその旧称が使用されています。
>
>

## <a name="overview-of-the-log-search-rest-api"></a>ログ検索 REST API の概要
Log Analytics の検索 REST API は RESTful であり、Azure Resource Manager API を使用してアクセスできます。 この記事では、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンド ライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を通じて API にアクセスする例を示します。 Log Analytics Search API には、ARMClient を使用する以外にもさまざまな方法でアクセスできます。 もう 1 つの方法は、Operational Insights 用の Azure PowerShell モジュールを使う方法です。これには検索にアクセスするためのコマンドレットが含まれています。 これらのツールを使用すると、Azure Resource Manager API を使用して OMS のワークスペースにアクセスし、その中で検索コマンドを実行できます。 API の検索結果は JSON 形式で出力されるため、検索結果をプログラムによりさまざまな方法で使用できます。

Azure Resource Manager は [.NET のライブラリ](https://msdn.microsoft.com/library/azure/dn910477.aspx)や [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx) 経由で使用できます。 詳細はリンク先の Web ページで確認してください。

> [!NOTE]
> `|measure count()` や `distinct` などの集計コマンドを使用する場合は、検索の呼び出しごとに最大で 500,000 レコードを返すことができます。 集計コマンドを含まない検索の場合は、最大 5,000 レコードが返されます。
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Log Analytics 検索 REST API の基本的なチュートリアル
### <a name="to-use-armclient"></a>ARMClient を使用するには
1. [Chocolatey](https://chocolatey.org/) をインストールします。これは、オープン ソースの Windows 用パッケージ マネージャーです。 管理者としてコマンド プロンプト ウィンドウを開き、次のコマンドを実行します。

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. 次のコマンドを実行して ARMClient エージェントをインストールします。

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>ARMClient を使用して検索を実行するには
1. Microsoft アカウントを使用するか、職場または学校アカウントを使用してログインします。

    ```
    armclient login
    ```

    ログインに成功すると、指定のアカウントに関連付けられているすべてのサブスクリプションが一覧表示されます。

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Operations Management Suite のワークスペースを取得します。

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    取得の呼び出しが成功すると、サブスクリプションに関連付けられているすべてのワークスペースが出力されます。

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 検索変数を作成します。

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 新しい検索変数を使用して検索します。

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Log Analytics 検索 REST API リファレンスの例
次の例は、検索 API の使用方法を示します。

### <a name="search---actionread"></a>検索 - アクション/読み取り
**サンプル URL:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**要求:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
次のテーブルは、使用可能なプロパティについて説明しています。

| **プロパティ** | **説明** |
| --- | --- |
| top |返される結果の最大数。 |
| highlight |pre パラメーターと post パラメーターが含まれ、一般に一致するフィールドを強調表示するために使用します。 |
| pre |一致するフィールドに指定した文字列をプレフィックスします。 |
| post |一致するフィールドに指定した文字列を追加します。 |
| query |結果を収集して返すのに使用する検索クエリ。 |
| start |結果を検索する時間枠の最初。 |
| end |結果を検索する時間枠の最後。 |

**応答:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>検索/{ID} - アクション/読み取り
**保存された検索の内容を要求:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> 検索によって「Pending」状態が返される場合、この API を使用して更新後の結果をポーリングできます。 検索の結果は 6 分後にキャッシュから削除され、HTTP Gone が返されます。 最初の検索要求によって "Successful" 状態がすぐに返される場合、結果はキャッシュに追加されません。そのため、クエリを実行すると、この API は HTTP Gone を返します。 HTTP 200 の結果の内容は最初の検索要求と同じ形式で、値のみが更新されます。
>
>

### <a name="saved-searches"></a>保存した検索条件
**保存された検索の一覧を要求:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

サポートされるメソッド: GET PUT DELETE

サポートされるコレクション メソッド: GET

次のテーブルは、使用可能なプロパティについて説明しています。

| プロパティ | 説明 |
| --- | --- |
| ID |一意の識別子。 |
| ETag |**PATCH の場合は必須**。 書き込みごとにサーバーによって更新されます。 更新するには、値を、現在格納されている値と等しくするか、"*"' にする必要があります。 古い値や無効な値の場合は、409 が返されます。 |
| properties.query |**必須**。 検索クエリ。 |
| properties.displayName |**必須**。 ユーザー定義のクエリの表示名。 |
| properties.category |**必須**。 ユーザー定義のクエリのカテゴリ。 |

> [!NOTE]
> Log Analytics 検索 API では現在、保存された検索をワークスペースでポーリングすると、ユーザー作成の保存された検索が返されます。 ソリューションで提供されている保存された検索は返されません。
>
>

### <a name="create-saved-searches"></a>保存された検索の作成
**要求:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>保存された検索の削除
**要求:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>保存された検索の更新
 **要求:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>メタデータ - JSON のみ
ここでは、ワークスペースで収集したデータのすべてのログの種類に対応したフィールドを表示する方法を示します。 たとえば、イベントの種類に "Computer" という名前のフィールドがあるかどうかを知りたい場合は、このクエリを使用して確認することもできます。

**フィールドの要求:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**応答:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

次のテーブルは、使用可能なプロパティについて説明しています。

| **プロパティ** | **説明** |
| --- | --- |
| name |フィールド名。 |
| displayName |フィールドの表示名。 |
| type |フィールド値の型。 |
| facetable |現在の "Indexed"、"stored"、"facet" の各プロパティの組み合わせ。 |
| display |現在の "display" プロパティ。 フィールドが検索で表示される場合は true。 |
| ownerType |オンボードされた IP アドレスに属している型のみに限定されます。 |

## <a name="optional-parameters"></a>省略可能なパラメーター
以下では、使用可能なオプションのパラメーターについて説明します。

### <a name="highlighting"></a>強調表示
“highlight” パラメーターを使用して、マーカーのセットを応答に含めるよう検索サブシステムに要求できます。

これらのマーカーは、検索クエリで指定した語句に一致して強調表示されるテキストの開始と終了を示します。
強調表示される語句を囲むために検索で使用される開始マーカーと終了マーカーを指定できます。

**検索クエリの例**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**サンプル結果:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

前の結果にはプレフィックスおよび追加されたエラー メッセージが含まれています。

## <a name="computer-groups"></a>コンピューター グループ
コンピューター グループは特別に保存された検索で、一連のコンピューターを返します。  その他のクエリでコンピューター グループを使用すると、グループ内のコンピューターに結果を制限できます。  コンピューター グループは、コンピューターの値を持つ Group タグが付いた保存済み検索として実装されます。

コンピューター グループの応答の例を次に示します。

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>コンピューター グループの取得
コンピューター グループを取得するには、グループ ID と共に Get メソッドを使用します。

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>コンピューター グループの作成または更新
コンピューター グループを作成するには、一意に保存された検索 ID と共に Put メソッドを使用します。 既存のコンピューター グループ ID を使用すると、その ID が変更されます。 Log Analytics ポータルでコンピューター グループを作成すると、グループと名前から ID が作成されます。

グループ定義に使用されるクエリは、正常に機能するグループの一連のコンピューターを返す必要があります。  正しいデータが返されるようにするために、クエリの末尾に `| Distinct Computer` を付けることをお勧めします。

保存した検索条件の定義には、検索がコンピューター グループとして分類されるように、コンピューターの値を持つ Group という名前のタグを含める必要があります。

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>コンピューター グループの削除
コンピューター グループを削除するには、グループ ID と共に Delete メソッドを使用します。

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>次のステップ
* 基準のカスタム フィールドを使用してクエリを作成するための、 [ログ検索](log-analytics-log-searches.md) について説明します。



<!--HONumber=Dec16_HO1-->


