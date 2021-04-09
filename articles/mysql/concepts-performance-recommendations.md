---
title: パフォーマンスの推奨事項 - Azure Database for MySQL
description: この記事では、Azure Database for MySQL のパフォーマンスの推奨事項機能について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1445ad3d6be020326c34d60c555c24c435548fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94537647"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL のパフォーマンスの推奨事項

**適用対象:** Azure Database for MySQL 5.7、8.0

パフォーマンスの推奨事項機能では、データベースを分析して、パフォーマンスの向上に役立つカスタマイズされた提案が作成されます。 推奨事項を生成するために、分析ではスキーマなどのさまざまなデータベースの特性に注目します。 パフォーマンスに関する推奨事項機能をフル活用するには、サーバー上で[クエリ ストア](concepts-query-store.md)を有効にしてください。 パフォーマンス スキーマがオフの場合、クエリ ストアをオンにすると、この機能に必要な performance_schema とパフォーマンス スキーマ インストルメントのサブセットが有効になります。 パフォーマンスに関する推奨事項を実装した後は、パフォーマンスをテストし、変更の影響を評価する必要があります。

## <a name="permissions"></a>アクセス許可

パフォーマンスに関する推奨事項機能を使用して分析を実行するには、**所有者** または **共同作成者** の許可が必要です。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

[パフォーマンスに関する推奨事項](concepts-performance-recommendations.md)機能は、パフォーマンスを向上させる可能性のあるインデックスを識別するために、サーバーの間でワークロードを分析します。

Azure portal の MySQL サーバーに関するページで、メニュー バーの **[インテリジェント パフォーマンス]** セクションから、 **[パフォーマンスの推奨事項]** を開きます。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="パフォーマンスに関する推奨事項ランディング ページ":::

**[解析]** を選択してデータベースを選択すると、分析が開始されます。 ワークロードによっては、分析が完了するまでに数分かかる場合があります。 分析が完了すると、ポータルで通知があります。 分析では、データベースの詳細な検査が行われます。 分析はピーク時を避けて実行することをお勧めします。

**[推奨事項]** ウィンドウには、推奨事項の一覧 (推奨事項が見つかった場合) と、その推奨事項が生成された関連するクエリ ID が表示されます。 このクエリ ID を使用して、[mysql.query_store](concepts-query-store.md#mysqlquery_store) ビューでクエリの詳細を確認できます。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="パフォーマンスに関する推奨事項の新しいページ":::

推奨事項は自動的には適用されません。 推奨事項を適用するには、クエリ テキストをコピーし、任意のクライアントから実行します。 推奨事項を評価するため、監視とテストを忘れずに行ってください。

## <a name="recommendation-types"></a>推奨事項の種類

### <a name="index-recommendations"></a>インデックスに関する推奨事項

"*インデックスの作成*" 推奨事項は、ワークロード内で特に頻繁に実行されるクエリまたは時間のかかるクエリを高速化するために、新しいインデックスを提案します。 この推奨事項の種類では、[クエリ ストア](concepts-query-store.md)を有効にする必要があります。 クエリ ストアは、クエリ情報を収集し、クエリの実行時間や頻度に関する詳細な統計情報を提供します。この情報は、分析で推奨事項を作成するために使用されます。

### <a name="query-recommendations"></a>クエリに関する推奨事項

クエリに関する推奨事項では、ワークロード内のクエリに対する最適化とリライトが提案されます。 MySQL クエリのアンチパターンを特定して構文的に修正することで、時間のかかるクエリのパフォーマンスを向上させることができます。 この推奨事項の種類では、クエリ ストアを有効にする必要があります。 クエリ ストアは、クエリ情報を収集し、クエリの実行時間や頻度に関する詳細な統計情報を提供します。この情報は、分析で推奨事項を作成するために使用されます。

## <a name="next-steps"></a>次のステップ
- Azure Database for MySQL での[監視とチューニング](concepts-monitoring.md)の詳細を確認する。