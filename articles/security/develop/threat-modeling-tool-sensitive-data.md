---
title: 機密データ - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での機密データの軽減策について説明します。 軽減策に関する情報とコード例をご覧ください。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0bcbe35fc6d9f104325bec8a3404ad57a6376cf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518126"
---
# <a name="security-frame-sensitive-data--mitigations"></a>セキュリティ フレーム:機密データ | 軽減策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **コンピューターの信頼の境界** | <ul><li>[機密情報を含むバイナリを難読化する](#binaries-info)</li><li>[ユーザー固有の機密データを保護するために暗号化されたファイル システム (EFS) を使うことを検討する](#efs-user)</li><li>[アプリケーションによってファイル システムに保存される機密データを暗号化する](#filesystem)</li></ul> | 
| **Web アプリケーション** | <ul><li>[機密コンテンツがブラウザーにキャッシュされないようにする](#cache-browser)</li><li>[Web アプリの構成ファイルの機密データを含むセクションを暗号化する](#encrypt-data)</li><li>[機密性の高いフォームおよび入力の autocomplete HTML 属性を明示的に無効にする](#autocomplete-input)</li><li>[ユーザーの画面に表示される機密データをマスクする](#data-mask)</li></ul> | 
| **[データベース]** | <ul><li>[動的データ マスクを実装して、権限を持たないユーザーへの機密データの露出を制限する](#dynamic-users)</li><li>[パスワードを salt ハッシュ形式で保存する](#salted-hash)</li><li>[データベースの列の機密データを暗号化する](#db-encrypted)</li><li>[データベース レベルの暗号化 (TDE) を有効にする](#tde-enabled)</li><li>[データベースのバックアップを暗号化する](#backup)</li></ul> | 
| **Web API** | <ul><li>[Web API に関連する機密データがブラウザーの記憶域に保存されないようにする](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Azure Cosmos DB に保存される機密データを暗号化する](#encrypt-docdb)</li></ul> | 
| **Azure IaaS VM の信頼の境界** | <ul><li>[Virtual Machines によって使われるディスクを、Azure Disk Encryption を使って暗号化する](#disk-vm)</li></ul> | 
| **Service Fabric の信頼の境界** | <ul><li>[Service Fabric アプリケーションでシークレットを暗号化する](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[セキュリティ モデリングを実行し、必要に応じて部署/チームを使う](#modeling-teams)</li><li>[重要なエンティティでの共有機能へのアクセスを最小限にする](#entities)</li><li>[Dynamics CRM の共有機能に関連するリスクおよび適切なセキュリティ プラクティスについてユーザーをトレーニングする](#good-practices)</li><li>[例外管理での構成の詳細の表示を禁止する開発標準規則を含める](#exception-mgmt)</li></ul> | 
| **Azure ストレージ** | <ul><li>[Azure Storage Service Encryption (SSE) for Data at Rest (プレビュー) を使う](#sse-preview)</li><li>[クライアント側暗号化を使って、Azure Storage に機密データを保存する](#client-storage)</li></ul> | 
| **モバイル クライアント** | <ul><li>[携帯電話のローカル ストレージに書き込まれた機密データまたは PII データを暗号化する](#pii-phones)</li><li>[生成されたバイナリをエンド ユーザーに配布する前に難読化する](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[clientCredentialType を Certificate または Windows に設定する](#cert)</li><li>[WCF セキュリティ モードを有効にしない](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>機密情報を含むバイナリを難読化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 取引上の秘密や、リバース エンジニアリングされてはならない機密のビジネス ロジックなどの機密情報が含まれているバイナリを、難読化します。 これは、アセンブリのリバース エンジニアリングを停止するためです。 この目的には、`CryptoObfuscator` などのツールを使うことができます。 |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>ユーザー固有の機密データを保護するために暗号化されたファイル システム (EFS) を使うことを検討する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | コンピューターに物理的にアクセスする敵対者からユーザー固有の機密データを保護するために、暗号化されたファイル システム (EFS) を使うことを検討します。 |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>アプリケーションによってファイル システムに保存される機密データを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | EFS を強制できない場合は、アプリケーションによってファイル システムに保存される機密データを暗号化します (DPAPI などを使用)。 |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>機密コンテンツがブラウザーにキャッシュされないようにする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、Web フォーム、MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ブラウザーは、キャッシュと履歴のために情報を保存できます。 これらのキャッシュされたファイルはフォルダーに保存されます (Internet Explorer の場合は Temporary Internet Files フォルダー)。 これらのページが再び参照されると、ブラウザーはキャッシュからページを表示します。 機密情報 (住所、クレジット カードの詳細、社会保障番号、ユーザー名など) がユーザーに表示された場合、この情報がブラウザーのキャッシュに保存されるので、ブラウザーのキャッシュを調べることで、または単にブラウザーの [戻る] ボタンをクリックすることで、情報を取得できる可能性があります。 すべてのページについて、cache-control 応答ヘッダーの値を "no-store" に設定します。 |

### <a name="example"></a>例
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-store" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>例
これは、フィルターによって実装できます。 次の例を使うことができる場合があります。 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Web アプリの構成ファイルの機密データを含むセクションを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [方法: DPAPI を使用して ASP.NET 2.0 内の構成セクションを暗号化する方法](/previous-versions/msp-n-p/ff647398(v=pandp.10))、[保護された構成プロバイダーの指定](/previous-versions/68ze1hb2(v=vs.140))、[Azure Key Vault を使用してアプリケーション シークレットを保護する](/azure/architecture/multitenant-identity/web-api) |
| **手順** | Web.config、appsettings.json などの構成ファイルは、一般的に、ユーザー名、パスワード、データベース接続文字列、暗号化キーなどの機密情報の保持に使用されます。 この情報を保護しないと、アプリケーションは、アカウントのユーザー名とパスワード、データベース名、サーバー名などの機密情報を取得する、攻撃者や悪意のあるユーザーに対して脆弱になります。 構成ファイルの重要なセクションは、デプロイメント タイプ (azure/on-prem) に基づいて、DPAPI または Azure Key Vault などのサービスを使用して暗号化してください。 |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>機密性の高いフォームおよび入力の autocomplete HTML 属性を明示的に無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [MSDN: autocomplete 属性](https://msdn.microsoft.com/library/ms533486(VS.85).aspx)、[HTML での AutoComplete の使用](/previous-versions/windows/internet-explorer/ie-developer/)、[HTML サニタイズの脆弱性](/security-updates/SecurityBulletins/2010/ms10-071)、[オートコンプリートをまた有効にするのですか](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **手順** | autocomplete 属性は、フォームがオートコンプリートをオンまたはオフにするかどうかを指定します。 オートコンプリートがオンのとき、ブラウザーは前にユーザーが入力した値に基づいて値を自動的に完成させます。 たとえば、新しい名前とパスワードがフォームに入力されて、フォームが送信されるとき、ブラウザーはパスワードを保存する必要があるかどうかを尋ねます。その後、フォームが表示されるとき、名前とパスワードは自動的に入力されるか、または名前が入力されると完成されます。 ローカルにアクセスできる攻撃者は、ブラウザーのキャッシュからクリア テキストのパスワードを入手する可能性があります。 既定ではオートコンプリートは有効なので、明示的に無効にする必要があります。 |

### <a name="example"></a>例
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>ユーザーの画面に表示される機密データをマスクする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | パスワード、クレジット カード番号、SSN などの機密データは、画面に表示するときにマスクする必要があります。 これは、承認されていない人物がデータにアクセスするのを防ぐためです (たとえば、サポート担当者が表示しているユーザーの SSN 番号を肩越しに見る、など)。 これらのデータ要素がプレーン テキストで表示されず、適切にマスクされるようにします。 このような対処は、入力を受け付けるときだけでなく (例: input type = "password")、画面に再表示するときも行う必要があります (例: クレジット カード番号の末尾 4 桁だけを表示する)。 |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>動的データ マスクを実装して、権限を持たないユーザーへの機密データの露出を制限する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure、OnPrem |
| **属性**              | SQL バージョン - V12、SQL バージョン - MsSQL2016 |
| **参照**              | [動的なデータ マスキング](/sql/relational-databases/security/dynamic-data-masking) |
| **手順** | 動的データ マスクの目的は、アクセスすべきではないユーザーがデータを閲覧することを防ぎ、デリケートなデータの公開を制限することにあります。 動的データ マスクは、ユーザーが直接データベースに接続し、徹底的なクエリを実行して、デリケートなデータの漏えいを防ぐことを目的としてはいません。 動的データ マスクは SQL Server の他のセキュリティ機能 (監査、暗号化、行レベルのセキュリティなど) を補完するものであり、この機能をそれらと併用して、データベースの機密データの保護を強化することを強くお勧めします。 この機能は SQL Server 2016 以降と Azure SQL Database によってのみサポートされることに注意してください。 |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>パスワードを salt ハッシュ形式で保存する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [.NET Crypto API を使ったパスワードのハッシュ](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **手順** | カスタム ユーザー ストア データベースにパスワードを保存してはなりません。 パスワード ハッシュは、代わりに salt 値を使って保存する必要があります。 ユーザーの salt が常に一意であることを確認し、パスワードを保存する前に 150,000 以上の作業因子反復回数で b-crypt、s-crypt、または PBKDF2 を適用して、ブルート フォース攻撃を受けないようにします。| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>データベースの列の機密データを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | SQL バージョン - すべて |
| **参照**              | [SQL Server での機微なデータの暗号化](/previous-versions/sql/sql-server-2008-r2/ff848751(v=sql.105))、[方法: SQL Server でのデータの列の暗号化](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)、[証明書による暗号化](/sql/t-sql/functions/encryptbycert-transact-sql) |
| **手順** | クレジット カード番号などの機密データは、データベースで暗号化する必要があります。 データは、列レベルの暗号化を使って、または暗号化関数を使ってアプリケーション機能により、暗号化できます。 |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>データベース レベルの暗号化 (TDE) を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [SQL Server の透過的なデータ暗号化 (TDE) について](/previous-versions/sql/sql-server-2008-r2/bb934049(v=sql.105)) |
| **手順** | SQL Server の Transparent Data Encryption (TDE) 機能は、データベースの機密データを暗号化し、証明書でデータを暗号化するために使われるキーを保護するのに役立ちます。 これにより、キーを持たないユーザーはデータを使うことができません。 TDE では、"静止した" データ、つまりデータとログ ファイルが保護されます。 この暗号化は、法律、規制、およびさまざまな業界で確立されているガイドラインの多くに準拠できるようになっています。 |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>データベースのバックアップを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure、OnPrem |
| **属性**              | SQL バージョン - V12、SQL バージョン - MsSQL2014 |
| **参照**              | [SQL データベース バックアップの暗号化](/sql/relational-databases/backup-restore/backup-encryption) |
| **手順** | SQL Server には、バックアップの作成中にデータを暗号化する機能があります。 バックアップを作成するときに暗号化アルゴリズムと暗号化機能 (証明書または非対称キー) を指定することで、暗号化されたバックアップ ファイルを作成できます。 |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Web API に関連する機密データがブラウザーの記憶域に保存されないようにする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC 5、MVC 6 |
| **属性**              | ID プロバイダー - ADFS、ID プロバイダー - Azure AD |
| **参照**              | 該当なし  |
| **手順** | <p>一部の実装では、Web API の認証に関連する機密性の高いアーティファクトがブラウザーのローカル記憶域に格納されます。 たとえば、adal.idtoken、adal.nonce.idtoken、adal.access.token.key、adal.token.keys、adal.state.login、adal.session.state、adal.expiration.key などの Azure AD 認証アーティファクトです。</p><p>これらのアーティファクトはすべて、サインアウトした後またはブラウザーを終了した後でも使用可能です。 敵対者がこれらのアーティファクトにアクセスできた場合、それを再利用して保護されたリソース (API) にアクセスできます。 Web API に関連するすべての機密アーティファクトが、ブラウザーの記憶域に保存されないようにします。 クライアント側に記憶することが避けられない場合は (たとえば、Implicit OpenIdConnect/OAuth フローを利用するシングル ページ アプリケーション (SPA) は、アクセス トークンをローカルに保存する必要があります)、永続性のない記憶域の選択肢を使います。 たとえば、LocalStorage ではなく SessionStorage を選びます。</p>| 

### <a name="example"></a>例
次の JavaScript のスニペットは、ローカル記憶域に認証アーティファクトを保存するカスタム認証ライブラリのものです。 このような実装は避ける必要があります。 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Cosmos DB に保存される機密データを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Document DB | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ドキュメント DB に保存する前にアプリケーション レベルで機密データを暗号化するか、または Azure Storage や Azure SQL のような他のストレージ ソリューションに機密データを保存します。| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Virtual Machines によって使われるディスクを、Azure Disk Encryption を使って暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure IaaS VM の信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Disk Encryption を使用して仮想マシンに使用されるディスクを暗号化する](../../storage/blobs/security-recommendations.md#data-protection) |
| **手順** | <p>Azure Disk Encryption は、現在プレビュー段階の新しい機能です。 この機能を使用すると、IaaS Virtual Machine に使用される OS ディスクとデータ ディスクを暗号化できます。 Windows の場合、ドライブの暗号化には、業界標準の BitLocker 暗号化テクノロジが使用されます。 Linux の場合、ディスクの暗号化には DM-Crypt テクノロジが使用されます。 DM-Crypt は Azure Key Vault と統合されているので、ディスクの暗号化キーを制御および管理できます。 Azure Disk Encryption ソリューションでは、次の 3 つのユーザー暗号化シナリオがサポートされています。</p><ul><li>ユーザーが暗号化した VHD ファイルおよびユーザーが提供した暗号化キーから作成した新しい IaaS VM で暗号化を有効にして、キーを Azure Key Vault に保存します。</li><li>Azure Marketplace から作成された新しい IaaS VM での暗号化を有効にします。</li><li>Azure で既に実行されている既存の IaaS VM での暗号化を有効にします。</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Service Fabric アプリケーションでシークレットを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure |
| **参照**              | [Service Fabric アプリケーションでのシークレットの管理](../../service-fabric/service-fabric-application-secret-management.md) |
| **手順** | シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。 Azure Key Vault を使って、Service Fabric アプリケーションのキーやシークレットを管理します。 |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>セキュリティ モデリングを実行し、必要に応じて部署/チームを使う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | セキュリティ モデリングを実行し、必要に応じて部署/チームを使います。 |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>重要なエンティティでの共有機能へのアクセスを最小限にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 重要なエンティティでの共有機能へのアクセスを最小限にします。 |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Dynamics CRM の共有機能に関連するリスクおよび適切なセキュリティ プラクティスについてユーザーをトレーニングする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | Dynamics CRM の共有機能に関連するリスクおよび適切なセキュリティ プラクティスについてユーザーをトレーニングします。 |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>例外管理での構成の詳細の表示を禁止する開発標準規則を含める

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 開発外部の例外管理で構成の詳細の表示を禁止する開発標準規則を含めます。 コード レビューまたは定期的な検査の一部として、これをテストします。|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Azure Storage Service Encryption (SSE) for Data at Rest (プレビュー) を使う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | StorageType - BLOB |
| **参照**              | [Azure Storage Service Encryption for Data at Rest (プレビュー)](../../storage/common/storage-service-encryption.md) |
| **手順** | <p>Azure Storage Service Encryption (SSE) for Data at Rest は、データの安全性を保護して組織のセキュリティおよびコンプライアンス要件を満たすのに役立ちます。 この機能を使用すると、Azure Storage はストレージに保存する前にデータを自動的に暗号化し、取得する前に復号化します。 暗号化、復号化、キーの管理は、ユーザーにはまったく意識されずに行われます。 SSE は、ブロック BLOB、ページ BLOB、および追加 BLOB にのみ適用されます。 テーブル、キュー、ファイルなど、他の種類のデータは暗号化されません。</p><p>暗号化と復号化のワークフロー:</p><ul><li>ユーザーがストレージ アカウントで暗号化を有効にします</li><li>ユーザーが Blob Storage に新しいデータを書き込むと (PUT Blob、PUT Block、PUT Page など)、使用可能なものの中で最も強力なブロック暗号化の 1 つである 256 ビット AES 暗号化を使って、すべての書き込みが暗号化されます</li><li>ユーザーがデータにアクセスすると (GET Blob など)、データは自動的に復号化されてユーザーに返されます</li><li>暗号化が無効になっている場合は、新しい書き込みは暗号化されず、既存の暗号化されたデータはユーザーが書き込み直すまで暗号化された状態のままになります。 暗号化が有効になっている間、Blob Storage への書き込みは暗号化されます。 ユーザーがストレージ アカウントの暗号化の有効/無効を切り替えても、データの状態は変わりません</li><li>すべての暗号化キーは、Microsoft によって保管、暗号化、管理されます</li></ul><p>現時点では暗号化に使われるキーは Microsoft が管理していることに注意してください。 Microsoft は、社内ポリシーの定義に従って、キーを生成し、キーの安全な保存と定期的な循環を管理しています。 将来的には、ユーザーが独自の暗号化キーを管理し、Microsoft が管理するキーからユーザーが管理するキーに移行する機能を追加する予定です。</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>クライアント側暗号化を使って、Azure Storage に機密データを保存する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../../storage/common/storage-client-side-encryption.md)、[チュートリアル: Azure Key Vault を使用した Microsoft Azure Storage 内の BLOB の暗号化と復号化](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)、[Azure 暗号化拡張機能で Azure Blob Storage にデータを安全に保存する](/archive/blogs/partnercatalystteam/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions) |
| **手順** | <p>.NET Nuget パッケージ用 Azure Storage クライアント ライブラリは、開発者が Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化し、クライアントにダウンロードするときにデータを復号化する作業を支援します。 また、このライブラリは Azure Key Vault との統合にも役立ち、ストレージ アカウント キー管理に利用することができます。 ここでは、クライアント側暗号化のしくみを簡単に説明します。</p><ul><li>Azure ストレージ クライアント SDK は、1 回使用の対称キーであるコンテンツ暗号化キー (CEK) を生成します</li><li>ユーザー データは、この CEK を使用して暗号化されます</li><li>CEK は、キー暗号化キー (KEK) を使用してラップ (暗号化) されます。 KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルに管理することも、Azure Key Vault に保存することもできます。 Storage クライアント自体が KEK にアクセスすることはありません。 クライアントは、Key Vault によって提供されるキー ラップ アルゴリズムを呼び出すだけです。 ユーザーは、必要に応じてキー ラップ/ラップ解除にカスタム プロバイダーを使うことができます</li><li>暗号化されたデータは、Azure Storage サービスにアップロードされます。 細かなレベルの実装の詳細については、「参照」セクションのリンクを参照してください。</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>携帯電話のローカル ストレージに書き込まれた機密データまたは PII データを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | モバイル クライアント | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、Xamarin  |
| **属性**              | 該当なし  |
| **参照**              | [Microsoft Intune ポリシーを使用してデバイスの設定と機能を管理する](/mem/intune/configuration/)、[Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **手順** | <p>アプリケーションがユーザーの PII (メール アドレス、電話番号、名、姓、個人設定など) のような機密情報をモバイル デバイスのファイル システムに書き込む場合、ローカル ファイル システムに書き込む前に暗号化する必要があります。 アプリケーションがエンタープライズ アプリケーションの場合は、Windows Intune を使ってアプリケーションを発行する可能性を調査します。</p>|

### <a name="example"></a>例
Intune は、機密データを保護するように次のセキュリティ ポリシーで構成できます。 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>例
アプリケーションがエンタープライズ アプリケーションではない場合は、ファイル システムに対して実行できる暗号化操作を使い、プラットフォームが提供するキーストアとキーチェーンを使って暗号化キーを保存します。 次のコード スニペットでは、xamarin を使ってキーチェーンからキーにアクセスする方法を示します。 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>生成されたバイナリをエンド ユーザーに配布する前に難読化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | モバイル クライアント | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Crypto Obfuscation For .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **手順** | 生成されるバイナリ (apk 内のアセンブリ) は、アセンブリのリバース エンジニアリングを止めるために難読化する必要があります。この目的には、`CryptoObfuscator` などのツールを使うことができます。 |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>clientCredentialType を Certificate または Windows に設定する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **手順** | 暗号化されていないチャネルを介してプレーン テキスト パスワードで UsernameToken を使うと、攻撃者にパスワードが暴露され、SOAP メッセージを傍受できます。 UsernameToken を使うサービス プロバイダーは、プレーン テキストで送信されるパスワードを受け付ける場合があります。 暗号化されていないチャネル経由でプレーン テキスト パスワードを送信すると、資格情報が攻撃者に暴露されて SOAP メッセージを傍受できるようになります。 | 

### <a name="example"></a>例
次の WCF サービス プロバイダーの構成では、UsernameToken を使っています。 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
clientCredentialType を Certificate または Windows に設定してください。 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF セキュリティ モードを有効にしない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | セキュリティ モード - トランスポート、セキュリティ モード - メッセージ |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference)、[WCF セキュリティの基礎 CoDe マガジン](https://www.codemag.com/article/0611051) |
| **手順** | トランスポートまたはメッセージのセキュリティが定義されていません。 トランスポートまたはメッセージのセキュリティなしでメッセージを送信するアプリケーションは、メッセージの機密性または整合性を保証できません。 WCF セキュリティのバインドが None に設定されている場合、トランスポートとメッセージのセキュリティはどちらも無効になります。 |

### <a name="example"></a>例
次の構成は、セキュリティ モードを None に設定します。 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>例
すべてのサービス バインドのセキュリティ モードは 5 つです。 
* [なし] : セキュリティをオフにします。 
* Transport。 相互認証とメッセージ保護のためにトランスポート セキュリティを使います。 
* Message. 相互認証とメッセージ保護のためにメッセージ セキュリティを使います。 
* Both。 トランスポート レベルとメッセージ レベルのセキュリティの設定を指定できます (MSMQ のみがこれをサポートします)。 
* TransportWithMessageCredential。 メッセージとメッセージ保護で資格情報が渡され、サーバー認証はトランスポート層で提供されます。 
* TransportCredentialOnly。 トランスポート層でクライアントの資格情報が渡され、メッセージ保護は適用されません。 トランスポートとメッセージのセキュリティを使って、メッセージの機密性と整合性を保護します。 次の構成は、メッセージ資格情報でトランスポート セキュリティを使うようサービスに指示します。
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```