---
title: Azure Active Directory の多要素認証の使用
description: Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics では、Active Directory のユニバーサル認証を使用して、SQL Server Management Studio (SSMS) からの接続をサポートします。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841132"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Azure Active Directory の多要素認証の使用
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics では、 *[Azure Active Directory - MFA で汎用]* 認証を使用して [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) からの接続をサポートします。 この記事では、さまざまな認証オプションの違いについて説明します。また、Azure SQL に対する Azure Active Directory (Azure AD) でのユニバーサル認証の使用に関連した制限事項についても説明します。

**最新の SSMS のダウンロード** - クライアント コンピューターで、「[SQL Server Management Studio (SSMS) のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」から SSMS の最新版をダウンロードします。 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


この記事で説明されているすべての機能を使用するには、2017 年 7 月以降のバージョン 17.2 を使用してください。 一番新しい接続ダイアログ ボックスは次の画像のようになっているはずです。

  ![SQL Server Management Studio の [サーバーに接続] ダイアログのスクリーンショット。サーバーの種類、サーバー名、認証それぞれの設定が表示されています。](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>認証オプション

Azure AD には 2 つの非対話型認証モデルがあり、さまざまなアプリケーション (ADO.NET、JDCB、ODC など) で使用できます。 これら 2 つの方式では、ポップアップ ダイアログ ボックスは表示されません。

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Azure AD Multi-Factor Authentication (MFA) もサポートする対話型の方式: 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA を使用すると、簡単なサインイン プロセスというユーザーの要求を満たしながら、データとアプリケーションへのアクセスを保護できます。 電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、簡単な各種確認オプションによって強力な認証が実現するため、ユーザーは自分に最も合った方法を選択できます。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。

Azure AD Multi-Factor Authentication の説明については、[Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) に関する記事をご覧ください。
構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](authentication-mfa-ssms-configure.md)」をご覧ください。

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD ドメイン名またはテナント ID パラメーター

[SSMS バージョン 17](/sql/ssms/download-sql-server-management-studio-ssms) 以降、他の Azure Active Directory から現在の Azure AD にゲスト ユーザーとしてインポートされたユーザーは、接続時に Azure AD のドメイン名またはテナント ID を指定できます。 ゲスト ユーザーには、他の Azure AD や、outlook.com、hotmail.com、live.com などの Microsoft アカウントまたは gmail.com などのその他のアカウントから招待されたユーザーが含まれます。 この情報により、`Azure Active Directory - Universal with MFA` 認証の際に、正しい認証機関を識別できます。 また、このオプションでは、outlook.com、hotmail.com、live.com などの Microsoft のアカウント (MSA) および MSA 以外のアカウントのサポートが必要です。 

ユニバーサル認証による認証を希望するすべてのユーザーは、Azure AD ドメイン名またはテナント ID を入力する必要があります。 このパラメーターは、Azure SQL 論理サーバーが関連付けられている現在の Azure AD ドメイン名またはテナント ID を表します。 たとえば、SQL 論理サーバーが Azure AD ドメイン `contosotest.onmicrosoft.com` に関連付けられており、ここでユーザー `joe@contosodev.onmicrosoft.com` が Azure AD ドメイン `contosodev.onmicrosoft.com` からインポートされたユーザーとしてホストされている場合、このユーザーを認証するために必要なドメイン名は `contosotest.onmicrosoft.com` になります。 ユーザーが SQL 論理サーバーに関連付けられている Azure AD のネイティブ ユーザーであり、MSA アカウントではない場合、ドメイン名およびテナント ID は必要ありません。 パラメーターを入力するには次のようにします (SSMS バージョン 17.2 以降)。


1. SSMS で接続を開きます。 サーバー名を入力し、 **[Azure Active Directory - MFA で汎用]** 認証を選択します。 サインインに使用する **ユーザー名** を追加します。
1. **[オプション]** ボックスを選択し、 **[接続プロパティ]** タブに移動します。 **[データベースへの接続]** ダイアログ ボックスで、使用するデータベースをダイアログ ボックスに入力します。 **[AD ドメインの名前またはテナントの ID]** ボックスをオンにし、ドメイン名 (**contosotest.onmicrosoft.com**) またはテナント ID の GUID などの認証機関を入力します。 

   ![[接続プロパティ] タブのスクリーンショット。[データベースに接続] と [AD ドメイン名またはテナント ID] が強調表示されています。](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

SSMS 18.x 以降を実行している場合、ゲスト ユーザーの AD ドメイン名またはテナント ID は不要になります。18.x 以降では自動的に認識されるためです。

   ![SSMS の [サーバーに接続] ダイアログの [接続プロパティ] タブのスクリーンショット。[データベースへの接続] フィールドで [MyDatabase] が選択されています。](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD の企業間サポート

> [!IMPORTANT]
> ゲスト ユーザーがグループに参加せずに Azure SQL Database、SQL Managed Instance、および Azure Synapse に接続することに対するサポートは、現在 **パブリック プレビュー** 段階です。 詳細については、「[Azure AD ゲスト ユーザーを作成し、Azure AD 管理者として設定する](authentication-aad-guest-users.md)」を参照してください。

Azure AD B2B シナリオでゲスト ユーザーとしてサポートされている Azure AD ユーザー ([Azure B2B コラボレーションの概要](../../active-directory/external-identities/what-is-b2b.md)に関する記事を参照) は、関連付けられた Azure AD に作成されたグループのメンバーとしてのみ SQL Database と Azure Synapse に接続でき、特定のデータベース内の [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) ステートメントを使用して手動でマップされます。 たとえば、`steve@gmail.com` を Azure AD `contosotest` に (Azure AD ドメイン `contosotest.onmicrosoft.com` を使用して) 招待した場合、Azure AD グループ (`usergroup` など) を、`steve@gmail.com` メンバーを含む Azure AD で作成する必要があります。 次に、Azure AD SQL 管理者または Azure AD DBO が Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` ステートメントを実行して、特定のデータベース (たとえば、`MyDatabase`) に対してこのグループを作成する必要があります。 

データベース ユーザーが作成されると、ユーザー `steve@gmail.com` は、SSMS 認証オプション `Azure Active Directory – Universal with MFA` を使用して `MyDatabase` にサインインできます。 既定では、`usergroup` には接続権限のみがあります。 それ以上のデータ アクセス許可は、十分な特権を持つユーザーがデータベースで[付与する](/sql/t-sql/statements/grant-transact-sql)必要があります。 

> [!NOTE]
> SSMS 17.x の場合、`steve@gmail.com` をゲスト ユーザーとして使用するには、 **[AD ドメイン名またはテナント ID]** ボックスをオンにし、 **[接続プロパティ]** ダイアログ ボックスに AD ドメイン名 `contosotest.onmicrosoft.com` を追加する必要があります。 **[AD ドメイン名またはテナントの ID]** オプションは、 **[Azure Active Directory - MFA で汎用]** 認証でのみサポートされます。 それ以外の場合、このチェック ボックスはグレー表示になります。

## <a name="universal-authentication-limitations"></a>ユニバーサル認証の制限事項

- SSMS および SqlPackage.exe は、現在、Active Directory ユニバーサル認証を介して MFA 認証できる、唯一のツールです。
- SSMS バージョン 17.2 では、MFA によるユニバーサル認証を使用したマルチユーザーの同時アクセスがサポートされます。 SSMS バージョン 17.0 および 17.1 では、ユニバーサル認証を使用して SSMS のインスタンスにログインできるのは、1 つの Azure Active Directory アカウントのみに制限されています。 別の Azure AD アカウントとしてサインインするには、別の SSMS インスタンスを使用する必要があります。 この制限は Active Directory ユニバーサル認証に限定されます。`Azure Active Directory - Password`認証、`Azure Active Directory - Integrated`認証、または `SQL Server Authentication`を使用すると、別のサーバーにサインインできます。
- SSMS では、オブジェクト エクスプローラー、クエリ エディター、クエリ ストアの視覚化で Active Directory ユニバーサル認証がサポートされます。
- SSMS バージョン 17.2 では、データベースのエクスポート/抽出/データの展開を支援する DacFx ウィザードが提供されています。 ユニバーサル認証を使用して、初期認証ダイアログ ボックスで特定のユーザーが認証されると、DacFx ウィザードは他のすべての認証方法についても同じように機能します。
- SSMS テーブル デザイナーは、ユニバーサル認証をサポートしていません。
- サポートされているバージョンの SSMS を使用する必要があることを除き、Active Directory ユニバーサル認証に関する追加のソフトウェア要件はありません。  
- ユニバーサル認証対応の Active Directory 認証ライブラリ (ADAL) の最新バージョンについては、次のリンクを参照してください: [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。  

## <a name="next-steps"></a>次のステップ

- 構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](authentication-mfa-ssms-configure.md)」をご覧ください。
- 自分のデータベースへのアクセス権を他のユーザーに付与する:[SQL Database の認証と承認:アクセス権の付与](logins-create-manage.md)  
- 他のユーザーがファイアウォール経由で接続できるようにする:[Azure portal でサーバー レベルのファイアウォール規則を構成する](firewall-configure.md)  
- [SQL Database または Azure Synapse で Azure Active Directory 認証を構成して管理する](authentication-aad-configure.md)
- [Azure AD ゲスト ユーザーを作成し、Azure AD 管理者として設定する](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [BACPAC ファイルを新しいデータベースにインポートする](database-import.md)  
- [BACPAC ファイルへのデータベースのエクスポート](database-export.md)  
- C# インターフェイス [IUniversalAuthProvider インターフェイス](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- **[Azure Active Directory - MFA で汎用]** 認証を使用している場合、ADAL トレースは、[SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms) 以降で使用可能です。 ADAL トレースは既定ではオフであり、オンにするには、 **[ツール]** の **[オプション]** メニューで、 **[Azure サービス]** 、 **[Azure クラウド]** 、 **[ADAL 出力ウィンドウのトレース レベル]** の順に選んで、 **[表示]** メニューの **[出力]** を有効にします。 出力ウィンドウで **[Azure Active Directory option]\(Azure Active Directory オプション\)** を選ぶと、トレースが使用可能になります。