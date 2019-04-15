---
title: Elastic Database ジョブのインストールの概要 | Microsoft Docs
description: 弾力性ジョブの機能のインストールについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316866"
---
# <a name="installing-elastic-database-jobs-overview"></a>Elastic Database ジョブのインストールの概要

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Elastic Database ジョブ**](sql-database-elastic-jobs-overview.md)は、PowerShell または Azure Portal を使用してインストールできます。PowerShell パッケージをインストールしている場合にのみ、PowerShell API を使用してジョブを作成および管理する機能を利用できます。 さらに現在のところ、PowerShell API はポータルよりもはるかに多数の機能を使用できます。

既存の**エラスティック プール**から、ポータルを使用して **Elastic Database ジョブ**を既にインストールしている場合、最新の PowerShell プレビューには、既存のインストールをアップグレードするスクリプトが含まれています。 PowerShell API 経由で公開されている新しい機能を活用するために、最新の **Elastic Database ジョブ** コンポーネントにアップグレードすることを強くお勧めします。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 無料評価版については、「 [無料評価版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* Azure PowerShell。 [Web Platform インストーラー](https://go.microsoft.com/fwlink/p/?linkid=320376)を使用して最新バージョンをインストールします。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」をご覧ください。
* [NuGet Command-line Utility](https://nuget.org/nuget.exe) を使用して、Elastic Database ジョブ パッケージをインストールします。 詳細については、 https://docs.nuget.org/docs/start-here/installing-nuget を参照してください。

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Elastic Database ジョブ PowerShell パッケージをダウンロードしてインストールする
1. Microsoft Azure PowerShell コマンド ウィンドウを開き、NuGet Command-line Utility (nuget.exe) をダウンロードしたディレクトリに移動します。
2. 次のコマンドを使用して、 **エラスティック データベース ジョブ** パッケージを現在のディレクトリにダウンロードしてインポートします。
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Elastic Database ジョブ** ファイルは、ローカル ディレクトリの **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** というフォルダー内に保存されます。この *x.x.xxxx.x* には、バージョン番号が反映されます。 (必要なクライアント .dll を含む) PowerShell コマンドレットは、**tools\ElasticDatabaseJobs** サブディレクトリに保存されます。インストール、アップグレード、およびアンインストールを行う PowerShell スクリプトも **tools** サブディレクトリに保存されます。
3. 次のように cd tools と入力して、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x 以下の tools サブディレクトリに移動します。
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. .\InstallElasticDatabaseJobsCmdlets.ps1 スクリプトを実行して、ElasticDatabaseJobs ディレクトリを $home\Documents\WindowsPowerShell\Modules にコピーします。 この処理で、次のように使用するモジュールも自動的にインポートされます。
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>PowerShell を使用して Elastic Database ジョブ コンポーネントをインストールする
1. Microsoft Azure PowerShell コマンド ウィンドウを開き、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x フォルダー以下の \tools サブディレクトリに移動します(cd \tools と入力します)
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. .\InstallElasticDatabaseJobs.ps1 PowerShell スクリプトを実行し、要求された変数に値を設定します。 このスクリプトで、 [Elastic Database ジョブ コンポーネントと価格設定](sql-database-elastic-jobs-overview.md#components-and-pricing) のページで説明されているコンポーネントが作成され、依存するコンポーネントを適切に使用するように Azure クラウド サービスが構成されます。

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

コマンドを実行すると、**[ユーザー名]** と **[パスワード]** の入力を求めるウィンドウが表示されます。 ここで入力するユーザー名とパスワードは Azure の資格情報ではなく、新しいサーバーの管理者の資格情報となります

この呼び出し例で指定したパラメーターは、実際の設定に合わせて変更できます。 次に各パラメーターの動作の詳細について説明します。

<table style="width:100%">
  <tr>
    <th>パラメーター</th>
    <th>説明</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>新しく作成された Azure コンポーネントを含めるために作成する Azure リソース グループ名を指定します。 このパラメーターの既定値は “__ElasticDatabaseJob” です。 この値は変更しないことをお勧めします。</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>新しく作成された Azure コンポーネントに使用する Azure のリージョンを指定します。 このパラメーターの既定値は Central US (米国中央部) です。</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>インストールするサービス ワーカーの数を指定します。 このパラメーターの既定値は 1 です。 ワーカー数を多くすると、サービスをスケール アウトし、高可用性を実現できます。 サービスの高可用性が必要なデプロイメントの場合、"2" を使用することをお勧めします。</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>クラウド サービス内で使用する VM サイズを指定します。 このパラメーターの既定値は A0 です。 ../../A3 のパラメーター値を指定できます。それぞれ、worker ロールに ExtraSmall/Small/Medium/Large サイズが使用されます。 worker ロールのサイズについて詳しくは、<a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">エラスティック データベース ジョブのコンポーネントと価格</a>に関するページをご覧ください。</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Standard エディションのコンピューティング サイズを指定します。 このパラメーターの既定値は S0 です。 ../../../../S9/S12 のパラメーター値を指定できます。Azure SQL Database では各コンピューティング サイズが使用されます。 SQL Database のコンピューティング サイズの詳細については、<a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">エラスティック データベース ジョブのコンポーネントと価格</a>に関するページをご覧ください。</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>新しく作成した Azure SQL Database サーバーの管理ユーザー名を指定します。 指定しない場合、PowerShell の資格情報ウィンドウが開き、資格情報の入力を求められます。</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>新しく作成した Azure SQL Database サーバーの管理パスワードを指定します。 指定しない場合、PowerShell の資格情報ウィンドウが開き、資格情報の入力を求められます。</td>
</tr>
</table>

多数のデータベースに対して同時に実行されるジョブ数が多数あるシステムの場合、-ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2 などのパラメーターを指定することをお勧めします。

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>PowerShell を使用して既存の Elastic Database ベース ジョブ コンポーネントのインストールを更新する
**Elastic Database ジョブ** は、スケールと高可用性のために既存のインストール内で更新できます。 このプロセスは、将来的に、管理データベースを削除して再作成することなく、サービス コードをアップグレードすること見込んでいます。 また、同じバージョン内でこのプロセスを使用して、サービス VM サイズまたはサーバーのワーカー数を変更できます。

インストールの VM サイズを更新するには、パラメーターを選択した値に更新して、次のスクリプトを実行します。

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>パラメーター</th>
  <th>説明</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Elastic Database ジョブ コンポーネントを最初にインストールしたときに使用した Azure リソース グループ名を指定します。 このパラメーターの既定値は “__ElasticDatabaseJob” です。 この値を変更することは推奨されないので、このパラメーターを指定する必要はありません。</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>インストールするサービス ワーカーの数を指定します。  このパラメーターの既定値は 1 です。  ワーカー数を多くすると、サービスをスケール アウトし、高可用性を実現できます。  サービスの高可用性が必要なデプロイメントの場合、"2" を使用することをお勧めします。</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>クラウド サービス内で使用する VM サイズを指定します。 このパラメーターの既定値は A0 です。 ../../A3 のパラメーター値を指定できます。それぞれ、worker ロールに ExtraSmall/Small/Medium/Large サイズが使用されます。 worker ロールのサイズについて詳しくは、<a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">エラスティック データベース ジョブのコンポーネントと価格</a>に関するページをご覧ください。</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>ポータルを使用して Elastic Database ジョブ コンポーネントをインストールする
[エラスティック プールを作成](sql-database-elastic-pool-manage-portal.md)すると、 **Elastic Database ジョブ** コンポーネントをインストールして、エラスティック プール内の各データベースに対して管理タスクを実行できるようになります。 **Elastic Database ジョブ** PowerShell API を使用するときとは異なり、現在、ポータル インターフェイスは既存のプールに対する実行のみに制限されています。

**推定所要時間:** 10 分。

1. [Azure Portal](https://portal.azure.com/#) で、エラスティック プールのダッシュボード ビューから、**[ジョブの作成]** をクリックします。
2. 初めてジョブを作成する場合は、**[プレビュー版の使用条件]** をクリックして、**Elastic Database ジョブ**をインストールする必要があります。
3. チェック ボックスをクリックして条項に同意します。
4. [サービスのインストール] ビューで、 **[ジョブの資格情報]** をクリックします。
   
    ![サービスのインストール][1]
5. データベース管理者のユーザー名とパスワードを入力します。インストールの一環として、新しい Azure SQL Database サーバーが作成されます。 この新しいサーバー内に、管理データベースと呼ばれる新しいデータベースが作成され、Elastic Database ジョブのメタデータの格納に使用されます。 ここで作成されるユーザー名とパスワードは、管理データベースにログインするために使用されます。 プール内のデータベースに対するスクリプト実行には、別の資格情報が使用されます。
   
    ![ユーザー名とパスワードの作成][2]
6. [OK] ボタンをクリックします。 新しい [リソース グループ](../azure-resource-manager/resource-group-overview.md)にコンポーネントが数分で作成されます。 下記に示すように、新しいリソース グループがスタート ボードに固定表示されています。 作成されると、弾力性データベース ジョブ (クラウド サービス、SQL Database、Service Bus、ストレージ記憶域) は、すべてグループ内に作成されます。
   
    ![スタート ボードのリソース グループ][3]
7. 弾力性データベース ジョブをインストール中にジョブを作成または管理しようとすると、 **資格情報** の入力中に 、次のメッセージが表示されます。
   
    ![実行中のデプロイメント][4]

アンインストールが必要な場合は、リソース グループを削除します。 「 [エラスティック データベース ジョブ コンポーネントのアンインストール方法](sql-database-elastic-jobs-uninstall.md)」を参照してください。

## <a name="next-steps"></a>次の手順
スクリプトを実行できる適切な権限を持つ資格情報がグループ内の各データベースに作成されていることを確認します。詳細については、「[SQL Database の保護](sql-database-manage-logins.md)」をご覧ください。
概要については、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」をご覧ください。

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
