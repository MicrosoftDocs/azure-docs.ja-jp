---
title: チュートリアル:ポータルの geo レプリケーションとフェールオーバー
description: Azure portal を使用してデータベースの geo レプリケーションを構成してフェールオーバーを開始します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673269"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>チュートリアル:Azure portal でアクティブ geo レプリケーションとフェールオーバーを構成する (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、[Azure portal](https://portal.azure.com) を使用して Azure SQL Database の[アクティブ geo レプリケーション](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)を構成し、フェールオーバーを開始する方法について説明します。

自動フェールオーバー グループを利用したベスト プラクティスについては、[Azure SQL Database のベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-database)と[Azure SQL Managed Instance のベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)に関するセクションを参照してください。 



## <a name="prerequisites"></a>前提条件

Azure Portal を使ってアクティブ geo レプリケーションを構成するには、次のリソースが必要です。

* Azure SQL Database 内のデータベース:別の地理的リージョンにレプリケートするプライマリ データベースです。

> [!Note]
> Azure portal を使用する場合は、プライマリと同じサブスクリプション内にのみセカンダリ データベースを作成できます。 別のサブスクリプション内にセカンダリ データベースが必要な場合、[データベースの作成の REST API](/rest/api/sql/databases/createorupdate) または [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql) を使用します。

## <a name="add-a-secondary-database"></a>セカンダリ データベースの追加

次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。  

セカンダリ データベースを追加するには、サブスクリプションの所有者または共同所有者でなければなりません。

セカンダリ データベースは、プライマリ データベースと同じ名前となります。また、既定でのサービス レベルおよびコンピューティング サイズはプライマリ データベースと同じになります。 セカンダリ データベースは、単一データベースまたはプールされたデータベースとすることができます。 詳細については、[DTU ベースの購入モデル](service-tiers-dtu.md)と[仮想コアベースの購入モデル](service-tiers-vcore.md)に関する記事を参照してください。
セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。

> [!NOTE]
> パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーションについてセットアップするデータベースを参照します。
2. SQL Database ページで、 **[geo レプリケーション]** を選択し、セカンダリ データベースを作成するリージョンを選択します。 プライマリ データベースをホストしているリージョンでなければどのリージョンを選択してもかまいませんが、[ペア リージョン](../../best-practices-availability-paired-regions.md)を選択することをお勧めします。

    ![geo レプリケーションの構成](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. サーバーと、セカンダリ データベースの価格レベルを選択または構成します。

    ![セカンダリ フォームの作成](./media/active-geo-replication-configure-portal/create-secondary.png)
4. 必要に応じて、以下のようにセカンダリ データベースをエラスティック プールに追加できます。 プールにセカンダリ データベースを作成するには、 **[エラスティック プール]** をクリックし、ターゲット サーバー上でプールを選択します。 プールは、ターゲット サーバーに既に存在する必要があります。 このワークフローでは、プールを作成できません。
5. **[作成]** をクリックして、セカンダリ データベースを追加します。
6. セカンダリ データベースが作成され、シード処理が始まります。

    ![セカンダリ マップ](./media/active-geo-replication-configure-portal/seeding0.png)
7. シード処理が完了すると、セカンダリ データベースの状態が表示されます。

    ![シード処理の完了](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>フェールオーバーの開始

セカンダリ データベースは、プライマリ データベースとして使用するように切り替えることができます。  

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. [SQL Database] ブレードで、 **[すべての設定]**  >  **[geo レプリケーション]** の順に選択します。
3. **[セカンダリ]** ボックスの一覧で、新しいプライマリとして使用するデータベースを選択し、 **[強制フェールオーバー]** をクリックします。

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. **[はい]** をクリックして、フェールオーバーを開始します。

このコマンドは、セカンダリ データベースをプライマリ ロールに即座に切り替えます。 このプロセスは、通常 30 秒以内に完了します。

ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。 プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。 通常の状況では、操作全体が完了するのに 1 分かかりません。

> [!NOTE]
> このコマンドは、障害発生時にデータベースを迅速に復旧するために設計されています。 データを同期せずに、フェールオーバーがトリガーされます (強制フェールオーバー)。  コマンドが発行されたときに、プライマリがオンラインでトランザクションをコミット中の場合、一部のデータが失われる可能性があります。

## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する

この操作では、セカンダリ データベースへのレプリケーションを完全に終了し、セカンダリ データベースのロールを通常の読み取り/書き込みデータベースに変更します。 セカンダリ データベースへの接続が切断された場合、コマンドは成功しますが、接続が復元するまでセカンダリ データベースは読み取り/書き込み状態になりません。  

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. SQL Database ページで、 **[geo レプリケーション]** を選択します。
3. **[セカンダリ]** ボックスの一覧で、geo レプリケーション パートナーシップから削除するデータベースを選択します。
4. **[レプリケーションを停止する]** をクリックします。

    ![セカンダリ データベースの削除](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. 確認ウィンドウが開きます。 **[はい]** をクリックして geo レプリケーション パートナーシップからデータベースを削除します。 (データベースを読み取り/書き込みデータベースに設定することは、レプリケーションの一部ではありません)。

## <a name="next-steps"></a>次のステップ

* アクティブ geo レプリケーションについて詳しくは、[アクティブ geo レプリケーション](active-geo-replication-overview.md)に関するページを参照してください。
* 自動フェールオーバー グループについては、[自動フェールオーバー グループ](auto-failover-group-overview.md)に関するページを参照してください
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事を参照してください。