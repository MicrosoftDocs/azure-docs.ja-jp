---
title: Azure AD Connect:ADSyncConfig PowerShell リファレンス | Microsoft Docs
description: このドキュメントでは、ADSyncConfig.psm1 PowerShell モジュールの参照情報を示します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850977"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:ADSyncConfig PowerShell リファレンス
次のドキュメントでは、Azure AD Connect に含まれる ADSyncConfig.psm1 PowerShell モジュールの参照情報を示します。


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>概要
各 AD コネクタに構成されているアカウント名とドメインを取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>Description
この関数では、AAD コネクトにある 'Get-ADSyncConnector' コマンドレットを使用し、AD コネクタ アカウントを示すテーブルである接続パラメーターから取得を行います。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>概要
アクセス許可の継承が無効になっている AD オブジェクトを取得します

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>Description
AD で SearchBase パラメーターから開始し検索を実行し、ACL の継承が現在無効になっていて ObjectClass パラメーターによってフィルター処理されているオブジェクトをすべて返します。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
'Contoso' ドメインで継承が無効になっているオブジェクトを検索します (既定では 'organizationalUnit' オブジェクトのみが返されます)
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>例 2
'Contoso' ドメインで継承が無効になっている 'user' オブジェクトを検索します
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>例 3
OU 内で継承が無効になっているすべての種類のオブジェクトを検索します
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETERS

#### <a name="-searchbase"></a>-SearchBase
AD ドメインの DistinguishedName または FQDN が可能な LDAP クエリの SearchBase

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

#### <a name="-objectclass"></a>-ObjectClass
'*' (すべてのオブジェクト クラス)、'user'、group'、'container' など、検索するオブジェクトのクラス。既定で、この関数は 'organizationalUnit' オブジェクト クラスを検索します。

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインを読み取るためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncBasicReadPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
コンピューターのすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
2.
デバイスのすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
3.
foreignsecurityprincipal のすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
5.
ユーザーのすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
6.
inetorgperson のすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
7.
グループのすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス
8.
連絡先のすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用される Exchange ハイブリッド機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncExchangeHybridPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
ユーザーのすべての子孫オブジェクトのすべての属性のプロパティの読み取り/書き込みアクセス
2.
inetorgperson のすべての子孫オブジェクトのすべての属性のプロパティの読み取り/書き込みアクセス
3.
グループのすべての子孫オブジェクトのすべての属性のプロパティの読み取り/書き込みアクセス
4.
連絡先のすべての子孫オブジェクトのすべての属性のプロパティの読み取り/書き込みアクセス

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用される Exchange メールのパブリック フォルダー機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncExchangeMailPublicFolderPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
パブリック フォルダーのすべての子孫オブジェクトのすべての属性のプロパティの読み取りアクセス

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用される mS-DS-ConsistencyGuid 機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncMsDsConsistencyGuidPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
ユーザーのすべての子孫オブジェクトの mS-DS-ConsistencyGuid 属性のプロパティの読み取り/書き込みアクセス

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用されるパスワード ハッシュの同期機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncPasswordHashSyncPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
ディレクトリの変更のレプリケート
2.
ディレクトリの変更すべてのレプリケート

