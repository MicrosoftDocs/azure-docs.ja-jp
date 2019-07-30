---
title: Azure Cosmos Emulator ダウンロードとリリース ノート
description: Azure Cosmos Emulator リリース ノートをダウンロードしてお読みください。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332150"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>ローカルでの開発とテストに Azure Cosmos Emulator を使用する

この記事では、Azure Cosmos エミュレーター リリース ノートを各リリースで行われた機能更新プログラムを示して説明します。 また、ダウンロードして使用するためのエミュレーターの最新バージョンも記載しています。

## <a name="download"></a>ダウンロード

| | |
|---------|---------|
|**MSI ダウンロード**|[Microsoft ダウンロード センター](https://aka.ms/cosmosdb-emulator)|
|**作業開始**|[Azure Cosmos Emulator を使用してローカルで開発する](local-emulator.md)|

## <a name="release-notes"></a>リリース ノート

### <a name="243"></a>2.4.3

- MongoDB サービスの既定での無効な開始。 既定で、SQL エンドポイントのみが有効になります。 ユーザーは、エミュレーターの "/EnableMongoDbEndpoint" コマンド ライン オプションを使用して、エンドポイントを手動で開始する必要があります。 これは、他のすべてのサービス エンドポイント (Gremlin、Cassandra、Table など) と同様です。
- "/AllowNetworkAccess" で開始した場合のエミュレーターのバグ (Gremlin、Cassandra、および Table エンドポイントが外部クライアントからの要求を適切に処理しない) を修正しました。
- 直接接続のポートをファイアウォール規則の設定に追加します。

### <a name="240"></a>2.4.0

- Pulse Client などのネットワーク監視アプリがホスト コンピューター上にあるときにエミュレーターの起動が失敗する問題を修正しました。
