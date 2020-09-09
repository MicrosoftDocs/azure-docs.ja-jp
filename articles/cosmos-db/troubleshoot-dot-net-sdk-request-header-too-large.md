---
title: Azure Cosmos DB での "要求ヘッダーが大きすぎる" メッセージまたは 400 Bad Request のトラブルシューティング
description: "\"要求ヘッダーが大きすぎる\" 例外を診断して修正する方法について説明します。"
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: d5d66ca05390af5f6fef91ca959f1db3d547d3fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014049"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB の "要求ヘッダーが大きすぎる" メッセージを診断してトラブルシューティングする
"要求ヘッダーが大きすぎる" メッセージは、HTTP エラー コード 400 でスローされます。 このエラーは、要求ヘッダーのサイズが大きくなりすぎて、最大許容サイズを超えている場合に発生します。 最新バージョンの SDK を使用することをお勧めします。 バージョン 3.x または 2.x では、ヘッダー サイズのトレースが例外メッセージに追加されるため、少なくともこれらのバージョンを使用してください。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
"要求ヘッダーが大きすぎる" メッセージは、セッションまたは継続トークンが大きすぎる場合に発生します。 次のセクションでは、各カテゴリの問題の原因とその解決方法について説明します。

### <a name="session-token-is-too-large"></a>セッション トークンが大きすぎる

#### <a name="cause"></a>原因:
400 Bad Request が発生する最も可能性の高い原因は、セッション トークンが大きすぎることです。 次の文に該当する場合は、セッション トークンが大きすぎます。

* このエラーは、継続トークンがないポイント操作 (作成、読み取り、更新など) で発生します。
* この例外は、アプリケーションに変更を加えずに開始されました。 セッション トークンは、コンテナー内のパーティション数が増加するにつれて増大します。 データ量が増加したり、スループットが増加したりすると、パーティションの数が増加します。

#### <a name="temporary-mitigation"></a>一時的な軽減策: 
クライアント アプリケーションを再起動して、すべてのセッション トークンをリセットします。 最終的に、問題の原因となったセッション トークンは以前のサイズに戻ります。 この問題を完全に回避するには、次のセクションの解決方法を使用します。

#### <a name="solution"></a>解決方法:
1. [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) または [.NET v2](performance-tips.md) パフォーマンスのヒントに関する記事のガイダンスに従います。 伝送制御プロトコル (TCP) で直接接続モードが使用されるようにアプリケーションを変換します。 TCP プロトコルを使用した直接接続モードでは HTTP プロトコルのようにヘッダー サイズの制限がないため、この問題は回避されます。 最新バージョンの SDK を使用してください。このバージョンには、サービスの相互運用性がない場合のクエリ操作に対する修正が含まれています。
1. TCP プロトコルを使用した直接接続モードがワークロードのオプションでない場合は、[クライアントの整合性レベル](how-to-manage-consistency.md)を変更することで軽減します。 セッション トークンは、Azure Cosmos DB の既定の整合性レベルであるセッションの整合性にのみ使用されます。 その他の整合性レベルでは、セッション トークンは使用されません。

### <a name="continuation-token-is-too-large"></a>継続トークン ヘッダーが大きすぎる

#### <a name="cause"></a>原因:
継続トークンが使用されている場合、継続トークンが大きくなりすぎたり、さまざまなクエリの継続トークンのサイズが異なったりすると、クエリ操作に対して 400 Bad Request が発生します。
    
#### <a name="solution"></a>解決方法:
1. [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) または [.NET v2](performance-tips.md) パフォーマンスのヒントに関する記事のガイダンスに従います。 TCP プロトコルで直接接続モードが使用されるようにアプリケーションを変換します。 TCP プロトコルを使用した直接接続モードでは HTTP プロトコルのようにヘッダー サイズの制限がないため、この問題は回避されます。 
1. TCP プロトコルを使用した直接接続モードがワークロードのオプションでない場合は、`ResponseContinuationTokenLimitInKb` オプションを設定します。 このオプションは、v2 の `FeedOptions` または v3 の `QueryRequestOptions` で見つかります。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