これらのアクセス許可は、フォレストのすべてのドメインに付与されます。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用されるパスワード ライトバック機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncPasswordWritebackPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
子孫ユーザー オブジェクトのパスワードのリセット
2.
ユーザーのすべての子孫オブジェクトの lockoutTime 属性のプロパティの書き込みアクセス
3.
ユーザーのすべての子孫オブジェクトの pwdLastSet 属性のプロパティの書き込みアクセス

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>概要
AD で保護されているいかなるセキュリティ グループにも含まれない AD オブジェクトのアクセス許可のセキュリティを強化します。
典型的な例は、自動的に AAD Connect によって作成される AD Connect アカウント (MSOL) です。
このアカウントは、すべてのドメインへのレプリケートのアクセス許可を持ちます。しかし、保護されていないので、簡単に侵害される可能性があります。

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncRestrictedPermissions 関数は、提供されているアカウントのアクセス許可のセキュリティを強化します。
アクセス許可のセキュリティの強化には、次の手順が含まれます。
1. 指定したオブジェクトの継承を無効にします
2. SELF に固有の ACE を除き、特定のオブジェクトのすべての ACE を削除します。
SELF については、既定のアクセス許可を維持します。
3. 以下の特定のアクセス許可を割り当てます。

   | Type | 名前 | アクセス | 適用対象 |
   |------|------|--------|------------|
   | Allow | SYSTEM | フル コントロール | このオブジェクト |
   | Allow | Enterprise Admins | フル コントロール | このオブジェクト |
   | Allow | Domain Admins | フル コントロール | このオブジェクト | 
   | Allow | 管理者 | フル コントロール | このオブジェクト |
   | Allow | Enterprise Domain Controllers | コンテンツの一覧 <br> すべてのプロパティの読み取り <br> 読み取りのアクセス許可 | このオブジェクト |
   | Allow | Authenticated Users | コンテンツの一覧 <br> すべてのプロパティの読み取り <br> 読み取りのアクセス許可 | このオブジェクト |

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
アクセス許可のセキュリティを強化する必要がある Active Directory アカウントの DistinguishedName。
これは通常は、MSOL_nnnnnnnnnn アカウントであるか、AD コネクタに構成されているカスタム ドメイン アカウントです。

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

#### <a name="-credential"></a>-Credential
ADConnectorAccountDN アカウントに対するアクセス許可を制限するために必要な権限を持つ管理者資格情報。 これは通常、企業またはドメインの管理者です。 アカウント参照の失敗を回避するには、管理者アカウントの完全修飾ドメイン名を使用します。
例:CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
DisableCredentialValidation を使用すると、-Credential で提供されている資格情報が AD では有効であるか、また提供されたアカウントに ADConnectorAccountDN アカウントのアクセス許可を制限するのに必要なアクセス許可があるかが関数によって確認されません。

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>概要
指定されたコネクターアカウントに対して、ご使用の Active Directory フォレストとドメインで利用されるグループ ライトバック機能のためのアクセス権を付与します。

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Description
Set-ADSyncUnifiedGroupWritebackPermissions 関数は、次を含む必要なアクセス許可を AD 同期アカウントに付与します。
1.
すべてのグループ オブジェクトの種類またはサブ オブジェクトの汎用の読み取り/書き込み、削除、ツリーの削除および子の作成\削除

これらのアクセス許可は、フォレスト内のすべてのドメインに適用されます。
必要に応じて ADobjectDN パラメーターに DistinguishedName を指定し、これらのアクセス許可をその AD オブジェクトのみに設定できます (サブ オブジェクトへの継承を含む)。
この場合、GroupWriteback 機能とリンクしたいコンテナーの識別名は ADobjectDN になります。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>例 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>例 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>例 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの名前。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントのドメイン。

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
ディレクトリ内のオブジェクトを管理するために Azure AD Connect Sync によって使用される、または使用される可能性のある Active Directory アカウントの DistinguishedName。

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
アクセス許可を設定するターゲットの AD オブジェクトの DistinguishedName (省略可能)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
これらのアクセス許可で AdminSDHolder コンテナーを更新しないことを示す省略可能なパラメーター

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>概要
指定した AD オブジェクトのアクセス許可を示します。

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>Description
この関数では、パラメーター -ADobjectDN で提供されている特定の AD オブジェクトに現在設定されている AD アクセス許可をすべて返します。
ADobjectDN は、DistinguishedName の形式で返される必要があります。

### <a name="examples"></a>例

#### <a name="example-1"></a>例 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{ADobjectDN の説明を入力}}

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

#### <a name="commonparameters"></a>共通パラメーター
このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。
詳細については、「about_CommonParameters」 (https://go.microsoft.com/fwlink/?LinkID=113216) ) を参照してください。
