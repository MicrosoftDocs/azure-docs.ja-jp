---
title: Azure Cosmos Emulator ダウンロードとリリース ノート
description: Azure Cosmos Emulator リリース ノートをダウンロードしてお読みください。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973704"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - リリース ノートとダウンロード情報

この記事では、Azure Cosmos エミュレーター リリース ノートを各リリースで行われた機能更新プログラムを示して説明します。 また、ダウンロードして使用するためのエミュレーターの最新バージョンも記載しています。

## <a name="download"></a>ダウンロード

| | |
|---------|---------|
|**MSI ダウンロード**|[Microsoft ダウンロード センター](https://aka.ms/cosmosdb-emulator)|
|**作業開始**|[Azure Cosmos Emulator を使用してローカルで開発する](local-emulator.md)|

## <a name="release-notes"></a>リリース ノート

### <a name="246"></a>2.4.6

- このリリースは、2019 年 7 月時点の Azure Cosmos サービスの機能と同等です。ただし、[Azure Cosmos エミュレーターを使用したローカルでの開発](local-emulator.md)に関するページに記載されている例外を除きます。 また、コマンド ラインを介して起動した場合のエミュレーターのシャットダウンに関連するバグ、および直接モード接続を使用して行われる SDK クライアントに対する内部 IP アドレスのオーバーライドに関するバグも修正されます。

### <a name="243"></a>2.4.3

- MongoDB サービスの既定での無効な開始。 既定で、SQL エンドポイントのみが有効になります。 ユーザーは、エミュレーターの "/EnableMongoDbEndpoint" コマンド ライン オプションを使用して、エンドポイントを手動で開始する必要があります。 これは、他のすべてのサービス エンドポイント (Gremlin、Cassandra、Table など) と同様です。
- "/AllowNetworkAccess" で開始した場合のエミュレーターのバグ (Gremlin、Cassandra、および Table エンドポイントが外部クライアントからの要求を適切に処理しない) を修正しました。
- 直接接続のポートをファイアウォール規則の設定に追加します。

### <a name="240"></a>2.4.0

- Pulse Client などのネットワーク監視アプリがホスト コンピューター上にあるときにエミュレーターの起動が失敗する問題を修正しました。
