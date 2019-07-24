---
title: Azure AD Connect:AD DS コネクタ アカウントのアクセス許可を構成する | Microsoft Docs
description: このドキュメントでは、新しい ADSyncConfig PowerShell モジュールを使用して AD DS コネクタ アカウントを構成する方法について詳しく説明します
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6510105af8c019b1aca5333f516a10667edaadb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000872"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS コネクタ アカウントのアクセス許可の構成 

[ADSyncConfig.psm1](reference-connect-adsyncconfig.md) という名前の PowerShell モジュールがビルド 1.1.880.0 (2018 年 8 月にリリース) に導入されました。これには、Azure AD Connect のデプロイ用の適切な Active Directory アクセス許可を構成するのに役立つコマンドレットのコレクションが含まれています。 

## <a name="overview"></a>概要 
以下の PowerShell コマンドレットを使用すると、AD DS コネクタ アカウントの Active Directory アクセス許可を、Azure AD Connect で有効にする機能ごとに設定することができます。 問題を防ぐため、フォレストに接続するためにカスタム ドメイン アカウントを使用して Azure AD Connect をインストールしたい場合は、必ず事前に Active Directory のアクセス許可を準備してください。 この ADSyncConfig モジュールは、Azure AD Connect のデプロイ後にアクセス許可を構成する場合も使用できます。

