---
title: Azure ID リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure ID リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 07ecaa564f2fda21967ab6f0c30c06fa876e4171
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699194"
---
# <a name="migrate-identity-resources-to-global-azure"></a>ID リソースをグローバル Azure に移行する

この記事には、Azure ID リソースを Azure Germany からグローバル Azure に移行するために役立つ可能性がある情報があります。

ID/テナントに関するガイダンスは、Azure のみの顧客を対象にしています。 Azure と O365 (またはその他の Microsoft 製品) のために一般的な Azure Active Directory (Azure AD) テナントを使用する場合は、ID の移行に複雑さが発生するため、この移行ガイダンスを使用する前に、まずアカウント マネージャーに連絡する必要があります。

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Germany の Azure AD は、グローバル Azure の Azure AD から分離しています。 現在、Azure Germany からグローバル Azure に Azure AD ユーザーを移動することはできません。

Azure Germany とグローバル Azure の既定のテナント名は、Azure が環境に基づいて自動的にサフィックスを追加するため、常に異なります。 たとえば、グローバル Azure の **contoso** テナントのメンバーのユーザー名が **user1\@contoso.microsoftazure.com** であるとします。 Azure Germany で、それは **user1\@contoso.microsoftazure.de** です。

Azure AD でカスタム ドメイン名 (**contoso.com** など) を使用する場合、Azure でドメイン名を登録する必要があります。 カスタム ドメイン名は、一度に *1 つだけ*のクラウド環境に定義できます。 ドメインが Azure Active Directory の*いずれか*のインスタンスに既に登録されている場合、ドメインの検証が失敗します。 たとえば、Azure Germany に存在するユーザー **user1\@contoso.com** は、同時に同じ名前でグローバル Azure にも存在することはできません。 **contoso.com** の登録は失敗します。

一部のユーザーが既に新しい環境に存在し、一部のユーザーがまだ古い環境に存在する "ソフト" 移行では、異なるクラウド環境ごとに異なるサインイン名が必要です。

この記事では、可能性のある各移行シナリオについて説明しません。 推奨事項は、たとえばユーザーのプロビジョニング方法、異なるユーザー名または UserPrincipalNames を使用するために選択できるオプション、およびその他の依存関係によって異なります。 ただし、現在の環境のユーザーとグループのインベントリの作成に役立ついくつかのヒントをまとめました。

Azure AD に関連するすべてのコマンドレットの一覧を取得するには、次のコマンドを実行します。

```powershell
Get-Help Get-AzureAD*
```

### <a name="inventory-users"></a>ユーザーのインベントリ作成

Azure AD インスタンスに存在するすべてのユーザーとグループの概要を取得するには:

```powershell
Get-AzureADUser -All $true
```

有効なアカウントのみを一覧表示するには、次のフィルターを追加します。

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true}
```

何かを忘れた場合に、すべての属性の完全なダンプを作成するには:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | Format-List *
```

ユーザーを再作成するために必要な属性を選択するには:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname
```

一覧を Excel にエクスポートするには、この一覧の最後で **Export-Csv** コマンドレットを使用します。 完全なエクスポートは、この例のようになります。

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname | Export-Csv -Path c:\temp\alluserUTF8.csv -Delimiter ";" -Encoding UTF8
```

> [!NOTE]
> パスワードを移行することはできません。 代わりに、シナリオに応じて、新しいパスワードを割り当てるか、セルフサービス メカニズムを使用する必要があります。
>
>また、環境に応じて、**Extensions**、**DirectReport**、または **LicenseDetail** の値などのその他の情報を収集する必要がある場合もあります。

必要に応じて、CSV ファイルを書式設定します。 次に[CSV からのデータのインポート](/powershell/azure/active-directory/importing-data)に関するページに記載されている手順に従って、新しい環境にユーザーを再作成します。

### <a name="inventory-groups"></a>グループのインベントリ作成

グループ メンバーシップをドキュメント化するには:

```powershell
Get-AzureADGroup
```

