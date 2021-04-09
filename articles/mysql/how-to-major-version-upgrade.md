---
title: Azure Database for MySQL 単一サーバーでのメジャー バージョンのアップグレード
description: この記事では、Azure Database for MySQL 単一サーバーでメジャー バージョンをアップグレードする方法について説明します
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 13cf315291cdf788951e352e430976851b30ce0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216719"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 単一サーバーでのメジャー バージョンのアップグレード

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

> [!IMPORTANT]
> Azure database for MySQL 単一サーバーのメジャー バージョンのアップグレードは、パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL 単一サーバーで MySQL のメジャー バージョンをインプレース アップグレードする方法について説明します。

この機能を使用すると、データを移動したり、アプリケーションの接続文字列を変更したりしなくても、ボタンをクリックするだけで MySQL 5.6 サーバーを MySQL 5.7 にインプレース アップグレードできます。

> [!Note]
> * メジャー バージョンのアップグレードは、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードにのみ使用できます。
> * アップグレード操作中は、サーバーを使用できなくなります。 したがって、計画メンテナンス期間中にアップグレードを実行することをお勧めします。 [読み取りレプリカを使用して、MySQL 5.6 から MySQL 5.7 へのメジャーバージョンのアップグレードを最小限のダウンタイムで実行する](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)こともできます。

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Azure portal を使用して MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを実行する

Azure portal を使用して Azure Database for MySQL 5.6 サーバーでメジャー バージョンのアップグレードを実行するには、次の手順に従います。

