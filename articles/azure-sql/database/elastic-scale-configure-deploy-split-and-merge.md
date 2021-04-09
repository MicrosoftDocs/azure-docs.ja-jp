---
title: split-merge サービスのデプロイ
description: split-merge ツールを使用して、シャード化されたデータベース間でデータを移動します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792074"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>split-merge サービスをデプロイして、シャード化されたデータベース間でデータを移動する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

split-merge ツールを使用すると、シャード化されたデータベース間でデータを移動できます。 「 [スケールアウトされたクラウド データベース間のデータ移動](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>分割-結合パッケージのダウンロード

1. [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)から最新の NuGet バージョンをダウンロードします。

1. コマンド プロンプトを開き、nuget.exe をダウンロードしたディレクトリに移動します。 ダウンロードには、PowerShell コマンドが含まれています。

1. 次のコマンドで最新の Split-Merge パッケージを現在のディレクトリにダウンロードします。

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

ファイルは、**Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** という名前のディレクトリに配置されます。*x.x.xxx.x* はバージョン番号です。 **content\splitmerge\service** サブディレクトリに Split-Merge サービス ファイル、**content\splitmerge\powershell** サブディレクトリに Split-Merge PowerShell スクリプト (および必要なクライアント DLL) が格納されています。

## <a name="prerequisites"></a>前提条件

1. Split-Merge ステータス データベースとして使用する Azure SQL Database データベースを作成します。 [Azure ポータル](https://portal.azure.com)にアクセスします。 新しい **SQL Database** を作成します。 データベースに名前を付けて、新しい管理者とパスワードを作成します。 今後の使用のために、パスワードと名前を必ず記録しておいてください。

1. サーバーで Azure サービスからの接続が許可されていることを確認します。 ポータルの **[ファイアウォール設定]** で、 **[Azure サービスへのアクセスを許可する]** 設定が **[オン]** に設定されていることを確認してください。 [保存] アイコンをクリックします。

1. 診断の出力用の Azure Storage アカウントを作成します。

1. Split-Merge サービス用の Azure クラウド サービスを作成します。

## <a name="configure-your-split-merge-service"></a>Split-Merge サービスの構成

### <a name="split-merge-service-configuration"></a>Split-Merge サービスの構成

1. Split-Merge アセンブリをダウンロードしたフォルダーで、*SplitMergeService.cspkg* に付属の *ServiceConfiguration.Template.cscfg* ファイルのコピーを作成し、*ServiceConfiguration.cscfg* という名前に変更します。

1. 証明書の拇印の形式などの入力値を検証する Visual Studio などのテキスト エディターで、 *ServiceConfiguration.cscfg* を開きます。

1. 新しいデータベースを作成するか、または Split-Merge 操作用のステータス データベースとして使用する既存のデータベースを選択し、そのデータベースの接続文字列を取得します。

   > [!IMPORTANT]
   > 現時点では、状態データベースでラテン語の照合順序 (SQL\_Latin1\_General\_CP1\_CI\_AS) を使用する必要があります。 詳細については、「[Windows 照合順序名 (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql)」を参照してください。

   Azure SQL Database では、通常、接続文字列の形式は次のようになります。

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. ElasticScaleMetadata 設定の **SplitMergeWeb** ロールと **SplitMergeWorker** ロールの両方のセクションに *.cscfg* ファイルの接続文字列を入力します。

1. **SplitMergeWorker** ロールの場合は、**WorkerRoleSynchronizationStorageAccountConnectionString** 設定として Azure Storage への有効な接続文字列を入力します。

### <a name="configure-security"></a>セキュリティを構成する

サービスのセキュリティを構成する詳細な手順については、「 [Split-Merge セキュリティ構成](elastic-scale-split-merge-security-configuration.md)」を参照してください。

このチュートリアルの簡単なテスト デプロイのため、最小限の構成の手順セットを行ってサービスを起動および実行します。 以下の手順では、サービスを実行する 1 つのコンピューター/アカウントのみがサービスと通信できます。

### <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

新しいディレクトリを作成し、そのディレクトリから [[Visual Studio 開発者コマンド プロンプト]](/dotnet/framework/tools/developer-command-prompt-for-vs) ウィンドウを使用して次のコマンドを実行します。

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

秘密キーを保護するパスワードの入力を求められます。 強力なパスワードを入力し、確定します。 その後、パスワードをもう一度使用するよう求められます。 最後に **[はい]** をクリックして、信頼されたルート証明機関ストアにインポートします。

### <a name="create-a-pfx-file"></a>PFX ファイルの作成

makecert を実行した同じウィンドウから次のコマンドを実行します。証明書の作成に使用したものと同じパスワードを使用します。

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>個人用ストアへのクライアント証明書のインポート

1. Windows エクスプローラーで、 *MyCert.pfx* をダブルクリックします。
2. **証明書のインポート ウィザード** で **[現在のユーザー]** を選択し、 **[次へ]** をクリックします。
3. ファイルのパスを確認し、 **[次へ]** をクリックします。
4. パスワードを入力します。 **[すべての拡張プロパティを含める]** はオンのままにして **[次へ]** をクリックします。
5. **[自動的に証明書ストアを選択する]** をオンのままにして、 **[次へ]** をクリックします。
6. **[完了]** 、 **[OK]** の順にクリックします。

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>クラウド サービスへの PFX ファイルのアップロード

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. **[クラウド サービス]** を選択します。
3. 分割/結合サービス用に上で作成したクラウド サービスを選択します。
4. 上部メニューで **[証明書]** をクリックします。
5. 下部のバーで **[アップロード]** をクリックします。
6. PFX ファイルを選択し、前述と同じパスワードを入力します。
7. 完了したら、一覧内の新しいエントリから証明書の拇印をコピーします。

### <a name="update-the-service-configuration-file"></a>サービス構成ファイルの更新

上記でコピーした証明書の拇印を、次の設定で、サムプリントと属性値に貼り付けます。
worker ロール:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Web ロール:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

運用デプロイメントでは、CA、暗号化、サーバー証明書、クライアント証明書のそれぞれに異なる証明書を使用する必要があることに注意してください。 この詳細な手順については、 [セキュリティの構成](elastic-scale-split-merge-security-configuration.md)に関するページを参照してください。

## <a name="deploy-your-service"></a>サービスのデプロイ

1. [Azure portal](https://portal.azure.com) に移動します
2. 前に作成したクラウド サービスを選択します。
3. **[Overview]** をクリックします。
4. ステージング環境を選択し、 **[アップロード]** をクリックします。
5. ダイアログ ボックスにデプロイ ラベルを入力します。 [パッケージ] と [構成] の両方で [ローカルから] をクリックし、*SplitMergeService.cspkg* ファイルと、先ほど構成した cscfg ファイルを選択します。
6. **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイします。]** チェック ボックスがオンになっていることを確認します。
7. 右下のチェック マークをクリックしてデプロイを開始します。 完了には数分かかります。

## <a name="troubleshoot-the-deployment"></a>デプロイのトラブルシューティング

Web ロールのオンライン化に失敗した場合は、セキュリティの構成に問題があると考えられます。 TLS/SSL が前の説明どおりに構成されていることをご確認ください。

worker ロールのオンライン化に失敗した場合に最も考えられるのは、先に作成した状態データベースへの接続に問題があることです。

- 使用する .cscfg の接続文字列が正確であることを確認してください。
- サーバーとデータベースが存在し、ユーザー ID とパスワードが正しいことを確認します。
- Azure SQL Database の場合、接続文字列の形式は次のようにする必要があります。

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- サーバー名が **https://** で始まっていないことを確認します。
- サーバーで Azure サービスからの接続が許可されていることを確認します。 これを行うには、ポータルでご自身のデータベースを開き、 **[Azure サービスへのアクセスを許可する]** 設定が [**オン** **] に設定されていることを確認してください。

## <a name="test-the-service-deployment"></a>サービス デプロイのテスト

### <a name="connect-with-a-web-browser"></a>Web ブラウザーへの接続

Split-Merge サービスの Web エンドポイントを決定します。 これをポータルで見つけるには、クラウド サービスの **[概要]** に移動し、右側の **[サイトの URL]** を検索します。 既定のセキュリティ設定では HTTP エンドポイントは無効であるため、**http://** を **https://** に置き換えます。 この URL のページをブラウザーに読み込みます。

### <a name="test-with-powershell-scripts"></a>PowerShell スクリプトでのテスト

付属の PowerShell スクリプト サンプルを実行して、デプロイメントと環境をテストできます。

> [!IMPORTANT]
> このサンプル スクリプトは PowerShell 5.1 で実行してください。 現時点では、PowerShell 6 以降では実行できません。

付属のスクリプト ファイルは、次のとおりです。

1. *SetupSampleSplitMergeEnvironment.ps1* - Split/Merge のテスト データ層を設定します (詳細については、次の表を参照してください)
2. *ExecuteSampleSplitMerge.ps1* - テスト データ層でテスト操作を実行します (詳細については、次の表を参照してください)
3. *GetMappings.ps1* – シャード マッピングの現在の状態を出力する最上位のサンプル スクリプトです。
4. *ShardManagement.psm1* – ShardManagement API をラップするヘルパー スクリプトです
5. *SqlDatabaseHelpers.psm1* – SQL Database のデータベースを作成および管理するためのヘルパー スクリプトです

   <table style="width:100%">
     <tr>
       <th>PowerShell ファイル</th>
       <th>手順</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. シャードのマップ マネージャー データベースを作成します。</td>
     </tr>
     <tr>
       <td>2. 2 つのシャード データベースを作成します。
     </tr>
     <tr>
       <td>3. これらのデータベース用のシャード マップを作成します (これらのデータベースに関する既存のシャード マップを削除します)。 </td>
     </tr>
     <tr>
       <td>4. 両シャード上に小さいサンプル テーブルを作成し、いずれかのシャードで、このテーブルにデータを読み込みます。</td>
     </tr>
     <tr>
       <td>5. シャード化したテーブルの SchemaInfo を宣言します。</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell ファイル</th>
       <th>手順</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. データを半分に分割して最初のシャードから 2 番目のシャードに渡す分割要求を Split-Merge サービスの Web フロントエンドに送信します。</td>
     </tr>
     <tr>
       <td>2. 分割要求の状態を Web フロントエンドに対してポーリングし、要求が完了するまで待機します。</td>
     </tr>
     <tr>
       <td>3. 2 番目のシャードから最初のシャードにデータを戻すための移動を求めるマージ要求を、Split-Merge サービスの Web フロントエンドに送信します。</td>
     </tr>
     <tr>
       <td>4. マージ要求の状態を Web フロントエンドに対してポーリングし、要求が完了するまで待機します。</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>PowerShell でのデプロイの確認

1. 新しい PowerShell ウィンドウを開き、分割-結合パッケージをダウンロードしたディレクトリに移動し、"powershell" ディレクトリに移動します。

2. サーバーを作成 (または既存のサーバーを選択) します。ここでシャード マップ マネージャーとシャードが作成されます。

   > [!NOTE]
   > *SetupSampleSplitMergeEnvironment.ps1* スクリプトは、これらのデータベースをすべて同じサーバー上に既定で作成します。これはスクリプトの簡潔さを維持するためです。 Split-Merge サービス自体の制約ではありません。

   Split-Merge サービスでデータを移動してシャード マップを更新するためには、DB への読み取りと書き込みのアクセス権のある SQL 認証ログインが必要になります。 Split-Merge サービスはクラウドで実行するため、現時点では統合認証はサポートしていません。

   これらのスクリプトを実行するマシンの IP アドレスからのアクセスを許可するようにサーバーが構成されていることをご確認ください。 この設定は、[SQL サーバー]/[ファイアウォール] と [仮想ネットワーク]/[クライアント IP アドレス] の下で確認できます。

3. *SetupSampleSplitMergeEnvironment.ps1* スクリプトを実行してサンプル環境を作成します。

   このスクリプトを実行すると、シャード マップ マネージャー データベース上にある既存のシャード マップ管理データ構造とシャードはすべて消去されます。 これは、シャード マップやシャードの再初期化を希望する場合、スクリプトを再実行するのに便利です。

   サンプルのコマンド ライン:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. サンプル環境に現在あるマッピングを表示するには、Getmappings.ps1 スクリプトを実行します。

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. *ExecuteSampleSplitMerge.ps1* スクリプトを実行して、分割操作を実行 (データの半分を最初のシャードから 2 番目のシャードに移動) し、次にマージ操作を実行 (データを最初のシャードに戻すために移動) します。 TLS が構成済みで、http エンドポイントが無効のままになっている場合は、代わりに https:// のエンドポイントを使用します。

    サンプルのコマンド ライン:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    下記のエラーが表示された場合に最も考えられるのは、Web エンドポイントの証明書に問題があることす。 任意の Web ブラウザーを使用して Web エンドポイントに接続を試み、証明書エラーになるかご確認ください。

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    成功した場合、出力は次のようになります。

    ```output
    > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
    > Sending split request
    > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
    > Polling split-merge request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    > Sending merge request
    > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
    > Polling request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    >
    ```

6. 他のデータ型も試してみてください。 これらのすべてのスクリプトでは、オプションの -ShardKeyType パラメーターを使用してキーの種類を指定することができます。 既定値は Int32 ですが、Int64、Guid、またはバイナリも指定できます。

## <a name="create-requests"></a>要求の作成

サービスは、Web UI を使用するか、インポートするか、Web ロールで要求を送信する SplitMerge.psm1 PowerShell モジュールを使用する方法のいずれかで利用できます。

サービスは、シャード化したテーブルと参照テーブルの両方にデータを移動できます。 シャード テーブルには、シャード キー列があり、シャードごとに異なる行データを保持します。 参照テーブルはシャードされないため、すべてのシャードに同じ行データが含まれています。 データを頻繁には変更しない場合に参照テーブルを使用すると便利です。また、これをクエリに使用してシャード化されたテーブルとの JOIN を行います。

分割やマージの操作を実行するには、移動の対象となるシャード化したテーブルと参照テーブルを宣言する必要があります。 これは **SchemaInfo** API で行います。 この API は、 **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** 名前空間にあります。

1. シャード テーブルごとに、テーブルの親のスキーマ名 (オプション、既定値は "dbo")、テーブル名、およびシャード キーが含まれているテーブル内の列名を記述する **ShardedTableInfo** オブジェクトを作成します。
2. 参照テーブルごとに、テーブルの親のスキーマ名 (オプション、既定値は "dbo") とテーブル名を記述する **ReferenceTableInfo** オブジェクトを作成します。
3. 新しい **SchemaInfo** オブジェクトに、前の TableInfo オブジェクトを追加します。
4. **ShardMapManager** オブジェクトへの参照を取得し、**GetSchemaInfoCollection** を呼び出します。
5. **SchemaInfoCollection** に **SchemaInfo** を追加し、シャード マップ名を入力します。

この例は、SetupSampleSplitMergeEnvironment.ps1 スクリプトで確認できます。

Split-Merge サービスはターゲット データベース (またはデータベースにある任意のテーブル用のスキーマ) を作成しません。 サービスに要求を送信する前に作成しておく必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

サンプル PowerShell スクリプトの実行中に、次のメッセージが表示されることがあります。

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

このエラーは、TLS/SSL 証明書が正しく構成されていないことを意味しています。 「Web ブラウザーを使って接続する」のセクションの手順に従ってください。

要求を送信できない場合、次の内容が表示される可能性があります。

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

この例外が表示された場合は、構成ファイルの、特に、 **WorkerRoleSynchronizationStorageAccountConnectionString** の設定値が適切であるか確認してください。 このエラーは、通常、Worker ロールがメタデータ データベースを初回使用時に正常に初期化できなかったことを示しています。

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png