![](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Azure AD Connect の高速インストールでは、自動的に生成されたアカウント (MSOL_nnnnnnnnnn) が、必要なすべてのアクセス許可が付与されて Active Directory に作成されます。そのため、組織単位または Azure AD に同期する特定の Active Directory オブジェクトでアクセス許可の継承がブロックされている場合を除き、この ADSyncConfig モジュールを使用する必要はありません。 
 
### <a name="permissions-summary"></a>アクセス許可の概要 
次の表で、AD オブジェクトに必要なアクセス許可の概要を示します。 

| 機能 | アクセス許可 |
| --- | --- |
| ms-DS-ConsistencyGuid 機能 |「[設計概念 - sourceAnchor としての ms-DS-ConsistencyGuid の使用](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)」に記載された msDS-ConsistencyGuid 属性への書き込みアクセス許可。 | 
| パスワード ハッシュの同期 |<li>ディレクトリの変更のレプリケート</li>  <li>ディレクトリの変更をすべてにレプリケート |
| Exchange ハイブリッドのデプロイメント |ユーザー、グループ、連絡先用の「[Exchange ハイブリッドの書き戻し](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)」に記載された属性への書き込みアクセス許可。 |
| Exchange メールのパブリック フォルダー |パブリック フォルダーに関して、「[Exchange メールのパブリック フォルダー](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)」に記載された属性への読み取りアクセス許可。 | 
| パスワードの書き戻し |ユーザー向けの「[パスワード管理の概要](../authentication/howto-sspr-writeback.md)」に記載された属性への書き込みアクセス許可。 |
| デバイスの書き戻し |「[デバイスの書き戻し](how-to-connect-device-writeback.md)」に記載されているデバイスのオブジェクトとコンテナーに対する書き込みアクセス許可。 |
| グループの書き戻し |同期された **Office 365 グループ**のグループ オブジェクトの読み取り、作成、更新、削除。  詳しくは、「[グループの書き戻し](how-to-connect-preview.md#group-writeback)」をご覧ください。|

## <a name="using-the-adsyncconfig-powershell-module"></a>ADSyncConfig PowerShell モジュールの使用 
ADSyncConfig モジュールは AD DS PowerShell モジュールとツールに依存するため、[AD DS 用のリモート サーバー管理ツール (RSAT)](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) が必要です。 AD DS 用の RSAT をインストールするには、「管理者として実行」して Windows PowerShell ウィンドウを開いて、次のように実行します。 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![構成](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>AD DS 用の RAST が既にインストールされているドメイン コントローラーにファイル **C:\Program files \microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** を、コピーし、そこでこの PowerShell モジュールを使用することもできます。

ADSyncConfig の使用を開始するには、Windows PowerShell ウィンドウにモジュールを読み込む必要があります。 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

このモジュールに含まれているすべてのコマンドレットを確認するには、次のように入力できます。  

``` powershell
Get-Command -Module AdSyncConfig  
```

![○](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

各コマンドレットには、AD DS コネクタ アカウントや AdminSDHolder スイッチを入力するものと同じパラメーターがあります。 AD DS コネクタ アカウントを指定するには、アカウント名とドメインと入力することも、アカウントの識別名 (DN) だけを入力することもできます。

例: 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName ADaccount -ADConnectorAccountDomain Contoso`

または、 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADaccount,OU=AADconnect,DC=Contoso,DC=com'`

AdminSDHolder コンテナーに対するアクセス許可を変更しない場合は、スイッチ `-SkipAdminSdHolders` を使用します。 

既定では、すべてのアクセス許可設定コマンドレットが、フォレスト内の各ドメインのルートに対して AD DS のアクセス許可を設定しようとします。つまり、PowerShell セッションを実行するユーザーには、フォレスト内の各ドメインに対するドメイン管理者権限が必要になります。  この要件により、フォレストのルートからエンタープライズ管理者を使用することをお勧めします。 Azure AD Connect のデプロイに複数の AD DS コネクタがある場合は、AD DS コネクタのある各フォレストで同じコマンドレットを実行する必要があります。 

また、パラメーター `-ADobjectDN` に続いて、アクセス許可を設定するターゲット オブジェクトの DN を使用して、特定の OU または AD DS オブジェクトにアクセス許可を設定することもできます。 ターゲット ADobjectDN を使用する場合、このコマンドレットはこのオブジェクトにのみアクセス許可を設定し、ドメインのルートまたは AdminSDHolder コンテナーには設定しません。 このパラメーターは、アクセス許可の継承が無効になっている特定の OU または AD DS オブジェクトがある場合に便利です (「アクセス許可の継承が無効になっているオブジェクトの検索」を参照) 

これらの一般的なパラメーターの例外は、AD DS コネクタ アカウント自体に対するアクセス許可の設定に使用する `Set-ADSyncRestrictedPermissions` コマンドレットと、パスワード ハッシュ同期に必要な権限がそのドメインのルートにのみ設定され、そのために `-ObjectDN` パラメーターまたは `-SkipAdminSdHolders` パラメーターが含まれない、`Set-ADSyncPasswordHashSyncPermissions` コマンドレットです。

### <a name="determine-your-ad-ds-connector-account"></a>AD DS コネクタ アカウントの確認 
Azure AD Connect が既にインストールされていて、Azure AD Connect で現在使用中の AD DS コネクタ アカウントを確認したい場合は、次のコマンドレットを実行できます。 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>アクセス許可の継承が無効になっている AD DS オブジェクトの検索 
アクセス許可の継承が無効になっている AD DS オブジェクトがあるかどうかを確認する場合は、次のように実行できます。 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
既定では、このコマンドレットは継承が無効になっている OU のみを検索しますが、`-ObjectClass` パラメーターにその他の AD DS オブジェクト クラスを指定することも、次のようにすべてのオブジェクト クラスとして ' *' を使用することもできます。 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>オブジェクトの AD DS アクセス許可の表示 
下のコマンドレットを使用すると、Active Directory オブジェクトの DistinguishedName を指定することで、そのオブジェクトに現在設定されているアクセス許可の一覧を表示できます。 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>AD DS コネクタ アカウントのアクセス許可の構成 
 
### <a name="configure-basic-read-only-permissions"></a>基本的な読み取り専用のアクセス許可の構成 
Azure AD Connect 機能を使用しない場合に、AD DS コネクタ アカウントに対して基本的な読み取り専用のアクセス許可を設定するには、次のコマンドを実行します。 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


または、 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


このコマンドレットは、次のアクセス許可を設定します。 
 

|type |Name |Access |適用対象| 
|-----|-----|-----|-----|
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |デバイスの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント|すべてのプロパティの読み取り |InetOrgPerson の子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |コンピューターの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |foreignSecurityPrincipal の子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |グループの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |ユーザーの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |連絡先の子孫オブジェクト| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-Consistency-Guid のアクセス許可の構成 
ソース アンカーとして ms-Ds-Consistency-Guid 属性を使用している (「自分のソース アンカーを Azure で管理できるようにする」オプションとも言います) ときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

または、 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW|AD DS コネクタ アカウント|プロパティの読み取り/書き込み|ユーザーの子孫オブジェクト|

### <a name="permissions-for-password-hash-synchronization"></a>パスワード同期のアクセス許可 
パスワード ハッシュ同期を使用しているときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


または、 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |AD DS コネクタ アカウント |ディレクトリの変更のレプリケート |このオブジェクトのみ (ドメインのルート)| 
|ALLOW |AD DS コネクタ アカウント |ディレクトリの変更すべてのレプリケート |このオブジェクトのみ (ドメインのルート)| 
  
### <a name="permissions-for-password-writeback"></a>パスワード ライトバックのアクセス許可 
パスワード ライトバックを使用しているときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


または、

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |AD DS コネクタ アカウント |パスワードのリセット |ユーザーの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |プロパティ lockoutTime の書き込み |ユーザーの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |プロパティ pwdLastSet の書き込み |ユーザーの子孫オブジェクト| 

### <a name="permissions-for-group-writeback"></a>グループの書き戻しのアクセス許可 
グループの書き戻しを使用しているときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
または、 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |AD DS コネクタ アカウント |汎用の読み取り/書き込み |オブジェクトの種類のグループとサブオブジェクトのすべての属性| 
|ALLOW |AD DS コネクタ アカウント |子オブジェクトの作成/削除 |オブジェクトの種類のグループとサブオブジェクトのすべての属性| 
|ALLOW |AD DS コネクタ アカウント |削除/ツリー オブジェクトの削除|オブジェクトの種類のグループとサブオブジェクトのすべての属性|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange ハイブリッドのデプロイのアクセス許可 
Exchange ハイブリッドのデプロイを使用しているときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


または、 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

このコマンドレットは、次のアクセス許可を設定します。  
 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り/書き込み |ユーザーの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り/書き込み |InetOrgPerson の子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り/書き込み |グループの子孫オブジェクト| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り/書き込み |連絡先の子孫オブジェクト| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange メールのパブリック フォルダーのアクセス許可 (プレビュー) 
Exchange メールのパブリック フォルダー機能を使用しているときに、AD DS コネクタ アカウントのアクセス許可を設定するには、次のように実行します。 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


または、 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |AD DS コネクタ アカウント |すべてのプロパティの読み取り |パブリック フォルダーの子孫オブジェクト| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>AD DS コネクタ アカウントのアクセス許可の制限 
この PowerShell スクリプトは、パラメーターとして指定された AD コネクタ アカウントに対するアクセス許可のセキュリティを強化します。 アクセス許可のセキュリティの強化には、次の手順が含まれます。 

- 指定したオブジェクトの継承を無効にします 
- 特定のオブジェクトのすべての ACE を削除します。ただし、SELF については既定のアクセス許可を維持するために、SELF に固有の ACE を除きます。 
 
  -ADConnectorAccountDN パラメーターは、アクセス許可のセキュリティを強化する必要がある AD アカウントです。 これは、通常、AD DS コネクタに構成されている MSOL_nnnnnnnnnnnn ドメイン アカウントです (「AD DS コネクタ アカウントの確認」を参照)。 -Credential パラメーターは、ターゲットの AD オブジェクトに対する Active Directory のアクセス許可を制限するために必要な特権を持つ、管理者アカウントを指定するために必要です。 これは通常、企業またはドメインの管理者です。  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
次に例を示します。 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

このコマンドレットは、次のアクセス許可を設定します。 

|type |Name |Access |適用対象|
|-----|-----|-----|-----| 
|ALLOW |SYSTEM |フル コントロール |このオブジェクト 
|ALLOW |Enterprise Admins |フル コントロール |このオブジェクト 
|ALLOW |Domain Admins |フル コントロール |このオブジェクト 
|ALLOW |管理者 |フル コントロール |このオブジェクト 
|ALLOW |Enterprise Domain Controllers |コンテンツの一覧 |このオブジェクト 
|ALLOW |Enterprise Domain Controllers |すべてのプロパティの読み取り |このオブジェクト 
|ALLOW |Enterprise Domain Controllers |読み取りのアクセス許可 |このオブジェクト 
|ALLOW |Authenticated Users |コンテンツの一覧 |このオブジェクト 
|ALLOW |Authenticated Users |すべてのプロパティの読み取り |このオブジェクト 
|ALLOW |Authenticated Users |読み取りのアクセス許可 |このオブジェクト 

## <a name="next-steps"></a>次の手順
- [Azure AD Connect:アカウントとアクセス許可](reference-connect-accounts-permissions.md)
- [高速インストール](how-to-connect-install-express.md)
- [カスタム インストール](how-to-connect-install-custom.md)
- [ADSyncConfig リファレンス](reference-connect-adsyncconfig.md)

