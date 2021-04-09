---
title: Azure Analysis Services モデルの非同期更新 | Microsoft Docs
description: Azure Analysis Services REST API を使用し、モデル データの非同期更新をコーディングする方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013901"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>REST API を使用した非同期更新

REST 呼び出しをサポートしているプログラミング言語を使用すれば、Azure Analysis Services 表形式モデルでの非同期データ更新操作を実行できます。 これには、クエリのスケールアウトのための読み取り専用レプリカの同期が含まれます。 

データ更新操作は、データ ボリュームや、パーティションを使用した最適化のレベルなどの数多くの要因によって、ある程度時間がかかる場合があります。これらの操作は、従来は [TOM](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (表形式オブジェクト モデル)、[PowerShell](/analysis-services/powershell/analysis-services-powershell-reference) コマンドレット、または [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (表形式モデル スクリプト言語) などの既存の方法を使用して呼び出されていました。 しかし、多くの場合、これらの方法は信頼性が低く実行時間が長い HTTP 接続を必要とします。

Azure Analysis Services 用の REST API なら、データ更新操作を非同期で実行できます。 REST API を使用すれば、クライアント アプリケーションからの実行時間の長い HTTP 接続は不要になります。 他にも、自動の再試行やバッチ処理されたコミットなどの信頼性を高める組み込み機能もあります。

## <a name="base-url"></a>ベース URL

ベース URL は、次の形式に従います。

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

たとえば、米国西部の Azure リージョンにある `myserver` という名前のサーバーの、AdventureWorks という名前のモデルの場合、 サーバー名は次のようになります。

```
asazure://westus.asazure.windows.net/myserver 
```

このサーバー名のベース URL は次のようになります。

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

このベース URL を使用すると、次のパラメーターに基づいたリソースと操作を追加できます。 

![非同期更新](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- 末尾が **s** ならコレクション。
- 末尾が **()** なら関数。
- その他はリソース/オブジェクト。

たとえば、次のように Refreshes コレクションに対して POST 動詞を使用すれば、更新操作を実行できます。

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>認証

すべての呼び出しは、Authorization ヘッダー内の有効な Azure Active Directory (OAuth 2) トークンで認証する必要があり、次の要件を満たす必要があります。

- そのトークンはユーザー トークンまたはアプリケーション サービス プリンシパルのどちらかである必要があります。
- そのトークンは正しい対象ユーザーを `https://*.asazure.windows.net` に設定している必要があります。
- そのユーザーまたはアプリケーションは、要求された呼び出しを実行するために、サーバーまたはモデルに対する十分なアクセス許可を与えられている必要があります。 そのアクセス許可のレベルは、モデル、またはサーバー上の管理者グループ内のロールによって決定されます。

    > [!IMPORTANT]
    > 現時点では、**サーバー管理者** ロールのアクセス許可が必要です。

## <a name="post-refreshes"></a>POST /refreshes

更新操作を実行するには、/refreshes コレクションに対して POST 動詞を使用して、新しい更新項目をコレクションに追加します。 応答内の Location ヘッダーには、更新 ID が含まれています。 このクライアント アプリケーションは非同期であるため、接続を切断して、必要があればあとで状態を確認できます。

1 つのモデルに対して一度に 1 回の更新操作のみ可能です。 現在実行中の更新操作がある場合に別の更新操作が送信されると、409 競合 HTTP 状態コードが返されます。

本文は次の文に似たものになっているはずです。

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>パラメーター

パラメーターを指定する必要はありません。 既定値が適用されます。

| 名前             | Type  | 説明  |Default  |
|------------------|-------|--------------|---------|
| `Type`           | 列挙型  | 実行する処理の種類です。 この種類は、TMSL の [refresh コマンド](/analysis-services/tmsl/refresh-command-tmsl)の種類 (full、clearValues、calculate、dataOnly、automatic、defragment) と一致します。 add 型はサポートされていません。      |   automatic      |
| `CommitMode`     | 列挙型  | オブジェクトがバッチでコミットされるかどうか、または完了する時間のみを決定します。 Mode には default、transactional、partialBatch が含まれています。  |  transactional       |
| `MaxParallelism` | int   | この値は、複数の処理コマンドを並列に実行するスレッドの最大数を決定します。 この値は、TMSL の [Sequence コマンド](/analysis-services/tmsl/sequence-command-tmsl)やその他のメソッドで設定できる MaxParallelism プロパティと一致します。       | 10        |
| `RetryCount`     | int   | 失敗前の操作の再試行回数を示します。      |     0    |
| `Objects`        | Array | 処理されるオブジェクトの配列です。 各オブジェクトには、テーブル全体を処理する時には "table" が、またはパーティションを処理する時には "table" と "partition" が含まれます。 オブジェクトが指定されていない場合は、モデル全体が更新されます。 |   モデル全体を処理      |

CommitMode は partialBatch と同じです。 これは、読み込みに何時間もかかる可能性がある大規模なデータセットを最初に読み込む時に使用されます。 1 つまたは複数のバッチのコミットに成功したあとに更新操作が失敗すると、コミットに成功したバッチはコミットされたままになります (コミットに成功したバッチはロールバックされません)。

> [!NOTE]
> 書き込み時、バッチ サイズは MaxParallelism の値になりますが、この値は変わる可能性があります。

### <a name="status-values"></a>状態の値

|ステータス値  |説明  |
|---------|---------|
|`notStarted`    |   操作はまだ開始されていません。      |
|`inProgress`     |   操作は実行中です。      |
|`timedOut`     |    ユーザー指定のタイムアウトに基づいて、操作がタイムアウトしました。     |
|`cancelled`     |   操作はユーザーまたはシステムによって取り消されました。      |
|`failed`     |   操作に失敗しました。      |
|`succeeded`      |   操作に成功しました。      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

更新操作の状態を確認するには、更新 ID に対して GET 動詞を使用します。 応答の本文の例を次に示します。 操作が進行中の場合、状態として `inProgress` が返されます。

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

モデルの更新操作の履歴リストを取得するには、/refreshes コレクションに対して GET 動詞を使用します。 応答の本文の例を次に示します。 

> [!NOTE]
> 書き込み時、直近の 30 日間の更新操作が保存され返されますが、この日数は変わる可能性があります。

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

実行中の更新操作をキャンセルするには、更新 ID に対して DELETE 動詞を使用します。

## <a name="post-sync"></a>POST /sync

更新操作を実行したあと、その新しいデータをクエリのスケールアウトのためにレプリカと同期する必要がある場合があります。モデルの同期操作を実行するには、/sync 関数に対して POST 動詞を使用します。 応答内の Location ヘッダーには、同期操作 ID が含まれています。

## <a name="get-sync-status"></a>GET /sync status

同期操作の状態を確認するには、パラメーターとして操作 ID を渡す GET 動詞を使用します。 応答の本文の例を次に示します。

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

`syncstate` の値:

- 0:レプリケーション中。 データベース ファイルはターゲット フォルダーにレプリケートされています。
- 1:リハイドレート中。 データベースは読み取り専用のサーバー インスタンスにリハイドレートされています。
- 2:完了。 同期操作は正常に完了しました。
- 3:失敗。 同期操作は失敗しました。
- 4:終了処理中。 同期操作は完了しましたが、クリーンアップ手順を実行中です。

## <a name="code-sample"></a>コード サンプル

開始するための C# コードのサンプルは [GitHub の RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample) にあります。

### <a name="to-use-the-code-sample"></a>サンプル コードを使用するには

1.    リポジトリを複製またはダウンロードします。 RestApiSample ソリューションを開きます。
2.    「**client.BaseAddress = …** 」の行を探し 、ご利用の[ベース URL](#base-url) を指定します。

このコード例では、[サービス プリンシパル](#service-principal)の認証を使用しています。

### <a name="service-principal"></a>サービス プリンシパル

Azure AS でサービス プリンシパルを設定し、必要なアクセス許可を割り当てる方法については、[Azure Portal でサービス プリンシパルを作成する方法](../active-directory/develop/howto-create-service-principal-portal.md)に関するページと「[サーバー管理者ロールへのサービス プリンシパルの追加](analysis-services-addservprinc-admins.md)」を参照してください。 これらの手順を完了したら、追加で次の手順を実行します。

1.    コード サンプル内で "**string authority = …** " を見つけたら、"**common**" を自分の組織のテナント ID に置き換えます。
2.    コメント化およびコメントの解除を行って、ClientCredential クラスで cred オブジェクトをインスタンス化します。 \<App ID> と \<App Key> の値が安全な方法でアクセスされていることを確認します。または、サービス プリンシパルの証明書ベース認証を使用します。
3.    サンプルを実行します。


## <a name="see-also"></a>関連項目

[サンプル](analysis-services-samples.md)   
[REST API](/rest/api/analysisservices/servers)