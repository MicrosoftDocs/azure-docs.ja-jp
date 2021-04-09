---
title: Azure Cosmos DB の Mongo DB 用 API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の MongoDB 用 API で発生する一般的な問題のトラブルシューティング方法について説明します。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692235"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

次の記事では、Azure Cosmos DB の MongoDB 用 API を使用するデプロイの一般的なエラーと解決策について説明します。

>[!Note]
> Azure Cosmos DB では、MongoDB エンジンをホストしません。 MongoDB [ワイヤ プロトコル バージョン 4.0](mongodb-feature-support-40.md)、[3.6](mongodb-feature-support-36.md) の実装と、[ワイヤ プロトコル バージョン 3.2](mongodb-feature-support.md) 用のレガシ サポートを提供します。 そのため、これらのエラーの中には、Azure Cosmos DB の MongoDB 用 API でしか発生しないものもあります。

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

| コード       | エラー                | 説明  | 解決策  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | 一般的な原因の 1 つは、指定した order by 項目に対応するインデックス パスが除外されること、または order by クエリに、提供元にすることができる対応する複合インデックスがないことです。 クエリは、インデックスが設定されていないフィールドに対して並べ替えを要求します。 | 実行しようとしている並べ替えクエリの一致するインデックス (または複合インデックス) を作成します。 |
| 2 | トランザクションがアクティブではありません | マルチドキュメント トランザクションが、固定の 5 秒間の制限を超えました。 | マルチドキュメント トランザクションを再試行するか、マルチドキュメント トランザクション内の操作の範囲を制限して、5 秒以内に完了するようにします。 |
| 13 | 権限がありません | 完了するためのアクセス許可が要求にありません。 | 正しいキーを使用していることを確認します。  |
| 26 | NamespaceNotFound | クエリで参照されているデータベースまたはコレクションが見つかりません。 | データベースまたはコレクションの名前が、クエリ内の名前と正確に一致していることを確認してください。|
| 50 | ExceededTimeLimit | 要求が実行のタイムアウトである 60 秒を超えました。 |  このエラーには多くの原因が考えられます。 原因の 1 つは、現在割り当てられている要求ユニットの容量が、要求を完了するのに十分ではないことです。 これは、そのコレクションまたはデータベースの要求ユニットを増やすことによって解決できます。 それ以外の場合は、大きな要求を小さい要求に分割することで、このエラーを回避できます。 このエラーが発生した書き込み操作を再試行すると、重複した書き込みが行われる可能性があります。 <br><br>RU に影響を与えることなく大量のデータを削除しようとしている場合は、次のようにします。 <br>- TTL (タイムスタンプに基づく) の使用を検討します: [Azure Cosmos DB の MongoDB 用 API でデータを期限切れにする](mongodb-time-to-live.md) <br>- カーソルまたはバッチのサイズを使用して削除を実行します。 1 つずつドキュメントをフェッチし、順番に削除します。 こうすることで実稼働アプリケーションに影響を与えずに、ゆっくりとデータを削除することができます。|
| 61 | ShardKeyNotFound | 要求内のドキュメントに、コレクションのシャード キー (Azure Cosmos DB パーティション キー) が含まれていませんでした。 | コレクションのシャード キーが要求で使用されていることを確認してください。|
| 66 | ImmutableField | 要求によって、変更不可のフィールドの変更が試行されています。 | "id" フィールドは変更できません。 要求によってそのフィールドまたはシャード キー フィールドの更新が試みられていないことを確認します。 |
| 67 | CannotCreateIndex | インデックスを作成する要求を完了できません。 | 1 つのコンテナー内に最大 500 の単一フィールド インデックスを作成できます。 複合インデックスには最大 8 つのフィールドを含めることができます (複合インデックスはバージョン 3.6 以降でサポートされます)。 |
| 112 | WriteConflict | 競合するマルチドキュメント トランザクションがあるため、マルチドキュメント トランザクションが失敗しました | マルチドキュメント トランザクションが成功するまで再試行します。 |
| 115 | CommandNotSupported | 試行された要求はサポートされていません。 | 追加の詳細情報はエラーに記載されています。 実際のデプロイ上、この機能が重要である場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポート チケットを作成してください。Azure Cosmos DB チームから折り返し連絡させていただきます。 |
| 11000 | DuplicateKey | 挿入しようとしているドキュメントのシャード キー (Azure Cosmos DB パーティション キー) が既にコレクションに存在するか、一意なインデックス フィールド制約に違反しています。 | update() 関数を使用して、既存のドキュメントを更新してください。 一意なインデックス フィールド制約に違反している場合は、シャードまたはパーティションにまだ存在しないフィールド値を使用してドキュメントを挿入または更新してください。 別のオプションとして、ID フィールドとシャード キー フィールドの組み合わせが含まれるフィールドを使用することもできます。 |
| 16500 | TooManyRequests  | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済みの要求ユニット レートを超えたために調整されました。 | Azure portal からコンテナーまたはコンテナーのセットに割り当てられているスループットをスケーリングすることを検討してください。または、操作を再試行できます。 SSR (サーバー側の再試行) を有効にした場合、Azure Cosmos DB は、このエラーが原因で失敗した要求を自動的に再試行します。 |
| 16501 | ExceededMemoryLimit | マルチテナント サービスとしての操作が、クライアントのメモリ配分を超えました。 これは、MongoDB バージョン 3.2 用の Azure Cosmos DB API にのみ適用されます。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 例: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 認識されないパイプライン ステージ名。 | 集計パイプライン要求のステージ名が認識されませんでした。 | 要求ですべての集計パイプライン名が有効であることを確認してください。 |
| - | MongoDB ワイヤ バージョンの問題 | 古いバージョンの MongoDB ドライバーは、Azure Cosmos アカウントの名前を接続文字列から検出できません。 | 接続文字列の末尾に `appName=@accountName@` を追加します。`accountName` は Azure Cosmos DB のアカウント名です。 |
| - | MongoDB クライアント ネットワークの問題 (ソケット、endOfStream の例外など)| ネットワーク要求が失敗しました。 これは、多くの場合、MongoDB クライアントが使用しようとしている非アクティブな TCP 接続が原因で発生します。 多くの場合、MongoDB ドライバーは接続プールを使用します。その結果、要求に使用されるプールからランダムな接続が選択されます。 非アクティブな接続は、通常、4 分後に Azure Cosmos DB 側でタイムアウトになります。 | アプリケーション コードでこれらの失敗した要求を再試行するか、4 分のタイムアウト期間の前に非アクティブな TCP 接続を破棄するよう MongoDB クライアント (ドライバー) の設定を変更するか、または TCP 接続をアクティブ状態で維持するように OS の `keepalive` の設定を構成することができます。<br><br>接続メッセージを回避するには、`maxConnectionIdleTime` を 1 から 2 分に設定するように接続文字列を変更することもできます。<br>- Mongo ドライバー: `maxIdleTimeMS=120000` を構成します <br>- Node.JS: `socketTimeoutMS=120000`、`autoReconnect` = true、`keepAlive` = true、`keepAliveInitialDelay` = 3 分を構成します
| - | Azure portal で Mongo シェルが動作していない | ユーザーが Mongo シェルを開こうとすると、何も起こらず、タブが空白のままになります。  | ファイアウォールを確認します。 Azure portal の Mongo シェルでは、ファイアウォールはサポートされていません。 <br>- ファイアウォール規則内のローカル コンピューターに Mongo シェルをインストールします <br>- レガシ Mongo シェルを使用します
| - | 接続文字列を使用して接続できない  | 3\.2 から 3.6 へのアップグレードの際に、接続文字列が変更されています | Azure Cosmos DB の MongoDB 用 API アカウントを使用する際は、3.6 バージョンのアカウントのエンドポイントが `*.mongo.cosmos.azure.com` 形式であるのに対し、3.2 バージョンのアカウントのエンドポイントが `*.documents.azure.com` 形式であることに注意してください。  

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。