各グループのメンバーの一覧を取得するには:

```powershell
Get-AzureADGroup | ForEach-Object {$_.DisplayName; Get-AzureADGroupMember -ObjectId $_.ObjectId}
```

### <a name="inventory-service-principals-and-applications"></a>インベントリ サービス プリンシパルとアプリケーション

すべてのサービス プリンシパルとアプリケーションを再作成する必要がありますが、サービス プリンシパルとアプリケーションの状態をドキュメント化することをお勧めします。 次のコマンドレットを使用して、すべてのサービス プリンシパルの詳細なリストを取得できます。

```powershell
Get-AzureADServicePrincipal |Format-List *
```

```powershell
Get-AzureADApplication |Format-List *
```

`Get-AzureADServicePrincipal*` または `Get-AzureADApplication*` で始まる他のコマンドレットを使用して、詳細情報を取得できます。 

### <a name="inventory-directory-roles"></a>インベントリ ディレクトリ ロール

現在のロール割り当てをドキュメント化するには:

```powershell
Get-AzureADDirectoryRole
```

各ロールを調べて、ロールに関連付けられているユーザーまたはアプリケーションを見つけます。

```powershell
Get-AzureADDirectoryRole | ForEach-Object {$_.DisplayName; Get-AzureADDirectoryRoleMember -ObjectId
$_.ObjectId | Format-Table}
```
詳細:

- [ハイブリッド ID ソリューション](../active-directory/choose-hybrid-identity-solution.md)について参照してください。
- さまざまなクラウド環境に同期する方法については、[複数のクラウドで Azure AD Connect を使用する](https://blogs.technet.microsoft.com/ralfwi/2017/01/24/using-adconnect-with-multiple-clouds/)方法に関するブログの投稿を参照してください。
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) の詳細を確認してください。
- [カスタム ドメイン名](../active-directory/fundamentals/add-custom-domain.md)について参照してください。
- [CSV から Azure AD にデータをインポートする](/powershell/azure/active-directory/importing-data)方法を確認してください。

## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect は、オンプレミス Active Directory インスタンスと Azure Active Directory (Azure AD) 間で ID データを同期するツールです。 Azure AD Connect の現在のバージョンは、Azure Germany とグローバル Azure の両方で機能します。 Azure AD Connect は、一度に 1 つだけの Azure AD インスタンスに同期できます。 Azure Germany とグローバル Azure に同時に同期する場合は、これらのオプションを検討してください。

- Azure AD Connect の 2 つ目のインスタンスに追加のサーバーを使用します。 同じサーバーで Azure AD Connect の複数のインスタンスを使用することはできません。
- ユーザーの新しいサインイン名を定義します。 サインイン名のドメイン部分 (**\@** の後) は環境ごとに異なる必要があります。
- 逆方向 (Azure AD からオンプレミスの Active Directory へ) にも同期する場合、明確な "真実のソース" を定義します。

既に Azure AD Connect を使用して、Azure Germany 間で同期している場合は、手動で作成したすべてのユーザーを移行してください。 次の PowerShell コマンドレットでは、Azure AD Connect を使用して同期されないすべてのユーザーが一覧表示されます。

```powershell
Get-AzureADUser -All $true |Where-Object {$_.DirSyncEnabled -ne "True"}
```

詳細:

- [Azure AD Connect](../active-directory/hybrid/reference-connect-dirsync-deprecated.md) の詳細を確認してください。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

新しい環境で、ユーザーを再作成し、Azure Multi-factor Authentication インスタンスを再定義する必要があります。 

多要素認証が有効になっているか、または適用対象にされているユーザー アカウントの一覧を取得するには:

1. Azure ポータルにサインインします。
1. **[ユーザー]** > **[すべてのユーザー]** > **[Multi-Factor Authentication]** の順に選択します。
1. 多要素認証サービス ページにリダイレクトされたら、適切なフィルターを設定して、ユーザーの一覧を取得します。

詳細:

- [Azure Multi-factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md) の詳細を確認してください。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
