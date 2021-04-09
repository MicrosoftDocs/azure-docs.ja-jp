---
title: SQL データ同期用のデータ同期エージェント
description: Azure に SQL データ同期のデータ同期エージェントをインストールして実行し、SQL Server データベースとデータを同期する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791462"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>SQL データ同期用のデータ同期エージェント
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure に SQL データ同期のデータ同期エージェントをインストールして構成することによって、SQL Server データベースとデータを同期します。 SQL データ同期の詳細については、「[複数のクラウドおよびオンプレミス データベースにわたるデータを SQL データ同期で同期します](sql-data-sync-data-sql-server-sql-database.md)」を参照してください。

> [!IMPORTANT]
> 現時点では、SQL データ同期で Azure SQL Managed Instance はサポートされて **いません**。

## <a name="download-and-install"></a>ダウンロードしてインストールする

データ同期エージェントをダウンロードするには、[SQL データ同期エージェント](https://www.microsoft.com/download/details.aspx?id=27693)のページに移動します。 データ同期エージェントをアップグレードするには、古いエージェントと同じ場所にエージェントをインストールすると、元のエージェントがオーバーライドされます。

### <a name="install-silently"></a>サイレント インストール

データ同期エージェントをコマンド プロンプトからサイレント インストールするには、次の例のようなコマンドを入力します。 ダウンロードした .msi ファイルのファイル名を確認し、**TARGETDIR** および **SERVICEACCOUNT** 引数に独自の値を指定します。

- **TARGETDIR** の値を指定しない場合、既定値は `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0` です。

- **SERVICEACCOUNT** の値として `LocalSystem` を指定した場合は、SQL Server に接続するエージェントを構成するときに、SQL Server 認証を使用します。

- **SERVICEACCOUNT** の値としてドメイン ユーザー アカウントまたはローカル ユーザー アカウントを指定した場合は、**SERVICEPASSWORD** 引数を使用してパスワードを指定する必要もあります。 たとえば、「 `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"` 」のように入力します。

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>SQL Server データベースとのデータの同期

1 つ以上の SQL Server データベースとデータを同期できるよう、データ同期エージェントを構成するには、[SQL Server データベースの追加](sql-data-sync-sql-server-configure.md#add-on-prem)に関するページを参照してください。

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>データ同期エージェントの FAQ

### <a name="why-do-i-need-a-client-agent"></a>なぜクライアント エージェントが必要ですか

SQL データ同期サービスは、クライアント エージェントを使って SQL Server データベースと通信します。 このセキュリティ機能では、ファイアウォールの背後にあるデータベースと直接通信できません。 SQL データ同期サービスは、エージェントと通信するとき、暗号化された接続と、一意のトークンつまり "*エージェント キー*" を使います。 SQL Server データベースは、接続文字列とエージェント キーを使ってエージェントを認証します。 この設計では、高レベルのセキュリティがデータに提供されます。

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>何個のローカル エージェント UI インスタンスを実行できますか

実行できる UI のインスタンスは 1 つだけです。

### <a name="how-can-i-change-my-service-account"></a>サービス アカウントを変更するにはどうすればよいですか

クライアント エージェントをインストールした後でサービス アカウントを変更する唯一の方法は、クライアント エージェントをアンインストールし、新しいサービス アカウントで新しいクライアント エージェントをインストールすることです。

### <a name="how-do-i-change-my-agent-key"></a>エージェント キーを変更するにはどうすればよいですか

エージェント キーは、エージェントによって一度だけ使われます。 エージェントを削除して新しいエージェントを再インストールするときに再利用したり、複数のエージェントで共用したりすることはできません。 既存のエージェントに対して新しいキーを作成する必要がある場合は、クライアント エージェントと SQL データ同期サービスで同じキーが記録されるようにする必要があります。

### <a name="how-do-i-retire-a-client-agent"></a>クライアント エージェントの使用を終了するにはどうすればよいですか

すぐにエージェントを無効にしたり、使用を終了したりするには、ポータルでエージェント キーの再生成だけを行い、エージェント UI でのキーの送信は行わないでおきます。 キーを再生成すると、対応するエージェントがオンラインかオフラインかにかかわらず、前のキーは無効になります。

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>別のコンピューターにクライアント エージェントを移動するにはどうすればよいですか

現在とは異なるコンピューターからローカル エージェントを実行する場合は、次のようにします。

1. 目的のコンピューターにエージェントをインストールします。
2. SQL データ同期ポータルにログインし、新しいエージェントのエージェント キーを再生成します。
3. 新しいエージェントの UI を使って、新しいエージェント キーを送信します。
4. 以前に登録されたオンプレミスのデータベースのリストをクライアント エージェントがダウンロードするまで待ちます。
5. 到達不能と表示されるすべてのデータベースに対し、データベースの資格情報を指定します。 これらのデータベースには、エージェントをインストールした新しいコンピューターから到達可能でなければなりません。

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> データ同期エージェントの問題のトラブルシューティング

- [クライアント エージェントのインストール、アンインストール、または修復に失敗する](#agent-install)

- [アンインストールを取り消した後、クライアント エージェントが動作しない](#agent-uninstall)

- [データベースがエージェントの一覧に表示されない](#agent-list)

- [クライアント エージェントが起動しない (エラー 1069)](#agent-start)

- [エージェント キーを送信できない](#agent-key)

- [関連付けられているオンプレミス データベースにアクセスできない場合、クライアント エージェントをポータルから削除できない](#agent-delete)

- [ローカル同期エージェント アプリでローカル同期サービスに接続できない](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> クライアント エージェントのインストール、アンインストール、または修復に失敗する

- **原因**。 このエラーはさまざまなシナリオで発生します。 このエラーの具体的な原因を特定するには、ログを調べます。

- **解決策**。 エラーの具体的な原因を特定するには、Windows インストーラー ログを生成し、内容を確認します。 ログ記録はコマンド プロンプトから有効にすることができます。 たとえば、ダウンロードしたインストール ファイルが `SQLDataSyncAgent-2.0-x86-ENU.msi` である場合は、次のコマンド ラインを使用してログ ファイルを生成し、内容を確認します。

  - インストールの場合: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - アンインストールの場合: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Windows インストーラーによって実行されるすべてのインストールについて、ログ記録を有効にすることもできます。 Microsoft サポート技術情報の記事「[Windows インストーラーのログの記録を有効にする方法](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)」には、Windows インストーラーのログの記録を有効にするワンクリック ソリューションが用意されています。 また、ログの場所も示されています。

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> アンインストールを取り消した後、クライアント エージェントが動作しない

クライアント エージェントのアンインストールを取り消した後でも、クライアント エージェントが動作しません。

- **原因**。 この原因は、SQL データ同期クライアント エージェントに資格情報が格納されないことです。

- **解決策**。 次の 2 つの解決策を試してください。

    -   services.msc を使用してクライアント エージェントの資格情報を再入力します。
    -   今のクライアント エージェントをアンインストールした後、新規にクライアント エージェントをインストールします。 [ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=27693)から最新のクライアント エージェントをダウンロードしてインストールします。

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> データベースがエージェントの一覧に表示されない

既存の SQL Server データベースを同期グループに追加しようとしたときに、対象のデータベースがエージェントの一覧に表示されません。

この問題は以下のシナリオで発生します。

- **原因**。 クライアント エージェントと同期グループが異なるデータセンターにある。

- **解決策**。 クライアント エージェントと同期グループは同じデータセンターに配置する必要があります。 この設定を行うには、2 つの方法があります。

    -   同期グループが配置されているデータセンターに新しいエージェントを作成します。 次に、そのエージェントにデータベースを登録します。
    -   現在の同期グループを削除します。 次に、エージェントが配置されているデータセンターで同期グループを再作成します。

- **原因**。 クライアント エージェントのデータベースの一覧が最新ではない。

- **解決策**。 クライアント エージェント サービスをいったん停止した後、再び開始します。

    関連付けられているデータベースの一覧をローカル エージェントがダウンロードするのは、エージェント キーの最初の送信時のみです。 それ以降のエージェント キーの送信時には、関連付けられているデータベースの一覧はダウンロードされません。 したがって、エージェントの移動中に登録されたデータベースは、元のエージェント インスタンスに表示されません。

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> クライアント エージェントが起動しない (エラー 1069)

SQL Server をホストしているコンピューターで、エージェントが実行されていませんでした。 エージェントを手動で起動しようとすると、"エラー 1069:ログオンに失敗したため、サービスを開始できませんでした。" というメッセージを示すダイアログ ボックスが表示されます。

![データ同期エラー 1069 のダイアログ ボックス](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **原因**。 このエラーの原因としては、エージェントを作成しエージェントのパスワードを指定した後でローカル サーバー上のパスワードが変更されたことが考えられます。

- **解決策**。 エージェントのパスワードを現在のサーバー パスワードに更新します。

  1. SQL データ同期クライアント エージェント サービスを見つけます。  
    a. **[スタート]** を選択します。  
    b. 検索ボックスに「**services.msc**」と入力します。  
    c. 検索結果で、 **[サービス]** を選択します。  
    d. **[サービス]** ウィンドウで、 **[SQL Data Sync Agent]\(SQL データ同期エージェント\)** のエントリまでスクロールします。  
  1. **[SQL Data Sync Agent]\(SQL データ同期エージェント\)** を右クリックして、 **[停止]** を選択します。
  1. **[SQL Data Sync Agent]\(SQL データ同期エージェント\)** を右クリックして、 **[プロパティ]** を選択します。
  1. **[SQL Data Sync Agent Properties]\(SQL データ同期エージェントのプロパティ\)** ウィンドウで、 **[ログイン]** タブを選択します。
  1. **[パスワード]** ボックスにパスワードを入力します。
  1. **[パスワードの確認]** ボックスにパスワードをもう一度入力します。
  1. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。
  1. **[サービス]** ウィンドウで、 **[SQL Data Sync Agent]\(SQL データ同期エージェント\)** サービスを右クリックし、 **[開始]** をクリックします。
  1. **[サービス]** ウィンドウを閉じます。

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> エージェント キーを送信できない

エージェントのキーを作成または再作成した後、そのキーを SqlAzureDataSyncAgent アプリケーションで送信しようとしました。 しかし、送信が完了しませんでした。

![同期エラー ダイアログ ボックス - エージェント キーを送信できない](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **前提条件**。 開始する前に、次の前提条件を確認しておく必要があります。

  - SQL データ同期 Windows サービスが実行されている。

  - SQL データ同期 Windows サービスのサービス アカウントがネットワークにアクセスできる。

  - 送信 1433 ポートがローカル ファイアウォール規則で開かれている。

  - ローカル IP アドレスが、同期メタデータ データベースのサーバーまたはデータベース ファイアウォール規則に追加されている。

- **原因**。 各ローカル エージェントは、エージェント キーによって一意に識別されます。 キーは次の 2 つの条件を満たす必要があります。

  -   SQL データ同期サーバーとローカル コンピューター上のクライアント エージェント キーが同一である必要があります。
  -   クライアント エージェント キーは 1 回だけ使用することができます。

- **解決策**。 エージェントが動作していない場合、これらの条件の 1 つまたは両方が満たされていないことが原因です。 再び動作するようにエージェントを設定するには、次の操作を実行します。

  1. 新しいキーを生成します。
  1. 新しいキーをエージェントに適用します。

  新しいキーをエージェントに適用するには、次の手順を実行します。

  1. エクスプローラーで、エージェントのインストール ディレクトリに移動します。 既定のインストール ディレクトリは、C:\\Program Files (x86)\\Microsoft SQL Data Sync です。
  1. bin サブディレクトリをダブルクリックします。
  1. SqlAzureDataSyncAgent アプリケーションを開きます。
  1. **[Submit Agent Key]\(エージェント キーの送信\)** を選択します。
  1. 表示されたスペースにクリップボードからキーを貼り付けます。
  1. **[OK]** を選択します。
  1. プログラムを閉じます。

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> 関連付けられているオンプレミス データベースにアクセスできない場合、クライアント エージェントをポータルから削除できない

SQL データ同期クライアント エージェントに登録されているローカル エンドポイント (つまり、データベース) にアクセスできない場合、クライアント エージェントを削除できません。

- **原因**。 アクセスできないデータベースがエージェントに登録されたままであるため、ローカル エージェントを削除することはできません。 エージェントを削除しようとすると、削除プロセスはデータベースにアクセスしようとしますが失敗します。

- **解決策**。 アクセスできないデータベースを削除するには、"強制削除" を使用します。

> [!NOTE]
> "強制削除" の後に同期メタデータ テーブルが残っている場合は、`deprovisioningutil.exe` を使用してクリーンアップします。

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> ローカル同期エージェント アプリでローカル同期サービスに接続できない

- **解決策**。 次の手順を試してみてください。

  1. アプリを終了します。  
  1. [コンポーネント サービス] パネルを開きます。  
    a. タスク バーの検索ボックスに「**services.msc**」と入力します。  
    b. 検索結果で、 **[サービス]** をダブルクリックします。  
  1. **SQL データ同期** サービスを停止します。
  1. **SQL データ同期** サービスを再起動します。  
  1. アプリをもう一度開きます。

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>コマンド プロンプトからのデータ同期エージェントの実行

コマンド プロンプトから、データ同期エージェントの次のコマンドを実行できます。

### <a name="ping-the-service"></a>サービスの ping

#### <a name="usage"></a>使用法

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>登録されたデータベースの表示

#### <a name="usage"></a>使用法

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>エージェント キーの送信

#### <a name="usage"></a>使用法

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>データベースの登録

#### <a name="usage"></a>使用法

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>データベースの登録解除

このコマンドを使用してデータベースの登録を解除すると、データベースが完全にプロビジョニング解除されます。 データベースが他の同期グループに参加している場合、この操作は他の同期グループを中断します。

#### <a name="usage"></a>使用法

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>資格情報の更新

#### <a name="usage"></a>使用法

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>例

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>次のステップ

SQL データ同期の詳細については、次の記事を参照してください。

-   概要 - [Azure の SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](sql-data-sync-data-sql-server-sql-database.md)
-   データ同期の設定
    - ポータル - [チュートリアル:Azure SQL Database と SQL Server の間でデータを同期するように SQL データ同期を設定する](sql-data-sync-sql-server-configure.md)
    - PowerShell の場合
        -  [PowerShell を使用して Azure SQL Database の複数データベース間で同期する](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell を使用して Azure SQL Database と SQL Server インスタンスのデータベース間で同期する](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   ベスト プラクティス - [Azure SQL データ同期のベスト プラクティス](sql-data-sync-best-practices.md)
-   監視 - [Azure Monitor ログによる SQL データ同期の監視](./monitor-tune-overview.md)
-   トラブルシューティング - [Azure SQL データ同期に関する問題のトラブルシューティング]sql-data-sync-troubleshoot.md)
-   同期スキーマの更新
    -   Transact-SQL の場合 - [Azure で SQL データ同期を使用してスキーマ変更のレプリケートを自動化する](sql-data-sync-update-sync-schema.md)
    -   PowerShell の場合 - [PowerShell を使用して、既存の同期グループの同期スキーマを更新する](scripts/update-sync-schema-in-sync-group.md)