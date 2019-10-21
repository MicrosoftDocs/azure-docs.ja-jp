---
title: Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) のノード
description: Azure Database for PostgreSQL のサーバー グループに含まれる 2 種類のノード (コーディネーターとワーカー) について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 097fcdb3a7e53bb63db9dc2d352d754062df7be6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947554"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) のノード

Hyperscale (Citus) (プレビュー) のホスティング タイプは、Azure Database for PostgreSQL サーバー (ノードと呼ばれます) が、「シェアード ナッシング方式」のアーキテクチャで互いに調和できるようにします。 サーバー グループ内のノードは集合的に、単一サーバーで可能性のある分量より多くのデータを保持し、より多くの CPU コアを使用します。 このアーキテクチャでは、サーバー グループにノードを追加してデータベースを拡張することもできます。

## <a name="coordinator-and-workers"></a>コーディネーターとワーカー

すべてのサーバー グループに、1 つのコーディネーター ノードと複数のワーカーが含まれます。 アプリケーションは、クエリをコーディネーター ノードに送信し、このノードが関連するワーカーにリレーして、結果を累積します。 アプリケーションは、ワーカーに直接接続することはできません。

クエリごとに、コーディネーターは、必要なデータが単一のノードと複数のノードのどちらに存在するかによって、単一のワーカー ノードにルーティングするか、複数のノードにわたって並列処理します。 コーディネーターは、メタデータ テーブルを参照して、どのように処理するかを決定します。 これらのテーブルは、ワーカー ノードの DNS 名およびヘルスと、ノード間でのデータの分布を追跡します。

## <a name="next-steps"></a>次の手順
- ノードで[分散データ](concepts-hyperscale-distributed-data.md)を格納する方法について学習する
