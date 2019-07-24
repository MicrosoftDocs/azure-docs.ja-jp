---
title: Azure データベース リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure データベース リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: bfa076f33ce3b2e7315a07717bba5768b89511c2
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495286"
---
# <a name="migrate-database-resources-to-global-azure"></a>データベース リソースをグローバル Azure に移行する

この記事には、Azure データベース リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="sql-database"></a>SQL Database

より小さな Azure SQL Database ワークロードを移行するには、エクスポート関数を使用して BACPAC ファイルを作成します。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含む圧縮 (zip 形式) ファイルです。 BACPAC ファイルを作成した後では、(たとえば、AzCopy を使用して) ターゲット環境にファイルをコピーし、インポート関数を使用してデータベースを再構築することができます。 次の考慮事項に注意してください。

- エクスポートでトランザクション上の一貫性が保たれるようにするには、次のいずれかの条件に当てはまることを確認します。
  - エクスポート中に、書き込み操作は行われません。
  - ご利用の SQL データベースのトランザクション上一貫性のあるコピーからエクスポートします。
- Azure Blob ストレージにエクスポートする場合、BACPAC ファイルのサイズは最大 200 GB に制限されています。 これより大きな BACPAC ファイルについては、ローカル ストレージにエクスポートします。
- SQL Database からのエクスポート操作にかかる時間が 20 時間を超える場合、操作はキャンセルされる可能性があります。 パフォーマンスを向上する方法のヒントについては、次の記事を確認してください。

> [!NOTE]
> エクスポート中にサーバーの DNS 名が変更されるため、接続文字列はエクスポート操作の後に変更されます。

詳細:

