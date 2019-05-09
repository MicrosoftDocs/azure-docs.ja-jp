---
title: Azure Database for PostgreSQL のパフォーマンスに関する推奨事項
description: この記事では、Azure Database for PostgreSQL のパフォーマンスに関する推奨機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620174"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のパフォーマンスに関する推奨事項

**適用対象:** Azure Database for PostgreSQL 9.6 および 10

パフォーマンスに関する推奨事項機能では、データベースを分析し、パフォーマンス向上に役立つカスタマイズされた提案が作成されます。 推奨事項を生成するために、分析ではスキーマなどのさまざまなデータベースの特性に注目します。 パフォーマンスに関する推奨事項機能をフル活用するには、サーバー上で[クエリ ストア](concepts-query-store.md)を有効にしてください。 パフォーマンスに関する推奨事項を実装した後は、パフォーマンスをテストし、変更の影響を評価する必要があります。 

## <a name="permissions"></a>アクセス許可
パフォーマンスに関する推奨事項機能を使用して分析を実行するには、**所有者**または**共同作成者**の許可が必要です。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項
[パフォーマンスに関する推奨事項](concepts-performance-recommendations.md)機能は、パフォーマンスを向上させる可能性のあるインデックスを識別するために、サーバーの間でワークロードを分析します。

Azure portal の PostgreSQL サーバーに関するページで、メニュー バーの **[インテリジェント パフォーマンス]** セクションから、**[パフォーマンスの推奨事項]** を開きます。

![パフォーマンスに関する推奨事項ランディング ページ](./media/concepts-performance-recommendations/performance-recommendations-page.png)

**[解析]** を選択してデータベースを選択すると、分析が開始されます。 ワークロードによっては、分析が完了するまでに数分かかる場合があります。 分析が完了すると、ポータルで通知があります。 分析では、データベースの詳細な検査が行われます。 分析はピーク時を避けて実行することをお勧めします。 

**[推奨事項]** ウィンドウでは、推奨事項が見つかった場合に、その一覧が表示されます。

![パフォーマンスに関する推奨事項の新しいページ](./media/concepts-performance-recommendations/performance-recommendations-result.png)

推奨事項は自動的には適用されません。 推奨事項を適用するには、クエリ テキストをコピーし、任意のクライアントから実行します。 推奨事項を評価するため、監視とテストを忘れずに行ってください。 

## <a name="recommendation-types"></a>推奨事項の種類

現時点では、2 種類の推奨事項がサポートされています: "*インデックスの作成*" と "*インデックスのドロップ*"。

### <a name="create-index-recommendations"></a>インデックスの作成に関する推奨事項
"*インデックスの作成*" 推奨事項は、ワークロード内で特に頻繁に実行されるクエリまたは時間のかかるクエリを高速化するために、新しいインデックスを提案します。 この推奨事項の種類では、[クエリ ストア](concepts-query-store.md)を有効にする必要があります。 クエリ ストアは、クエリ情報を収集し、クエリの実行時間や頻度に関する詳細な統計情報を提供します。この情報は、分析で推奨事項を作成するために使用されます。

### <a name="drop-index-recommendations"></a>インデックスの削除に関する推奨事項
足りないインデックスの検出だけでなく、Azure Database for PostgreSQL は既存のインデックスのパフォーマンスを継続的に分析します。 インデックスがほとんど使用されなかったり、冗長であったりした場合、アナライザーによりドロップが推奨されます。


## <a name="next-steps"></a>次の手順
- Azure Database for PostgreSQL での[監視およびチューニング](concepts-monitoring.md)の詳細を確認する。

