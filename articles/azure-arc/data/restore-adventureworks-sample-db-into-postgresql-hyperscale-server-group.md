---
title: AdventureWorks サンプル データベースを Azure Arc 対応 PostgreSQL Hyperscale にインポートする
description: AdventureWorks サンプル データベースを Azure Arc 対応 PostgreSQL Hyperscale に復元する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a9efa17fb782d5a913493907b66973272e4e0356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441790"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>AdventureWorks サンプル データベースを Azure Arc 対応 PostgreSQL Hyperscale にインポートする

[AdventureWorks](/sql/samples/adventureworks-install-configure) は、チュートリアルで使用される OLTP データベースと例を含むサンプル データベースです。 [SQL Server サンプル GitHub リポジトリ](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)の一部として、Microsoft によって提供および管理されています。

オープンソース プロジェクトによって、AdventureWorks データベースが Azure Arc 対応 PostgreSQL Hyperscale と互換性を持つように変換されました。
- [元のプロジェクト](https://github.com/lorint/AdventureWorks-for-Postgres)
- [CSV ファイルが PostgreSQL と互換性を持つように事前に変換するプロジェクトに従う](https://github.com/NorfolkDataSci/adventure-works-postgres)

このドキュメントでは、AdventureWorks サンプル データベースを PostgreSQL Hyperscale サーバー グループにインポートするための簡単なプロセスについて説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks のバックアップ ファイルをダウンロードする

AdventureWorks の .sql ファイルを PostgreSQL Hyperscale サーバー グループ コンテナーにダウンロードします。 この例では、`kubectl exec` コマンドを使用して、PostgreSQL Hyperscale サーバー グループ コンテナー内のコマンドをリモートで実行し、ファイルをコンテナーにダウンロードします。 このファイルは、`curl` がアクセスできる任意の場所からダウンロードできます。 他のデータベースのバックアップ ファイルを PostgreSQL Hyperscale サーバー グループ コンテナーにプルする場合も、これと同じ方法を使用します。 PostgreSQL Hyperscale サーバー グループ コンテナーでは、データベースとスキーマを簡単に作成し、データを設定することができます。

次のようなコマンドを実行して、ファイルをダウンロードします。実行する前にポッド名と名前空間名の値を置き換えます。

> [!NOTE]
>  GitHub からファイルをダウンロードするには、コンテナーが 443 経由でインターネットに接続されている必要があります。

> [!NOTE]
>  Postgres Hyperscale サーバー グループのコーディネーター ノードのポッド名を使用します。 その名前は <server group name>c-0 です (たとえば、postgres01c-0 の場合、c は Coordinator ノードを表します)。  ポッド名がわからない場合は、`kubectl get pod` コマンドを実行します。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>手順 2: AdventureWorks データベースをインポートする

同様に、kubectl exec コマンドを実行して、PostgreSQL Hyperscale サーバー グループ コンテナーに含まれている psql CLI ツールを使用し、データベースを作成して読み込むことができます。

次のようなコマンドを実行して、最初に空のデータベースを作成します。そのためには、まずポッド名と名前空間名の値を置き換えてから実行します。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

その後、次のようなコマンドを実行してデータベースをインポートします。そのためには、まずポッド名と名前空間名の値を置き換えてから実行します。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **注意事項: Azure Arc 対応 PostgreSQL Hyperscale をスケールアウトして、PostgreSQL Hyperscale サーバー グループのワーカー ノード全体にデータ/テーブルをシャード/分散することによるパフォーマンス上の利点はあまりありません。[推奨される次の手順](#suggested-next-steps)を参照してください。**

## <a name="suggested-next-steps"></a>推奨される次の手順
- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for PostgreSQL Hyperscale のすべての利点を活用するために、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。 :
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
