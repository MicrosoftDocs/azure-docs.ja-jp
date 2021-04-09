---
title: Azure AD Connect:LocalDB の 10 GB の制限の問題から回復する方法 | Microsoft Docs
description: このトピックでは、LocalDB の 10 GB 制限の問題が発生したときに Azure AD Connect Synchronization Service を回復する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e10aa5d96722b414d7384ceb81f393575d57e2a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688775"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect:LocalDB の 10 GB 制限からの回復方法
Azure AD Connect には、ID データを格納する SQL Server データベースが必要です。 Azure AD Connect と共にインストールされる既定の SQL Server 2012 Express LocalDB を使用するか、所有している完全バージョンの SQL を使用することができます。 SQL Server Express には、10 GB のサイズ制限があります。 LocalDB を使用していて、この上限に達すると、Azure AD Connect Synchronization Service は正常に開始または同期できなくなります。 この記事では、回復の手順を説明します。

## <a name="symptoms"></a>現象
一般的には、次の 2 つの症状があります。

* Azure AD Connect Synchronization Service が **実行中** でも、 *"stopped-database-disk-full"* エラーで同期が失敗する。

* Azure AD Connect Synchronization Service が **開始できない**。 このサービスを開始しようとすると、イベント 6323 で失敗し、"*SQL Server のディスク領域が不足しているため、サーバーでエラーが発生しました。* " というエラー メッセージが表示されます。

## <a name="short-term-recovery-steps"></a>短期的な回復手順
このセクションでは、Azure AD Connect Synchronization Service が操作を再開するために必要な DB 空き領域を増やす手順について説明します。 これには次の手順が含まれます。
1. [Synchronization Service の状態を確認する](#determine-the-synchronization-service-status)
2. [データベースを縮小する](#shrink-the-database)
3. [実行履歴データを削除する](#delete-run-history-data)
4. [実行履歴データの保有期間を短縮する](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Synchronization Service の状態を確認する
まず、Synchronization Service がまだ実行中かどうかを確認します。

1. 管理者として Azure AD Connect サーバーにログインします。

2. **[サービス コントロール マネージャー]** に移動します。

3. **Microsoft Azure AD Sync** の状態を確認します。


4. 実行されている場合は、サービスを停止または再起動しないでください。 「[データベースを縮小する](#shrink-the-database)」の手順をスキップし、「[実行履歴データを削除する](#delete-run-history-data)」の手順に進みます。

5. 実行されていない場合は、サービスを開始してみてください。 サービスが正常に開始した場合は、「[データベースを縮小する](#shrink-the-database)」の手順をスキップし、「[実行履歴データを削除する](#delete-run-history-data)」の手順に進みます。 そうでない場合は、「[データベースを縮小する](#shrink-the-database)」の手順に進みます。

### <a name="shrink-the-database"></a>データベースを縮小する
縮小操作を使用して、Synchronization Service を開始するための十分な DB 空き領域を確保します。 データベース内の空白を削除することによって、DB 空き領域が確保されます。 必ず領域を回復できる保証はないため、この手順はベスト エフォートです。 縮小操作の詳細については、「[データベースの圧縮](/sql/relational-databases/databases/shrink-a-database)」という記事を参照してください。

> [!IMPORTANT]
> Synchronization Service を実行できる場合は、この手順をスキップしてください。 SQL DB の縮小は、断片化の増加によってパフォーマンスが低下する可能性があるため、お勧めしません。

Azure AD Connect 用に作成されるデータベースの名前は、**ADSync** です。 縮小操作を実行するには、sysadmin またはデータベースの DBO としてログインする必要があります。 Azure AD Connect のインストール中に、以下のアカウントに sysadmin 権限が付与されます。
* ローカル管理者
* Azure AD Connect のインストールを実行するために使用されたユーザー アカウント。
* Azure AD Connect Synchronization Service の操作コンテキストとして使用される Sync Service アカウント。
* インストール中に作成されたローカル グループ ADSyncAdmins。

1. `%ProgramFiles%\Microsoft Azure AD Sync\Data` にある **ADSync.mdf** ファイルと **ADSync_log.ldf** ファイルを安全な場所にコピーして、データベースをバックアップします。

2. 新しい PowerShell セッションを開始します。

3. `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` フォルダーに移動します。

4. sysadmin またはデータベースの DBO の資格情報を使用してコマンド `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` を実行することで、**sqlcmd** ユーティリティを起動します。

5. データベースを縮小するには、sqlcmd プロンプト (`1>`) で「`DBCC Shrinkdatabase(ADSync,1);`」と入力し、次の行に「`GO`」と入力します。

6. 操作が成功した場合は、Synchronization Service をもう一度開始してみます。 Synchronization Service を開始できる場合は、「[実行履歴データを削除する](#delete-run-history-data)」の手順に進みます。 そうでない場合は、サポートに問い合わせてください。

### <a name="delete-run-history-data"></a>実行履歴データを削除する
既定では、Azure AD Connect は最大 7 日間分の実行履歴データを保持します。 この手順では、Azure AD Connect Synchronization Service が再度同期を開始できるように、実行履歴データを削除して DB 空き領域を増やします。

1. [スタート]、[Synchronization Service] の順に移動して、**Synchronization Service Manager** を起動します。

2. **[操作]** タブに移動します。

3. **[アクション]** で **[Clear Runs (実行のクリア)]** コマンドを選択します。

4. **[Clear all runs (すべての実行をクリア)]** または **[Clear runs before… (以下より前の実行をクリア)]\<date>** オプションを選択できます。 まずは 2 日を経過した実行履歴データをクリアすることをお勧めします。 DB サイズの問題が引き続き発生する場合は、 **[Clear all runs (すべての実行をクリア)]** オプションを選択してください。

### <a name="shorten-retention-period-for-run-history-data"></a>実行履歴データの保有期間を短縮する
この手順は、複数の同期サイクル後に 10 GB 制限の問題が発生する可能性を低減するためのものです。

1. 新しい PowerShell セッションを開きます。

2. `Get-ADSyncScheduler` を実行し、現在の保有期間を指定している PurgeRunHistoryInterval プロパティを書き留めておきます。

3. `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` を実行して、保有期間を 2 日間に設定します。 必要に応じて保有期間を調整します。

## <a name="long-term-solution--migrate-to-full-sql"></a>長期的な解決策 – 完全バージョンの SQL への移行
一般に、この問題は、Azure AD Connect がオンプレミスの Active Directory を Azure AD に同期するのに 10 GB というデータベース サイズが十分でなくなったことを示しています。 SQL Server の完全バージョンを使用するよう切り替えることをお勧めします。 既存の Azure AD Connect デプロイの LocalDB を、SQL の完全バージョンのデータベースに直接置き換えることはできません。 代わりに、SQL の完全バージョンを搭載した新しい Azure AD Connect サーバーをデプロイする必要があります。 スウィング移行を実行することをお勧めします。スウィング移行では、新しい Azure AD Connect サーバー (SQL DB を含む) がステージング サーバーとして、既存の Azure AD Connect サーバー (LocalDB を含む) の隣にデプロイされます。 
* Azure AD Connect でリモート SQL を構成する方法の手順については、「[Azure AD Connect のカスタム インストール](./how-to-connect-install-custom.md)」を参照してください。
* Azure AD Connect アップグレードのスウィング移行の手順については、「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](./how-to-upgrade-previous-version.md#swing-migration)」を参照してください。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
