---
title: "最大で 10 年間の Azure SQL Database のバックアップを格納する | Microsoft Docs"
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
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 768a630e1652a48fa4478ec2c25173d536ea6c09
ms.lasthandoff: 03/23/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>最大で 10 年間の Azure SQL Database のバックアップを格納する
多くのアプリケーションには規制、コンプライアンス、またはその他のビジネス上の目的があり、SQL Database の[自動バックアップ](sql-database-automated-backups.md)によって提供される、7 - 35 日間を経過した完全なデータベースの自動バックアップを保持する必要があります。 **バックアップの長期保存**機能では、最大で 10 年間の Azure SQL Database のバックアップを Azure Recovery Services の資格情報コンテナーに保存できます。 資格情報コンテナーごとに最大 1000 個のデータベースを格納できます。 新しいデータベースとして復元する任意のバックアップを資格情報コンテナーで選択できます。

> [!IMPORTANT]
> 長期のバックアップ リテンション期間は現在プレビュー段階にあり、利用可能なリージョンは、オーストラリア東部、オーストラリア南東部、ブラジル南部、米国中部、東アジア、米国東部、米国東部 2、インド中部、インド南部、東日本、西日本、米国中北部、北ヨーロッパ、米国中南部、東南アジア、西ヨーロッパ、および米国西部です。
>

> [!NOTE]
> 資格情報コンテナーあたり 24 時間で最大 200 個のデータベースを有効にできます。 したがって、この制限の影響を最小限に抑えるため、サーバーごとに個別の資格情報コンテナーを使用することをお勧めします。 
> 
> 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>SQL Database の長期的なバックアップ保有期間のしくみ

バックアップの長期的なバックアップ保有期間を使うと、Azure SQL Database サーバーを Azure Recovery Services コンテナーに関連付けることができます。 

* 資格情報コンテナーは、SQL Server を作成した Azure サブスクリプションで、同じ地理的領域およびリソース グループで作成する必要があります。 
* その後、任意のデータベースの保持ポリシーを構成します。 このポリシーにより、週単位の完全データベース バックアップが Recovery Services コンテナーにコピーされ、指定された保有期間 (最大で 10 年間) 保持されます。 
* サブスクリプション内のあらゆるサーバーの新しいデータベースに、これらのバックアップのいずれかから復元することができます。 コピーは、Azure ストレージによって既存のバックアップから実行され、既存のデータベースのパフォーマンスには影響を与えません。

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-portal.md)」、または「[PowerShell を使用したデータの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-powershell.md)」をご覧ください

## <a name="how-do-i-enable-long-term-backup-retention"></a>長期的なバックアップ保有期間を有効にする方法

データベースのバックアップの長期保存を構成するには、次の手順を実行します。

1. SQL Database サーバーと同じリージョン、サブスクリプション、およびリソース グループで Azure Recovery Services コンテナーを作成します。 
2. サーバーを資格情報コンテナーに登録します。
3. Azure Recovery Services の保護ポリシーを作成します。
4. 長期的なバックアップの保有を必要とするデータベースに、保護ポリシーを適用します。

Azure Portal を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[Azure Portal を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-portal.md)をご覧ください。 PowerShell を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[PowerShell を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-powershell.md)をご覧ください。

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>長期的なバックアップ保有期間機能で保存されたデータベースを復元する方法

長期的なバックアップ保有期間のバックアップから回復するには、次のようにします。

1. バックアップが格納されている資格情報コンテナーを一覧表示します
2. 論理サーバーにマップされる資格情報コンテナーを一覧表示します
3. データベースにマップされている資格情報コンテナー内のデータ ソースを一覧表示します
4. 復元に使用できる回復ポイントを一覧表示します
5. サブスクリプション内で、回復ポイントからターゲット サーバーに復元します

Azure Portal を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[Azure Portal を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-portal.md)をご覧ください。 PowerShell を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[PowerShell を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-powershell.md)をご覧ください。

## <a name="how-much-does-long-term-backup-retention-cost"></a>長期的なバックアップ保有期間のコスト