> [!IMPORTANT]
> 直接、運用環境をアップグレードするのではなく、最初に、サーバーの復元済みコピーにアップグレードを実行することをお勧めします。 [ポイントインタイム リストアを実行する方法](howto-restore-server-portal.md#point-in-time-restore)を参照してください。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL 5.6 サーバーを選択します。

2. **[概要]** ページのツール バーで **[アップグレード]** ボタンをクリックします。

3. **[アップグレード]** セクションで、 **[OK]** を選択して、Azure Database for MySQL 5.6 サーバーを 5.7 サーバーにアップグレードします。

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 概要 - アップグレード":::

4. アップグレードが成功したことを確認する通知が表示されます。


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Azure CLI を使用して MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを実行する

Azure CLI を使用して Azure Database for MySQL 5.6 サーバーでメジャー バージョンのアップグレードを実行するには、次の手順に従います。

> [!IMPORTANT]
> 直接、運用環境をアップグレードするのではなく、最初に、サーバーの復元済みコピーにアップグレードを実行することをお勧めします。 [ポイントインタイム リストアを実行する方法](howto-restore-server-cli.md#server-point-in-time-restore)を参照してください。

1. [Azure CLI for Windows](/cli/azure/install-azure-cli) をインストールするか、[Azure Cloud Shell](../cloud-shell/overview.md) で Azure CLI を使用してアップグレード コマンドを実行します。 
 
   このアップグレードでは、Azure CLI のバージョン 2.16.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 az version を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 最新バージョンにアップグレードするには、az upgrade を実行します。

2. サインインしたら、[az mysql server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade) コマンドを実行します。

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   コマンド プロンプトに "-Running" (実行中) というメッセージが表示されます。 このメッセージが表示されなくなると、バージョンのアップグレードが完了しています。

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Azure portal を使用して、読み取りレプリカで MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを実行する

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL 5.6 読み取りレプリカ サーバーを選択します。

2. **[概要]** ページのツール バーで **[アップグレード]** ボタンをクリックします。

3. **[アップグレード]** セクションで、 **[OK]** を選択して、Azure Database for MySQL 5.6 読み取りレプリカ サーバーを 5.7 サーバーにアップグレードします。

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 概要 - アップグレード":::

4. アップグレードが成功したことを確認する通知が表示されます。

5. **[概要]** ページで、Azure Database for MySQL の読み取りレプリカ サーバーのバージョンが 5.7 であることを確認します。

6. ここで、プライマリ サーバーにアクセスし、そこで[メジャー バージョンのアップグレード](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal)を実行します。

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>読み取りレプリカを使用して、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを最小限のダウンタイムで実行する

読み取りレプリカを利用すると、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを最小限のダウンタイムで実行することができます。 この考え方は、サーバーの読み取りレプリカを最初に 5.7 にアップグレードし、後でアプリケーションをフェールオーバーして読み取りレプリカをポイントし、それを新しいプライマリにするというものです。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MySQL 5.6 を選択します。

2. プライマリ サーバーから[読み取りレプリカ](./concepts-read-replicas.md#create-a-replica)を作成します。

3. [読み取りレプリカをバージョン 5.7 にアップグレード](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal)します。

4. レプリカ サーバーがバージョン 5.7 で実行されていることを確認したら、アプリケーションからプライマリ サーバーへの接続を停止します。
 
5. レプリケーションの状態を確認し、すべてのデータが同期されるように、レプリカがすべてプライマリに追いついていることを確認し、プライマリで確実に新しい操作が実行されないようにします。

   レプリカ サーバーで [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) コマンドを呼び出して、レプリケーションの状態を確認します。

   ```sql
   SHOW SLAVE STATUS\G
   ```

   `Slave_IO_Running` と `Slave_SQL_Running` の状態が "yes" で、`Seconds_Behind_Master` の値が "0" であれば、レプリケーションは正常に機能しています。 `Seconds_Behind_Master` は、レプリカの遅れの程度を示しています。 この値が "0" 以外である場合、レプリカで更新処理が実行されていることを意味します。 `Seconds_Behind_Master` が "0" であることを確認したら、安全にレプリケーションを停止できます。

6. [レプリケーションを停止](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server)して、読み取りレプリカをプライマリに昇格します。

7. サーバー 5.7 を実行している新しいプライマリ (以前のレプリカ) にアプリケーションをポイントします。 各サーバーには一意の接続文字列があります。 ソースではなく、(以前の) レプリカを指すようにアプリケーションを更新します。

> [!Note]
> このシナリオでダウンタイムが発生するのは、手順 4、5、6 の間だけです。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>アップグレードが必要な MySQL v5.6 を運用環境で使用しているのですが、このアップグレード機能が GA になるのは、いつですか?

この機能は、MySQL v5.6 の提供終了前に GA となる予定です。 ただし、この機能は、実稼働可能であり、Azure で完全にサポートされているため、安心して環境で実行してください。 推奨されるベスト プラクティスとして、最初に、サーバーの復元済みコピーで実行し、テストすることを強くお勧めします。これにより、アップグレード中のダウンタイムを予測し、運用環境での実行前にアプリケーションの互換性テストを実行することができます。 詳細については、[ポイントインタイム リストアを実行する方法](howto-restore-server-portal.md#point-in-time-restore)を参照して、特定の時点のサーバーのコピーを作成してください。 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>これによってサーバーのダウンタイムは発生しますか? その場合、長さはどのくらいですか?

はい、アップグレード プロセス中はサーバーを使用できなくなります。ですから、この操作は、計画メンテナンス期間中に実行することをお勧めします。 推定ダウンタイムは、データベースのサイズ、プロビジョニングされたストレージのサイズ (プロビジョニングされた IOPS)、およびデータベース上のテーブルの数によって異なります。 アップグレード時間は、サーバー上のテーブルの数に直接比例します。Basic SKU サーバーは、標準ストレージ プラットフォームにあるため、アップグレード時間が長くなることが予想されます。 サーバー環境のダウンタイムを推定するために、最初にサーバーの復元済みコピーにアップグレードを実行することをお勧めします。 [読み取りレプリカを使用して、MySQL 5.6 から MySQL 5.7 へのメジャー バージョンのアップグレードを最小限のダウンタイムで実行する](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)ことを検討してください。

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>2021 年 2 月 5 日までに MySQL v5.6 サーバーをアップグレードすることを選択しなかった場合はどうなりますか?

引き続き、MySQL v5.6 サーバーを実行できます。 Azure **では、サーバーに強制アップグレードが実行されることはありません**。 ただし、[Azure Database for MySQL のバージョン管理ポリシー](concepts-version-policy.md)に記載されている制限が適用されます。

## <a name="next-steps"></a>次のステップ

[Azure Database for MySQL バージョン管理ポリシー](concepts-version-policy.md)について説明します。