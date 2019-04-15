---
title: App Service on Azure Stack Update 5 のリリース ノート | Microsoft Docs
description: App Service on Azure Stack Update 5 の内容、既知の問題、更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 851673de4a711f5eb64228233ea7703ef86bfa7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500204"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>App Service on Azure Stack update 5 のリリース ノート

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、Azure App Service on Azure Stack Update 5 における機能強化と修正点、および既知の問題について説明します。 既知の問題は、デプロイおよび更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]
> Azure App Service 1.5 をデプロイする前に、Azure Stack 統合システムに 1901 更新プログラムを適用するか、最新の Azure Stack Development Kit をデプロイします。


## <a name="build-reference"></a>ビルドのリファレンス

App Service on Azure Stack Update 5 のビルド番号は **80.0.2.15** です。

### <a name="prerequisites"></a>前提条件

デプロイを開始する前に、[前提条件に関するドキュメント](azure-stack-app-service-before-you-get-started.md)をご覧ください。

Azure App Service on Azure Stack の 1.5 へのアップグレードを開始する前に:

- Azure Stack 管理ポータルにおいて Azure App Service の [管理] ですべてのロールが [準備完了] になっていることを確認します。

- App Service とマスター データベースをバックアップします。
  - AppService_Hosting
  - AppService_Metering
  - Master

- テナント アプリのコンテンツ ファイル共有をバックアップします。

- Marketplace から**カスタム スクリプト拡張機能**バージョン **1.9.1** を配信します。

### <a name="new-features-and-fixes"></a>新機能と修正

Azure App Service on Azure Stack Update 5 には、次の機能強化と修正が含まれています。

- **App Service のテナント ポータル、管理ポータル、Functions ポータル、Kudu ツール**の更新。 Azure Stack Portal SDK バージョンと一致しています。

- **Azure Functions ランタイム**が **v1.0.12205** に更新されました。

- **切断された** Azure Stack をお客様が操作する場合のスタイル設定と機能に関する問題を解決する、**Kudu ツール**の更新。 

- 信頼性を高めるためのコア サービスと、一般的な問題を簡単に診断できるようにするエラー メッセージの更新。

- **次のアプリケーション フレームワークとツールの更新**:
  - ASP.NET Core 2.1.6 および 2.2.0 を追加
  - NodeJS 10.14.1 を追加
  - NPM 6.4.1 を追加
  - Kudu を 79.20129.3767 に更新
  
- **すべてのロールの基になっているオペレーティング システムの更新プログラム**:
  - [x64 ベース システム用 Windows Server 2016 に対する 2019-02 累積的更新プログラム (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>デプロイ後の手順

> [!IMPORTANT]  
> SQL Always On インスタンスを使用して App Service RP を提供している場合は、データベースのフェールオーバー時にサービスの停止を防ぐために、[appservice_hosting と appservice_metering データベースを可用性グループに追加](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)し、それらのデータベースを同期する必要があります。

### <a name="post-update-steps"></a>更新後の手順

既存の Azure App Service on Azure Stack デプロイに関して包含データベースへの移行を検討されている場合は、Azure App Service on Azure Stack 1.5 への更新が完了した後で、以下の手順を実行してください。

> [!IMPORTANT]
> 移行手順の所要時間は 5 分から 10 分程度です。  この手順には、既存のデータベース ログイン セッションを中止する作業が伴います。  Azure App Service on Azure Stack の移行とその後の検証に伴うダウンタイムを考慮してください。  Azure App Service on Azure Stack 1.3 に更新した後でこれらの手順を実行した場合、これらの手順は必要ありません。
>
>

1. [AppService データベース (appservice_hosting and appservice_metering) を可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)に追加します

1. 包含データベースを有効にします。
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. データベースを部分的包含に変換すると、変換の際にすべてのアクティブなセッションを停止する必要があるため、ダウンタイムが引き起こされます。

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

検証

1. SQL Server で包含が有効になっているかどうかを確認します。

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 既存の包含の動作を確認します。
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

- App Service が既存の仮想ネットワークにデプロイされ、ファイル サーバーがプライベート ネットワークでしか使用できない場合、worker はファイル サーバーに到達することができません。Azure App Service on Azure Stack のデプロイ ドキュメントで説明されているとおりです。

ファイル サーバーに接続するために既存の仮想ネットワークと内部 IP アドレスへデプロイする場合は、送信セキュリティ規則を追加して、worker サブネットとファイル サーバー間の SMB トラフィックを有効にする必要があります。 管理者ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。
 * ソース:任意
 * 送信元ポート範囲: *
 * 変換先:IP アドレス
 * 宛先 IP アドレス範囲:ファイル サーバーの IP の範囲
 * 送信先ポート範囲:445
 * プロトコル:TCP
 * アクション:ALLOW
 * 優先順位:700
 * 名前:Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を運用するクラウド管理者に関する既知の問題

[Azure Stack 1809 リリース ノート](azure-stack-update-1809.md)内のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure App Service の概要については、「[Azure Stack 上の App Service の概要](azure-stack-app-service-overview.md)」をご覧ください。
- App Service on Azure Stack のデプロイの準備をする方法の詳細については、「[App Service on Azure Stack を開始する前に](azure-stack-app-service-before-you-get-started.md)」をご覧ください。