Azure SQL Database の長期的なバックアップ保有期間は、[Azure バックアップ サービスの価格レート](https://azure.microsoft.com/pricing/details/backup/)に従って課金されます。

Azure SQL Database サーバーを資格情報コンテナーに登録した後は、資格情報コンテナーに格納されている週単位のバックアップで使用されている合計ストレージに対して課金されます。

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>長期のバックアップ リテンション期間で保存されている、利用可能なバックアップを表示する

Azure Portal を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[Azure Portal を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-portal.md)をご覧ください。 PowerShell を使って、Azure Recovery Services コンテナーで自動バックアップの長期的なバックアップ保有期間を構成、管理、および復元する方法については、[PowerShell を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-powershell.md)をご覧ください。

## <a name="disabling-long-term-retention"></a>長期保存の無効化

Recovery Service は、指定した保持ポリシーに基づいてバックアップのクリーンアップを自動的に処理します。 

* 特定のデータベースのバックアップの資格情報コンテナーへの送信を停止するには、そのデータベースの保持ポリシーを削除します。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 既に資格情報コンテナーにあるバックアップは影響を受けません。 保有期間を超過すると、これらのデータベースはRecovery Service によって自動的に削除されます。


## <a name="removing-long-term-backup-retention-backups-from-the-azure-recovery-services-vault"></a>Azure Recovery Services コンテナーからの長期的なバックアップ保有期間バックアップの削除

長期的なバックアップ保有期間のバックアップをコンテナーから削除するには、「[長期保存のバックアップを削除する](sql-database-manage-long-term-backup-retention-powershell.md)」をご覧ください

## <a name="long-term-backup-retention-faq"></a>長期的なバックアップ保有期間の FAQ:

1. Q: 資格情報コンテナー内の特定のバックアップを手動で削除することはできますか。

    A: 現時点ではできません。資格情報コンテナーは、リテンション期間を超過したバックアップを自動的にクリーンアップします。
2. Q: 複数の資格情報コンテナーにバックアップを保存するためにサーバーを登録することはできますか。

    A: いいえ。現時点では、一度にバックアップを格納できるのは 1 つの資格情報コンテナーのみです。
3. Q: 資格情報コンテナーとサーバーを異なるサブスクリプションに配置することはできますか。

    A: いいえ。現時点では、資格情報コンテナーとサーバーは同じサブスクリプションとリソース グループに配置する必要があります。
4. Q: サーバーのリージョンとは異なるリージョンで作成した資格情報コンテナーを使用することはできますか。

    A: いいえ。コピーにかかる時間を最小限にしてトラフィック料金が発生しないようにするため、資格情報コンテナーとサーバーのリージョンは同じである必要があります。
5. Q: 1 つの資格情報コンテナーにはいくつのデータベースを格納できますか。

    A: 現時点では、資格情報コンテナーあたり最大 1000 個のデータベースがサポートされています。 
6. Q. サブスクリプションごとに作成できる資格情報コンテナーはいくつですか。

    A. サブスクリプションあたり最大 25 個のコンテナーを作成できます。
7. Q. 資格情報コンテナーごとに、1 日あたりいくつのデータベースを構成できますか。

    A. 資格情報コンテナーごとに 設定できるデータベースは 1 日あたり 200 個のみです。
8. Q: 長期バックアップ保存は、エラスティック プールと連携しますか。

    A: はい。 プール内のどのデータベースも保持ポリシーで構成できます。
9. Q: バックアップが作成される時刻を選択できますか。

    A: いいえ。SQL Database は、データベースのパフォーマンスに与える影響を最小限に抑えるために、バックアップ スケジュールを制御します。
10. Q: データベースに対して TDE が有効化されています。 コンテナーで TDE を使用できますか。 

    A. はい。TDE はサポートされています。 元のデータベースが存在しない場合でも、資格情報コンテナーからデータベースを復元できます。
11. Q. サブスクリプションを中断した場合、資格情報コンテナー内のバックアップはどうなりますか。 

    A. サブスクリプションが中断されている場合、既存のデータベースとバックアップは保持されます。 新しいバックアップは資格情報コンテナーにコピーされません。 サブスクリプションを再有効化すると、資格情報コンテナーへのバックアップのコピーが再開されます。 資格情報コンテナーは、サブスクリプションが中断される前に資格情報コンテナーにコピーされていたバックアップを使用して、復元操作でアクセス可能になります。 
12. Q: SQL Server からダウンロード、または SQL Server に復元するため、SQL Database のバックアップ ファイルにアクセスすることはできますか。

    A: 現時点ではできません。
13. Q: SQL のリテンション期間ポリシー内で複数のスケジュール (日単位、週単位、月単位、年単位) を設定することはできますか。

    A: いいえ。現時点では、これは仮想マシンのバックアップでのみ可能です。
14. Q. アクティブ geo レプリケーションのセカンダリであるデータベースに対して、長期のバックアップ リテンション期間を設定するとどうなりますか。

    A: 現在のところ、レプリカのバックアップは提供していません。そのため、セカンダリに対して長期のバックアップ リテンション期間を適用するオプションはありません。 ただし、以下の理由から、アクティブ geo レプリケーションのセカンダリに対しては、お客様自身で長期のバックアップ リテンション期間を設定するようお願いします。
    - フェールオーバーが発生してデータベースがプライマリになった場合、完全バックアップが実行され、その完全バックアップがコンテナーにアップロードされます。
    - セカンダリに対して長期のバックアップ リテンション期間を設定しても、お客様への追加料金は発生しません。



## <a name="next-steps"></a>次のステップ
データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。


