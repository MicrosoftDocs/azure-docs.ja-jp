---
title: "最大で 10 年間 Azure SQL Database のバックアップを格納する | Microsoft Docs"
description: "Azure SQL Database が最大で 10 年間バックアップの格納をサポートする方法について説明します。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 25e651203f804fbf32d632b5f83145a3f3f72a7f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>最大で 10 年間 Azure SQL Database のバックアップを格納する
多くのアプリケーションで、規制、コンプライアンス、またはその他のビジネス上の目的で、Azure SQL Database の[自動バックアップ](sql-database-automated-backups.md)によって提供される 7 ～ 35 日間を超えて、データベースのバックアップを保持する必要があります。 長期のバックアップ リテンション期間機能を使用して、最大で 10 年間 SQL Database のバックアップを Azure Recovery Services コンテナーに保存できます。 コンテナーあたり最大 1000 個のデータベースを格納できます。 新しいデータベースとして復元する任意のバックアップを資格情報コンテナーで選択できます。

> [!IMPORTANT]
> 長期のバックアップ リテンション期間は現在プレビュー段階にあり、利用可能なリージョンは、オーストラリア東部、オーストラリア南東部、ブラジル南部、米国中部、東アジア、米国東部、米国東部 2、インド中部、インド南部、東日本、西日本、米国中北部、北ヨーロッパ、米国中南部、東南アジア、西ヨーロッパ、および米国西部です。
>

> [!NOTE]
> 資格情報コンテナーあたり 24 時間で最大 200 個のデータベースを有効にできます。 この制限の影響を最小限に抑えるため、サーバーごとに個別のコンテナーを使用することをお勧めします。 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>SQL Database の長期のバックアップ リテンション期間のしくみ

長期のバックアップ リテンション期間により、SQL Database サーバーを Azure Recovery Services コンテナーに関連付けることができます。 

* コンテナーは、SQL Server を作成した Azure サブスクリプションで、および同じ地理的リージョンとリソース グループで作成する必要があります。 
* その後、任意のデータベースの保持ポリシーを構成します。 このポリシーにより、週単位の完全データベース バックアップが Recovery Services コンテナーにコピーされ、指定された保有期間 (最大で 10 年間) 保持されます。 
* これらのバックアップのいずれかから、サブスクリプション内のあらゆるサーバーの新しいデータベースに、データベースを復元することができます。 Azure ストレージは、既存のバックアップからコピーを作成するため、コピーが既存のデータベースのパフォーマンスに影響を与えることはありません。

> [!TIP]
> ハウツー ガイドについては、「[Azure SQL Database を構成して長期のバックアップ リテンション期間から復元する](sql-database-long-term-backup-retention-configure.md)」を参照してください。

## <a name="enable-long-term-backup-retention"></a>長期のバックアップ リテンション期間の有効化

データベースのバックアップの長期保存を構成するには、次の手順を実行します。

1. SQL Database サーバーと同じリージョン、サブスクリプション、およびリソース グループで Azure Recovery Services コンテナーを作成します。 
2. サーバーをコンテナーに登録します。
3. Azure Recovery Services の保護ポリシーを作成します。
4. 長期のバックアップ リテンション期間を必要とするデータベースに、保護ポリシーを適用します。

Azure Recovery Services コンテナー内の自動バックアップの長期のバックアップ リテンション期間からデータベースを構成、管理、および復元するには、以下のいずれかを実行します。

