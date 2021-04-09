---
title: Azure AD Connect:ADSyncTools PowerShell リファレンス | Microsoft Docs
description: このドキュメントでは、ADSyncTools.psm1 PowerShell モジュールの参照情報を示します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672270"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:ADSyncTools PowerShell リファレンス
次のドキュメントでは、Azure AD Connect に含まれる ADSyncTools.psm1 PowerShell モジュールの参照情報を示します。

## <a name="install-the-adsynctools-powershell-module"></a>ADSyncTools PowerShell モジュールをインストールする
ADSyncTools PowerShell モジュールをインストールするには、次の手順を実行します。

1.  管理者特権で Windows PowerShell を開きます。
2.  次を入力するか、コピーして貼り付けます。 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Enter キーを押します。
4.  モジュールがインストールされたことを確認するには、次のように入力するか、コピーして貼り付けます。
    ```powershell
    Get-module AdSyncTools
    ```
5.  モジュールに関する情報が表示されます。


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>概要
mS-Ds-ConsistencyGuid を AD ユーザーからクリアします

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
ターゲット AD ユーザーの mS-Ds-ConsistencyGuid の値をクリアします

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
設定する AD のターゲット ユーザー

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-database"></a>-Database
{{データベースの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{インスタンスの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{パスワードの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{サーバーの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{ユーザー名の説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>概要
ConsistencyGuid レポートをエクスポートします

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>Description
Import-ADSyncToolsImmutableIdMigration からのインポート CSV ファイルに基づいて ConsistencyGuid レポートを生成します

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
代替ログイン ID (メール) を使用します

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
CSV ファイルと LOG ファイルの出力ファイル名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{ホスト名の説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>概要
AD からユーザーを取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
AD オブジェクトを返します。作業予定:マルチ フォレストのサポート

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
ConsistencyGuid を設定する AD のターゲット ユーザー

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>概要
AD ユーザーから mS-Ds-ConsistencyGuid を取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
ターゲット AD ユーザーの mS-Ds-ConsistencyGuid 属性の値を GUID 形式で返します

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
設定する AD のターゲット ユーザー

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>概要
AD ユーザーから ObjectGuid を取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
ターゲット AD ユーザーの ObjectGUID 属性の値を GUID 形式で返します

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
設定する AD のターゲット ユーザー

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>概要
AAD 接続実行履歴を取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>Description
XML 形式で AAD 接続実行履歴を返す関数

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>例 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-days"></a>-Days
{{日の説明を入力}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>概要
SourceAnchor の変更エラーのあるユーザーを取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
関数は AAD 接続実行履歴を照会し、エラー "SourceAnchor 属性が変更されました。" を報告するすべてのユーザーをエクスポートします。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "ログ ファイルのパスとファイル名を入力" #"\<Source_Path\>" $outputPath = Read-Host -Prompt "出力ファイルのパスとファイル名を入力" #"\<Out_Path\>"

 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-sourcepath"></a>-sourcePath
{{ソース パスの説明を入力}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{出力パスの説明を入力}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>概要
AAD から ImmutableID をインポートします

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>Description
ImmutableID 値を含むすべての Azure AD 同期済みユーザーを格納するファイルを GUID 形式で生成します。要件:MSOnline PowerShell モジュール

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-output"></a>-Output
出力 CSV ファイル

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Azure AD のごみ箱から同期ユーザーを取得します

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-query"></a>-Query
{{クエリの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{SqlConnection の説明を入力}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>概要
UserCertificate 属性から有効期限が切れた証明書を削除するスクリプト

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>Description
このスクリプトは、Active Directory ドメイン内のターゲット組織単位からすべてのオブジェクトを取得し、オブジェクト クラス (ユーザー/コンピューター) でフィルター処理し、UserCertificate 属性に存在する有効期限が切れたすべての証明書を削除します。
既定 (BackupOnly モード) では、期限切れの証明書をファイルにバックアップするだけで、AD 内は何も変更しません。
-BackupOnly $false を使用した場合、これらのオブジェクトの UserCertificate 属性に存在する有効期限が切れた証明書は、ファイルにコピーされた後で AD から削除されます。
各証明書は別のファイル名でバックアップされます (ObjectClass_ObjectGUID_CertThumprint.cer)。このスクリプトでは、有効なまたは有効期限切れの証明書を持つすべてのユーザーと、行われた実際のアクション (スキップ/エクスポート/削除) が記載された CSV 形式のログ ファイルが作成されます。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>例 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMETERS

#### <a name="-targetou"></a>-TargetOU
AD オブジェクトを検索するターゲット OU

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly は AD から証明書を削除しません

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
オブジェクト クラスのフィルター

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>概要
簡単な説明

### <a name="syntax"></a>SYNTAX

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>Description
長い説明

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{ホスト名の説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>概要
(TO DO) 証明書ファイルから AD UserCertificate 属性を復元します

### <a name="syntax"></a>SYNTAX

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>Description
長い説明

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>概要
AD ユーザーに mS-Ds-ConsistencyGuid を設定します

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>Description
ターゲット AD ユーザーの mS-Ds-ConsistencyGuid 属性の値を設定します

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
ConsistencyGuid を設定する AD のターゲット ユーザー

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId (バイト配列、GUID、GUID 文字列または Base64 文字列)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>概要
{{概要を入力}}

### <a name="syntax"></a>SYNTAX

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>Description
{{説明を入力}}

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```powershell
PS C:\> {{ Add example code here }}
```

{{例の説明をここに追加}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{ホスト名の説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{ポートの説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>概要
AD インポート手順からトレース ファイルを作成します

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>Description
AAD Connect AD のすべての ldap クエリをトレースします。AD インポートは指定した AD 基準値チェックポイント (パーティション cookie) から実行します。 現在のフォルダーにトレース ファイル '\ADimportTrace_yyyyMMddHHmmss.log' を作成します。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{ADConnectorXML の説明を入力}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
AD コネクタ エクスポートの XML ファイル

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
ターゲット ドメイン コントローラー

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
フォレスト ルート DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
トレースする AD オブジェクトの種類 \> * = すべてのオブジェクトの種類

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
既にドメイン管理者として実行している場合は、AD 資格情報を指定する必要はありません。
XML ファイルの代わりに、透かしを手動で入力します。例: $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>概要
簡単な説明

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>Description
長い説明

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>例 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-context"></a>-Context
Param1 のヘルプの説明

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Param2 のヘルプの説明

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Param2 のヘルプの説明

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Param2 のヘルプの説明

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>概要
新しい ConsistencyGuid (ImmutableId) でユーザーを更新します

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
ConsistencyGuid レポートから取得した新しい ConsistencyGuid (ImmutableId) 値でユーザーを更新します。この機能は WhatIf スイッチをサポートします。注:ConsistencyGuid レポートはタブ区切り文字と一緒にインポートする必要があります

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>例 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
アクション

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
LOG ファイルの出力ファイル名

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
コマンドレットの実行時に発生する内容を示します。
このコマンドレットは実行されません。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
コマンドレットの実行前に確認を求めるメッセージが表示されます。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。
