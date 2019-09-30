---
title: Azure SQL を参照する DNS エイリアス用の PowerShell | Microsoft Docs
description: New-AzSqlServerDNSAlias などの PowerShell コマンドレットを使用すると、クライアントの構成を手動で変更することなく、新しいクライアント接続を別の Azure SQL Database サーバーにリダイレクトできます。
keywords: DNS SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 47afd905b1fa28ce65163203b9d43781f434233d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058548"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Azure SQL Database を参照する DNS エイリアス用の PowerShell

この記事では、Azure SQL Database の DNS エイリアスを管理する方法を示す PowerShell スクリプトについて説明します。 このスクリプトには次のコマンドレットが含まれており、それぞれ下記のアクションを実行します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

コード例で使用されているコマンドレットは次のとおりです。

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias):Azure SQL Database サービス システムに新しい DNS エイリアスを作成します。 このエイリアスは、Azure SQL Database サーバー 1 を参照します。
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias):SQL DB サーバー 1 に割り当てられているすべての DNS エイリアスを取得して一覧表示します。
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias):エイリアスが参照するよう構成されているサーバー名をサーバー 1 から SQL DB サーバー 2 に変更します。
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias):エイリアスの名前を使って、DNS エイリアスを SQL DB サーバー 2 から削除します。

## <a name="dns-alias-in-connection-string"></a>接続文字列内の DNS エイリアス

SQL Server Management Studio (SSMS) などのクライアントでは、特定の Azure SQL Database サーバーに接続するために、実際のサーバー名の代わりに DNS エイリアス名を指定できます。 次のサーバー文字列の例では、4 ノード サーバー文字列の中で、ドットで区切られた 1 つ目のノードでエイリアス *any-unique-alias-name* が使用されています。

- サーバー文字列の例: `any-unique-alias-name.database.windows.net`

## <a name="prerequisites"></a>前提条件

この記事で紹介した PowerShell デモ スクリプトを実行する際は、次の前提条件となるものを用意する必要があります。

- Azure サブスクリプションおよびアカウント。 無料試用版については、[https://azure.microsoft.com/free/][https://azure.microsoft.com/free/] をクリックしてください。
- コマンドレット **New-AzSqlServerDNSAlias** が含まれている Azure PowerShell モジュール。
  - インストールまたはアップグレードするには、[Azure PowerShell モジュールのインストール][install-Az-ps-84p]に関するページを参照してください。
  - バージョンを確認するには、powershell\_ise.exe で `Get-Module -ListAvailable Az;` を実行してください。
- Azure SQL Database サーバー 2 つ。

## <a name="code-example"></a>コード例

次の PowerShell コード例では、最初に、複数の変数にリテラル値を割り当てています。 コードを実行する際は、まず、システム内の実際の値に一致するようにすべてのプレースホルダーの値を編集する必要があります。 コード例は、コードを実行せず、コードを学習する目的でも役立ちます。 コードのコンソール出力も下に記載しています。

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>PowerShell コード例の実際のコンソール出力

次のコンソール出力は、実際の実行からコピーして貼り付けられたものです。

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>次の手順

SQL Database の DNS エイリアス機能の詳しい説明については、「[Azure SQL Database の DNS エイリアス][dns-alias-overview-37v]」を参照してください。

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
