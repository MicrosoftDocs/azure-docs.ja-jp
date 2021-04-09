---
title: Azure Cosmos DB .NET SDK 2.0 (Microsoft.Azure.Cosmos) を使用するようにアプリケーションを移行する
description: 既存の .NET アプリケーションを v1 SDK から Core (SQL) API 用の .NET SDK v2 にアップグレードする方法について説明します。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94550035"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Azure Cosmos DB .NET SDK v2 を使用するようにアプリケーションを移行する

> [!IMPORTANT]
> .NET SDK の v3 が現在使用可能であることに注意してください。v2 から v3 への移行計画は[こちら](migrate-dotnet-v3.md)で確認できます。 Azure Cosmos DB .NET SDK v2 の詳細については、[リリース ノート](sql-api-sdk-dotnet.md)、[.NET GitHub リポジトリ](https://github.com/Azure/azure-cosmos-dotnet-v2)、.NET SDK v2 の[パフォーマンスに関するヒント](performance-tips.md)、および[トラブルシューティング ガイド](troubleshoot-dot-net-sdk.md)を参照してください。
>

この記事では、既存の v1 .NET アプリケーションを、Core (SQL) API 用の Azure Cosmos DB .NET SDK v2 にアップグレードする際の考慮事項について説明します。 Azure Cosmos DB .NET SDK v2 は、`Microsoft.Azure.DocumentDB` 名前空間に対応します。 v2 SDK `Microsoft.Azure.Cosmos` を使用するように、次のいずれかの Azure Cosmos DB .NET プラットフォームからアプリケーションを移行する場合は、このドキュメントに記載されている情報を使用できます。

* SQL API 用の Azure Cosmos DB .NET Framework v1 SDK
* SQL API 用の Azure Cosmos DB .NET Core SDK v1

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK で利用可能なもの

次のように、v2 SDK では多くのユーザビリティとパフォーマンスが向上しています。

* Windows 以外のクライアント向けの TCP 直接モードのサポート
* マルチリージョンの書き込みのサポート
* クエリ パフォーマンスの向上
* 地理空間/ジオメトリ コレクションとインデックス作成のサポート
* ダイレクト/TCP トランスポート診断の機能強化
* 確立される接続数を減らすために、ダイレクト TCP トランスポート スタックを更新
* RequestTimeout での待機時間短縮の改善

SDK の再試行ロジックおよび下位レベルのほとんどは、ほぼ未変更のままです。

## <a name="why-migrate-to-the-net-v2-sdk"></a>.NET v2 SDK に移行する理由

数々のパフォーマンス向上に加え、最新の SDK で行われた新機能への投資は、以前のバージョンにはバックポートされません。

また、古い SDK は新しいバージョンに置き換えられ、v1 SDK は[メンテナンス モード](sql-api-sdk-dotnet.md)になります。 最適な開発環境を実現するために、アプリケーションを新しいバージョンの SDK に移行することをお勧めします。

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>V1 SDK から v2 SDK への主な変更点

### <a name="direct-mode--tcp"></a>直接モード + TCP

.NET v2 SDK では、直接モードとゲートウェイ モードの両方がサポートされるようになりました。 直接モードでは、TCP プロトコルを介した接続がサポートされ、より少ないネットワーク ホップ数でバックエンド レプリカに直接接続するため、パフォーマンスが向上します。

詳細については、[Azure Cosmos DB SQL SDK 接続モードに関するガイド](sql-sdk-connection-modes.md)を参照してください。

### <a name="session-token-formatting"></a>セッション トークン形式

v2 SDK では、v1 で使用されていた "*シンプル*" セッション トークン形式は使用されなくなりました。代わりに、この SDK では、"*ベクター*" 形式が使用されます。 この形式は、バージョンが異なるクライアント アプリケーションに渡すときに変換する必要があります。これは、これらの形式に互換性がないためです。

詳細については、[.NET SDK でのセッション トークン形式の変換](how-to-convert-session-token.md)に関する記事を参照してください。

### <a name="using-the-net-change-feed-processor-sdk"></a>.NET 変更フィード プロセッサ SDK の使用

.NET 変更フィード プロセッサ ライブラリ 2.1.x には、`Microsoft.Azure.DocumentDB` 2.0 以降が必要です。

2\.1.x ライブラリの主な変更点を次に示します。

* 安定性と診断能力の向上
* エラーと例外の処理の改善
* パーティション分割されたリース コレクションの追加サポート
* 追加のエラー処理とトレースのための `ChangeFeedDocument` インターフェイスおよびクラスを実装する高度な拡張機能
* パーティションごとに継続トークンを保持するためのカスタム ストアの使用に対するサポートの追加

詳細については、変更フィード プロセッサ ライブラリの[リリース ノート](sql-api-sdk-dotnet-changefeed.md)を参照してください。

### <a name="using-the-bulk-executor-library"></a>Bulk Executor ライブラリの使用

v2 Bulk Executor ライブラリは、現在、Azure Cosmos DB .NET SDK 2.5.1 以降に依存しています。

詳細については、「[Azure Cosmos DB Bulk Executor ライブラリの概要](bulk-executor-overview.md)」および .NET Bulk Executor ライブラリの[リリース ノート](sql-api-sdk-bulk-executor-dot-net.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* SQL API v2 用の Azure Cosmos DB を使用してパフォーマンスが最大になるようにアプリケーションを最適化するために、[追加のパフォーマンスのヒント](sql-api-get-started.md)を確認する
* [v2 SDK でできること](sql-api-dotnet-samples.md)について、さらに学習する