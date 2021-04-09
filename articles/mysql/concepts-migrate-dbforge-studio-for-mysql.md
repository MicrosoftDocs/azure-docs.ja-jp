---
title: dbForge Studio for MySQL を使用して Azure Database for MySQL に接続する
description: この記事では、dbForge Studio for MySQL を使用して Azure Database for MySQL サーバーに接続する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107194"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>dbForge Studio for MySQL を使用して Azure Database for MySQL に接続する

dbForge Studio for MySQL を使用して Azure Database for MySQL に接続するには、次のようにします。

1. [データベース] メニューで、[新しい接続] を選択します。

2. ホスト名とログイン資格情報を指定します。

3. [テスト接続] ボタンを選択して構成を確認します。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure 接続":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>バックアップと復元の機能を使用してデータベースを移行する

Studio では、さまざまな方法でデータベースを Azure に移行できます。どの方法を選ぶかは、ニーズに応じてのみ決まります。 データベース全体を移動する必要がある場合は、バックアップと復元の機能を使用することをお勧めします。 この例では、MySQL サーバーに存在する *sakila* データベースを Azure Database for MySQL に移行します。 dbForge Studio for MySQL のバックアップと復元の機能を使用する移行プロセスの背後にあるロジックは、MySQL データベースのバックアップを作成してから、それを Azure Database for MySQL で復元することです。

### <a name="back-up-the-database"></a>データベースをバックアップする

1. [データベース] メニューの [バックアップと復元] をポイントしてから、[データベースのバックアップ] を選択します。 データベース バックアップ ウィザードが表示されます。

2. データベース バックアップ ウィザードの [Backup content]\(バックアップ コンテンツ\) タブで、バックアップするデータベース オブジェクトを選択します。

3. [オプション] タブで、バックアップ プロセスを要件に合わせて構成します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="バックアップ ウィザードのオプション":::

4. 次に、エラー処理の動作とログ オプションを指定します。

5. [バックアップ] を選択します。

### <a name="restore-the-database"></a>データベースを復元する

1. 前述のように、Azure for Database for MySQL に接続します。

2. データベース エクスプローラーの本文を右クリックし、[バックアップと復元] をポイントしてから、[データベースの復元] を選択します。

3. 開いたデータベース復元ウィザードで、データベースのバックアップが含まれているファイルを選択します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="復元手順":::

4. [復元] を選択します。

5. その結果を確認します。

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>データベースのコピー機能を使用してデータベースを移行する

データベースのコピー機能はバックアップと復元に似ていますが、データベースを移行するための 2 つの手順を必要としない点が異なります。 その上、この機能を使用すると、2 つ以上のデータベースを一度に転送できます。 データベースのコピー機能は、Enterprise Edition の dbForge Studio for MySQL でのみ使用できます。
この例では、*world_x* データベースを MySQL サーバーから Azure Database for MySQL に移行します。
データベースのコピー機能を使用してデータベースを移行するには、次のようにします。

1. [データベース] メニューで、[データベースのコピー] を選択します。 

2. 表示される [データベースのコピー] タブで、ソースおよびターゲット接続を指定し、移行するデータベースを選択します。 ここでは Azure MySQL 接続を入力し、*world_x* データベースを選択します。 緑色の矢印を選択してプロセスを開始します。

3. その結果を確認します。

データベースの移行作業の結果、*world_x* データベースが Azure MySQL に正常に表示されました。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="データベースのコピーの結果":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>スキーマとデータ比較ツールを使用してデータベースを移行する

dbForge Studio for MySQL には、MySQL データベース、MySQL スキーマまたはデータを Azure に移行できるいくつかのツールが組み込まれています。 ニーズとプロジェクトの要件に応じて、機能を選択します。 データベースを選択的に移動する (つまり、特定の MySQL テーブルを Azure に移行する) 必要がある場合は、スキーマとデータ比較機能を使用することをお勧めします。
この例では、MySQL サーバー上に存在する *world* データベースを Azure Database for MySQL に移行します。 dbForge Studio for MySQL のスキーマとデータ比較機能を使用する移行プロセスの背後にあるロジックは、Azure Database for MySQL に空のデータベースを作成し、必要な MySQL データベースと同期することです。その場合、まずスキーマ比較ツールを使用してから、データ比較ツールを使用します。 これにより、MySQL スキーマとデータが Azure に正確に移動されます。

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Azure Database for MySQL に接続して空のデータベースを作成する

Azure Database for MySQL に接続して空のデータベースを作成します。

### <a name="step-2-schema-synchronization"></a>手順 2. スキーマの同期

1. [比較] メニューで、[新しいスキーマ比較] を選択します。
新しいスキーマ比較ウィザードが表示されます。

2. ソースとターゲットを選択してから、スキーマ比較オプションを指定します。 [比較] を選択します。

3. 表示される比較結果グリッドで、同期するオブジェクトを選択します。 緑色の矢印ボタンを選択して、スキーマ同期ウィザードを開きます。

4. 同期を構成するウィザードの手順を確認します。 [同期] を選択して、変更をデプロイします。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="スキーマ同期ウィザード":::

### <a name="data-comparison"></a>データ比較

1. [比較] メニューで、[新しいデータ比較] を選択します。 新しいデータ比較ウィザードが表示されます。

2. ソースとターゲットを選択してから、データ比較オプションを指定し、必要に応じてマッピングを変更します。 [比較] を選択します。

3. 表示される比較結果グリッドで、同期するオブジェクトを選択します。 緑色の矢印ボタンを選択してデータ同期ウィザードを開きます。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="データ比較の結果":::

4. 同期を構成するウィザードの手順を確認します。 [同期] を選択して、変更をデプロイします。

5. その結果を確認します。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="データ同期の結果":::

## <a name="summary"></a>まとめ

このデータベース サービスは設定、管理、およびスケーリングが容易であるため、今日では、より多くの企業がデータベースを Azure Database for MySQL に移行しています。 その移行に手間をかける必要はありません。 dbForge Studio for MySQL には、プロセスを大幅に進めることができる完璧な移行ツールが搭載されています。 Studio では、データベース転送の構成、保存、編集、自動化、およびスケジュール設定を簡単に行うことができます。

## <a name="next-steps"></a>次のステップ
- [MySQL の概要](overview.md)