---
title: Azure Cosmos DB の Mongo DB 用 API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の MongoDB 用 API で発生する一般的な問題のトラブルシューティング方法について説明します。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258186"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

次の記事では、Azure Cosmos DB の MongoDB 用 API を使用するデプロイの一般的なエラーと解決策について説明します。

>[!Note]
> Azure Cosmos DB では、MongoDB エンジンをホストしません。 これは、MongoDB ワイヤ プロトコルの実装を提供します。 そのため、これらのエラーの中には、Azure Cosmos DB の MongoDB 用 API でしか発生しないものもあります。 

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

| コード       | エラー                | 説明  | 解決策  |
|------------|----------------------|--------------|-----------|
| 2 | 指定した order by 項目に対応するインデックス パスが除外される。または、order by クエリに、提供元にすることができる対応する複合インデックスがない。 | クエリは、インデックスが設定されていないフィールドに対して並べ替えを要求します。 | 実行しようとしている並べ替えクエリの一致するインデックス (または複合インデックス) を作成します。 |
| 13 | 権限がありません | 完了するためのアクセス許可が要求にありません。 | データベースとコレクションに対して適切なアクセス許可を設定していることを確認してください。  |
| 16 | InvalidLength | 指定した要求の長さが無効です。 | explain() 関数を使用する場合は、1 つの操作のみを指定するようにしてください。 |
| 26 | NamespaceNotFound | クエリで参照されているデータベースまたはコレクションが見つかりません。 | データベースまたはコレクションの名前が、クエリ内の名前と正確に一致していることを確認してください。|
| 50 | ExceededTimeLimit | 要求が実行のタイムアウトである 60 秒を超えました。 |  このエラーには多くの原因が考えられます。 原因の 1 つは、現在割り当てられている要求ユニットの容量が、要求を完了するのに十分ではないことです。 これは、そのコレクションまたはデータベースの要求ユニットを増やすことによって解決できます。 それ以外の場合は、大きな要求を小さい要求に分割することで、このエラーを回避できます。 このエラーが発生した書き込み操作を再試行すると、重複した書き込みが行われる可能性があります。 <br><br>RU に影響を与えることなく大量のデータを削除しようとしている場合は、次のようにします。 <br>\- TTL (タイムスタンプに基づく) の使用を検討してください: [Azure Cosmos DB の MongoDB 用 API でデータを期限切れにする](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>\- カーソルまたはバッチ サイズを使用して削除を実行します。 1 つずつドキュメントをフェッチし、順番に削除します。 こうすることで実稼働アプリケーションに影響を与えずに、ゆっくりとデータを削除することができます。|
| 61 | ShardKeyNotFound | 要求内のドキュメントに、コレクションのシャード キー (Azure Cosmos DB パーティション キー) が含まれていませんでした。 | コレクションのシャード キーが要求で使用されていることを確認してください。|
| 66 | ImmutableField | 要求によって、変更不可のフィールドの変更が試行されています。 | "id" フィールドは変更できません。 要求によってそのフィールドの更新が試行されていないことを確認してください。 |
| 67 | CannotCreateIndex | インデックスを作成する要求を完了できません。 | 1 つのコンテナー内に最大 500 の単一フィールド インデックスを作成できます。 複合インデックスには最大 8 つのフィールドを含めることができます (複合インデックスはバージョン 3.6 以降でサポートされます)。 |
| 115 | CommandNotSupported | 試行された要求はサポートされていません。 | 追加の詳細情報はエラーに記載されています。 使用されているデプロイでこの機能が重要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) でサポート チケットを作成してお問い合わせください。 |
| 11000 | DuplicateKey | 挿入しようとしているドキュメントのシャード キー (Azure Cosmos DB パーティション キー) が既にコレクションに存在するか、一意なインデックス フィールド制約に違反しています。 | update() 関数を使用して、既存のドキュメントを更新してください。 一意なインデックス フィールド制約に違反している場合は、シャードまたはパーティションにまだ存在しないフィールド値を使用してドキュメントを挿入または更新してください。 |
| 16500 | TooManyRequests  | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済みの要求ユニット レートを超えたために調整されました。 | Azure portal からコンテナーまたはコンテナーのセットに割り当てられているスループットをスケーリングすることを検討してください。または、操作を再試行できます。 [SSR を有効にした](prevent-rate-limiting-errors.md)場合 (サーバー側の再試行)、Azure Cosmos DB はこのエラーで失敗する要求を自動的に再試行します。 |
| 16501 | ExceededMemoryLimit | マルチテナント サービスとしての操作が、クライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 例: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 認識されないパイプライン ステージ名。 | 集計パイプライン要求のステージ名が認識されませんでした。 | 要求ですべての集計パイプライン名が有効であることを確認してください。 |
| - | MongoDB ワイヤ バージョンの問題 | 古いバージョンの MongoDB ドライバーは、Azure Cosmos アカウントの名前を接続文字列から検出できません。 | Cosmos DB の MongoDB 用 API の接続文字列用の末尾に *appName=@**accountName**@* を追加します。ここで、**accountName** は Cosmos DB アカウント名です。 |
| - | MongoDB クライアント ネットワークの問題 (ソケット、endOfStream の例外など)| ネットワーク要求が失敗しました。 これは、多くの場合、MongoDB クライアントが使用しようとしている非アクティブな TCP 接続が原因で発生します。 多くの場合、MongoDB ドライバーは接続プールを使用します。その結果、要求に使用されるプールからランダムな接続が選択されます。 非アクティブな接続は、通常、4 分後に Azure Cosmos DB 側でタイムアウトになります。 | アプリケーション コードでこれらの失敗した要求を再試行するか、MongoDB クライアント (ドライバー) 設定を変更して、4 分間のタイムアウト期間の前に非アクティブな TCP 接続を破棄するか、または OS のキープアライブ設定を構成して、TCP 接続をアクティブ状態のまま維持することができます。<br><br>接続メッセージを回避するには、接続文字列を変更して、maxConnectionIdleTime を 1 から 2 分に設定することもできます。<br>- Mongo ドライバー: _maxIdleTimeMS=120000* を構成します <br>- Node.JS: *socketTimeoutMS=120000*、*autoReconnect* = true、*keepAlive* = true、*keepAliveInitialDelay* = 3 分を構成します

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