- [BACPAC ファイルにデータベースをエクスポート](../sql-database/sql-database-export.md)する方法を学習してください。
- [BACPAC ファイルをデータベースにインポート](../sql-database/sql-database-import.md)する方法を学習してください。
- [Azure SQL Database のドキュメント](https://docs.microsoft.com/azure/sql-database/)を確認してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse のリソースを Azure Germany からグローバル Azure に移行するには、Azure SQL Database で説明されている手順に従います。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB データ移行ツールを使用して Azure Cosmos DB にデータを移行することができます。 Azure Cosmos DB データ移行ツールは、さまざまなソースからデータを Azure Cosmos DB にインポートするためのオープン ソース ソリューションです。

Azure Cosmos DB データ移行ツールは、グラフィカル インターフェイス ツールまたはコマンド ライン ツールとして使用できます。 ソース コードは、[Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub リポジトリで入手できます。 [ツールのコンパイル済みバージョン](https://www.microsoft.com/download/details.aspx?id=46436)は、Microsoft ダウンロード センターで入手できます。

Azure Cosmos DB リソースを移行するには、次の手順を完了することをお勧めします。

1. アプリケーションのアップタイム要件およびアカウント構成を確認して、最適なアクション プランを決定します。
1. データ移行ツールを実行して、Azure Germany から新しいリージョンにアカウント構成を複製します。
1. メンテナンス ウィンドウを使用できる場合は、データ移行ツールを実行することによって、ソースから宛先にデータをコピーします。
1. メンテナンス ウィンドウを使用できない場合は、ツールを実行してソースから宛先にデータをコピーしてから、次の手順を完了します。
   1. 構成主導のアプローチを使用して、アプリケーションでの読み取り/書き込みに変更を加えます。
   1. 初回の同期を完了します。
   1. 増分同期を設定し、変更フィードを反映します。
   1. 新しいアカウントの読み取りを指示し、アプリケーションを確認します。
   1. 古いアカウントへの書き込みを停止し、変更フィードが反映されていることを確認してから、新しいアカウントに対する書き込みを指示します。
   1. このツールを停止し、古いアカウントを削除します。
1. 新旧のアカウント間でデータが一貫性のあることを検証するツールを実行します。

詳細:

- [Azure Cosmos DB の概要](../cosmos-db/introduction.md) を参照してください。
- [Azure Cosmos DB にデータをインポートする](../cosmos-db/import-data.md)方法を説明します。

## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Azure Cache for Redis インスタンスを Azure Germany からグローバル Azure に移行する場合には、いくつかのオプションがあります。 選択するオプションは、要件によって異なります。

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>オプション 1:データ損失を受け入れ、新しいインスタンスを作成する

次の両方の条件が該当する場合、このアプローチは最も道理にかなっています。

- 一時的なデータ キャッシュとして Azure Cache for Redis を使用している。
- アプリケーションが新しいリージョンにキャッシュ データを自動的に再取り込みする。

データ損失とともに移行し、新しいインスタンスを作成するには、次のようにします。

1. 新しいターゲット リージョンに新しい Azure Cache for Redis インスタンスを作成します。
1. 新しいリージョンの新しいインスタンスを使用するようにアプリケーションを更新します。
1. ソース リージョンで古い Azure Cache for Redis インスタンスを削除します。

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>オプション 2:ソース インスタンスからターゲット インスタンスへのデータのコピー

Azure Cache for Redis チームのメンバーは、Azure Cache for Redis のあるインスタンスから別のインスタンスにインポートまたはエクスポート機能を必要とせずデータをコピーする、オープン ソース ツールを作成しました。 そのツールについては、以下の手順の手順 4 を参照してください。

対象インスタンスにソース インスタンスからデータをコピーするには、次のようにします。

1. ソース リージョンに VM を作成します。 Azure Cache for Redis のデータセットが大きい場合は、コピー時間を最小限にするために、必ず比較的強力な VM サイズを選択するようにします。
1. ターゲット リージョンに新しい Azure Cache for Redis インスタンスを作成します。
1. **ターゲット** インスタンスからデータをフラッシュします。 (**ソース** インスタンスからはフラッシュ*しないでください*。 フラッシュが必要なのは、コピー ツールがターゲットの場所の既存のキーを*上書きしない*からです)。
1. ソースの Azure Cache for Redis インスタンスからターゲットの Azure Cache for Redis インスタンスにデータを自動的にコピーするには、次のツールを使用します。[ツール ソース](https://github.com/deepakverma/redis-copy)と[ツール ダウンロード](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。

> [!NOTE]
> このプロセスは、データセットのサイズに応じて長時間かかる場合があります。

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>オプション 3: ソース インスタンスからのエクスポート、宛先インスタンスへのインポート

このアプローチでは、Premium レベルでのみ利用できる機能を利用します。

ソース インスタンスからエクスポートし、宛先インスタンスにインポートするには、次のようにします。

1. ターゲット リージョンに、新しい Premium レベルの Azure Cache for Redis を作成します。 ソースの Azure Cache for Redis インスタンスと同じサイズを使用します。
1. [ソースのキャッシュからデータをエクスポート](../redis-cache/cache-how-to-import-export-data.md)するか、または [Export-AzRedisCache PowerShell コマンドレット](/powershell/module/az.rediscache/export-azrediscache)を使用します。

   > [!NOTE]
   > エクスポート Azure Storage アカウントは、キャッシュ インスタンスと同じリージョンに存在する必要があります。

1. エクスポートされた BLOB を宛先リージョンのストレージ アカウントに (たとえば AzCopy を使用して) コピーします。
1. [宛先キャッシュにデータをインポート](../redis-cache/cache-how-to-import-export-data.md)するか、または [Import-AzRedisCAche PowerShell コマンドレット](/powershell/module/az.rediscache/import-azrediscache)を使用します。
1. ターゲットの Azure Cache for Redis を使用するように、アプリケーションを再構成します。

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>オプション 4: データを 2 つの Azure Cache for Redis インスタンスに書き込み、1 つのインスタンスから読み取る

このアプローチの場合、アプリケーションを変更する必要があります。 アプリケーションは、いずれかのキャッシュ インスタンスからの読み取り中に、複数のキャッシュ インスタンスにデータを書き込む必要があります。 このアプローチは、Azure Cache for Redis に格納されたデータが次の条件を満たしている場合に有効です。
- データが定期的に更新される。 
- すべてのデータがターゲットの Azure Cache for Redis に書き込まれる。
- すべてのデータを更新するのに十分な時間がある。

詳細:

- 「[Azure Cache for Redis の概要](../redis-cache/cache-overview.md)」を確認してください。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