* Azure Portal を使用: **[Long-term backup retention]** (長期のバックアップ リテンション期間) をクリックし、データベースを選択して、**[構成]** をクリックします。 

   ![長期のバックアップ リテンション期間の対象となるデータベースの選択](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* PowerShell を使用: 「[Azure SQL Database を構成して長期のバックアップ リテンション期間から復元する](sql-database-long-term-backup-retention-configure.md)」を参照してください。

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>長期のバックアップ リテンション期間機能で保存されたデータベースを復元する

長期的なバックアップ保有期間のバックアップから回復するには、次のようにします。

1. バックアップが格納されているコンテナーを一覧表示します。
2. 論理サーバーにマップされるコンテナーを一覧表示します。
3. データベースにマップされているコンテナー内のデータ ソースを一覧表示します。
4. 復元に使用できる復旧ポイントを一覧表示します。
5. サブスクリプション内で、回復ポイントからターゲット サーバーにデータベースを復元します。

Azure Recovery Services コンテナー内の自動バックアップの長期のバックアップ リテンション期間からデータベースを構成、管理、および復元するには、以下のいずれかを実行します。

* Azure Portal を使用: [Azure Portal を使用して長期のバックアップ リテンション期間を管理する](sql-database-long-term-backup-retention-configure.md)方法に関するページをご覧ください。 

* PowerShell を使用: [PowerShell を使用して長期のバックアップ リテンション期間を管理する](sql-database-long-term-backup-retention-configure.md)方法に関するページをご覧ください。

## <a name="get-pricing-for-long-term-backup-retention"></a>長期のバックアップ リテンション期間の価格の取得

SQL Database の長期のバックアップ リテンション期間は、[Azure バックアップ サービスの価格レート](https://azure.microsoft.com/pricing/details/backup/)に従って課金されます。

SQL Database サーバーをコンテナーに登録した後は、コンテナーに格納されている週単位のバックアップで使用されている合計ストレージに対して課金されます。

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>長期のバックアップ リテンション期間で保存されている、利用可能なバックアップを表示する

Azure Portal を使用して、Azure Recovery Services コンテナー内の自動バックアップの長期のバックアップ リテンション期間を構成したり、管理したり、それから復元したりするには、次のいずれかを実行してください。

* Azure Portal を使用: [Azure Portal を使用して長期のバックアップ リテンション期間を管理する](sql-database-long-term-backup-retention-configure.md)方法に関するページをご覧ください。 

* PowerShell を使用: [PowerShell を使用して長期のバックアップ リテンション期間を管理する](sql-database-long-term-backup-retention-configure.md)方法に関するページをご覧ください。

## <a name="disable-long-term-retention"></a>長期保存の無効化

Recovery Service は、指定した保持ポリシーに基づいてバックアップのクリーンアップを自動的に処理します。 

特定のデータベースのバックアップの資格情報コンテナーへの送信を停止するには、そのデータベースの保持ポリシーを削除します。
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> コンテナーに既に存在するバックアップは、影響を受けません。 保有期間を超過すると、これらのデータベースはRecovery Service によって自動的に削除されます。

## <a name="long-term-backup-retention-faq"></a>長期のバックアップ リテンション期間の FAQ

**資格情報コンテナー内の特定のバックアップを手動で削除することはできますか。**

現時点では連携しません。 資格情報コンテナーは、リテンション期間を超過したバックアップを自動的にクリーンアップします。

**複数の資格情報コンテナーにバックアップを保存するためにサーバーを登録することはできますか。**

いいえ。現在一度にバックアップを格納できるのは 1 つの資格情報コンテナーのみです。

**資格情報コンテナーとサーバーを異なるサブスクリプションに配置することはできますか。**

いいえ。現時点では、資格情報コンテナーとサーバーは同じサブスクリプションとリソース グループに配置する必要があります。

**サーバーのリージョンとは異なるリージョンで作成した資格情報コンテナーを使用することはできますか。**

いいえ。コピーにかかる時間を最小限にしてトラフィック料金が発生しないようにするため、資格情報コンテナーとサーバーのリージョンは同じである必要があります。

**1 つの資格情報コンテナーにはいくつのデータベースを格納できますか。**

現時点では、資格情報コンテナーあたり最大 1000 個のデータベースがサポートされています。 

**サブスクリプションごとに作成できる資格情報コンテナーはいくつですか。**

サブスクリプションあたり最大 25 個のコンテナーを作成できます。

**資格情報コンテナーごとに、1 日あたりいくつのデータベースを構成できますか。**

資格情報コンテナーごとに設定できるデータベースは 1 日あたり 200 個です。

**長期のバックアップ リテンション期間は、エラスティック プールと連携しますか。**

はい。 プール内のどのデータベースも保持ポリシーで構成できます。

**バックアップが作成される時刻を選択できますか。**

いいえ。SQL Database は、データベースのパフォーマンスに与える影響を最小限に抑えるために、バックアップ スケジュールを制御します。

**データベースの Transparent Data Encryption を有効にしています。資格情報コンテナーでそれを使用できますか。** 

はい。Transparent Data Encryption はサポートされています。 元のデータベースが存在しない場合でも、資格情報コンテナーからデータベースを復元できます。

**サブスクリプションを中断した場合、資格情報コンテナー内のバックアップはどうなりますか。** 

サブスクリプションが中断されている場合、既存のデータベースとバックアップは保持されます。 新しいバックアップは資格情報コンテナーにコピーされません。 サブスクリプションを再有効化すると、資格情報コンテナーへのバックアップのコピーが再開されます。 資格情報コンテナーは、サブスクリプションが中断される前に資格情報コンテナーにコピーされていたバックアップを使用して、復元操作でアクセス可能になります。 

**SQL Server からダウンロード、または SQL Server に復元するため、SQL Database のバックアップ ファイルにアクセスすることはできますか。**

いいえ。現時点ではできません。

**SQL のリテンション期間ポリシー内で複数のスケジュール (日単位、週単位、月単位、年単位) を設定することはできますか。**

いいえ、複数のスケジュールは、現在、仮想マシンのバックアップにのみ使用できます。

**アクティブ geo レプリケーションのセカンダリ データベースであるデータベースに対して、長期のバックアップ リテンション期間を設定するとどうなりますか。**

レプリカのバックアップは提供していないため、現在セカンダリ データベースに対して長期のバックアップ リテンション期間を適用するオプションはありません。 ただし、以下の理由から、アクティブ geo レプリケーションのセカンダリ データベースに対しては、ユーザー自身で長期のバックアップ リテンション期間を設定するようお願いします。
* フェールオーバーが発生してデータベースがプライマリ データベースになった場合、完全バックアップが実行され、それがコンテナーにアップロードされます。
* セカンダリ データベースに対して長期のバックアップ リテンション期間を設定しても、お客様への追加料金は発生しません。

## <a name="next-steps"></a>次のステップ
データベース バックアップは、データの不慮の破損または削除から保護するため、ビジネス継続性および障害復旧戦略の最も重要な部分です。 その他の SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。

