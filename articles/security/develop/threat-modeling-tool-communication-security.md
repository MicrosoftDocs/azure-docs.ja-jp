---
title: Microsoft Threat Modeling Tool の通信セキュリティ
titleSuffix: Azure
description: Threat Modeling Tool で公開されている通信セキュリティの脅威に対する軽減策について説明します。 軽減策の情報を参照し、コード例を確認します。
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
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517531"
---
# <a name="security-frame-communication-security--mitigations"></a>セキュリティ フレーム:通信のセキュリティ | 軽減策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[SSL/TLS を使用して Event Hub への通信をセキュリティで保護する](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[サービス アカウントの権限を確認し、カスタム サービスまたは ASP.NET ページで CRM のセキュリティが考慮されていることをチェックする](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[オンプレミス SQL Server を Azure Data Factory に接続しているときはデータ管理ゲートウェイを使用する](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Identity Server へのすべてのトラフィックで HTTPS 接続が使用されていることを確認する](#identity-https)</li></ul> |
| **Web アプリケーション** | <ul><li>[SSL、TLS、および DTLS 接続の認証に X.509 証明書が使用されていることを確認する](#x509-ssltls)</li><li>[Azure App Service でカスタム ドメインに対して TLS/SSL 証明書を構成する](#ssl-appservice)</li><li>[Azure App Service へのすべてのトラフィックに HTTPS 接続を強制する](#appservice-https)</li><li>[HTTP Strict Transport Security (HSTS) を有効にする](#http-hsts)</li></ul> |
| **[データベース]** | <ul><li>[SQL Server 接続の暗号化と証明書の検証を確認する](#sqlserver-validation)</li><li>[SQL サーバーへの通信を強制的に暗号化する](#encrypted-sqlserver)</li></ul> |
| **Azure ストレージ** | <ul><li>[Azure Storage への通信が HTTPS 経由であることを確認する](#comm-storage)</li><li>[HTTPS を有効にできない場合は、BLOB のダウンロード後に MD5 ハッシュを検証する](#md5-https)</li><li>[SMB 3.0 対応クライアントを使用して Azure ファイル共有に転送中のデータの暗号化を確認する](#smb-shares)</li></ul> |
| **モバイル クライアント** | <ul><li>[証明書のピン留めを実装する](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS - セキュリティで保護されたトランスポート チャネルを有効にする](#https-transport)</li><li>[WCF: メッセージのセキュリティ保護レベルを EncryptAndSign に設定する](#message-protection)</li><li>[WCF: 最小権限のアカウントを使用して WCF サービスを実行する](#least-account-wcf)</li></ul> |
| **Web API** | <ul><li>[Web API へのすべてのトラフィックに HTTPS 接続を強制する](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Azure Cache for Redis への通信が TLS 経由であることを確認する](#redis-ssl)</li></ul> |
| **IoT フィールド ゲートウェイ** | <ul><li>[デバイスからフィールド ゲートウェイへの通信をセキュリティで保護する](#device-field)</li></ul> |
| **IoT クラウド ゲートウェイ** | <ul><li>[デバイスからクラウド ゲートウェイへの通信を SSL/TLS を使用してセキュリティで保護する](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>SSL/TLS を使用して Event Hub への通信をセキュリティで保護する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Event Hub | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Event Hubs の認証とセキュリティ モデルの概要](../../event-hubs/authenticate-shared-access-signature.md) |
| **手順** | SSL/TLS を使用して Event Hub への AMQP または HTTP 接続をセキュリティで保護します |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>サービス アカウントの権限を確認し、カスタム サービスまたは ASP.NET ページで CRM のセキュリティが考慮されていることをチェックする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | サービス アカウントの権限を確認し、カスタム サービスまたは ASP.NET ページで CRM のセキュリティが考慮されていることをチェックします |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>オンプレミス SQL Server を Azure Data Factory に接続しているときはデータ管理ゲートウェイを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Data Factory | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | リンクされたサービスの種類 - Azure とオンプレミス |
| **参照**              |[オンプレミスと Azure Data Factory の間でデータを移動する](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway)、[データ管理ゲートウェイ](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **手順** | <p>データ管理ゲートウェイ (DMG) ツールは、企業ネットワークまたはファイアウォールの背後で保護されているデータ ソースに接続するときに必要です。</p><ol><li>コンピューターをロックダウンすると、DMG ツールが切り離され、正常に動作していないプログラムによって、データ ソース コンピューターが破損したりスヌーピング (のぞき見) されたりするのを防ぐことができます  ( 最新の更新プログラムをインストールする必要がある、最低限必要なポートを有効にする、制御されたアカウントのプロビジョニング、監査の有効化、ディスク暗号化の有効化など)。</li><li>データ ゲートウェイのキーは頻繁に、または DMG サービス アカウントのパスワードが更新されるたびに交換する必要があります</li><li>リンク サービス経由のデータ転送は暗号化する必要があります</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Identity Server へのすべてのトラフィックで HTTPS 接続が使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IdentityServer3 - キー、署名、および暗号化](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html)、[IdentityServer3 - デプロイ](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **手順** | 既定では、IdentityServer の着信接続はすべて、HTTPS 経由である必要があります。 IdentityServer との通信は、セキュリティで保護されたトランスポートのみを介していなければなりません。 ただし、TLS オフロードのように、この要件が緩和されるデプロイ シナリオもいくつかあります。 詳細については、「参照」の Identity Server デプロイのページを参照してください。 |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>SSL、TLS、および DTLS 接続の認証に X.509 証明書が使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>SSL、TLS、または DTLS を使用するアプリケーションは、接続先エンティティの X.509 証明書を完全に検証する必要があります。 これには、以下の証明書の検証が含まれます。</p><ul><li>ドメイン名</li><li>有効期間 (開始日と有効期限)</li><li>失効状態</li><li>使用状況 (サーバー認証 (サーバーの場合)、クライアント認証 (クライアントの場合) など)</li><li>信頼チェーン。 プラットフォームによって信頼されている、または管理者によって明示的に構成されているルート証明機関 (CA) に、証明書がチェーンされている必要があります</li><li>証明書の公開キーの長さが 2048 ビットを超えていること</li><li>ハッシュ アルゴリズムが SHA256 以上であること |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Azure App Service でカスタム ドメインに対して TLS/SSL 証明書を構成する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | EnvironmentType - Azure |
| **参照**              | [アプリに対する HTTPS を Azure App Service で有効にする](../../app-service/configure-ssl-bindings.md) |
| **手順** | Azure の既定では、*.azurewebsites.net ドメインのワイルドカード証明書を使用するすべてアプリに対して、HTTPS を利用できます。 ただし、すべてのワイルドカード ドメインと同様に、カスタム ドメインに独自の証明書を使用する場合ほど安全ではありません ([こちら](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)を参照)。 デプロイされたアプリへのアクセスに使用するカスタム ドメインに対して、TLS を有効にすることをお勧めします|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Azure App Service へのすべてのトラフィックに HTTPS 接続を強制する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | EnvironmentType - Azure |
| **参照**              | [Azure App Service に HTTPS を適用する](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **手順** | <p>Azure では、*.azurewebsites.net ドメインのワイルドカード証明書を使用する Azure App Service に対して、HTTPS が既に有効になっていますが、適用されていません。 訪問者は引き続き HTTP を使用してアプリにアクセスするため、アプリのセキュリティが侵害される可能性があります。したがって HTTPS を明示的に適用する必要があります。 ASP.NET MVC アプリケーションは、セキュリティで保護されていない HTTP 要求が HTTPS 経由で再送信されるように強制する、[RequireHttps フィルター](/dotnet/api/system.web.mvc.requirehttpsattribute)を使用する必要があります。</p><p>また、Azure App Service に組み込まれている URL 書き換えモジュールを使用して、HTTPS を適用することもできます。 URL 書き換えモジュールを使用すると、アプリケーションに渡す前に受信要求に適用するルールを開発者が定義できます。 URL 書き換えルールは、アプリケーションのルートに格納されている web.config ファイルで定義されます</p>|

### <a name="example"></a>例
次の例には、すべての受信トラフィックに HTTPS の使用を強制する基本的な URL 書き換えルールが含まれています
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
このルールは、ユーザーが HTTP を使用してページを要求したときに HTTP 状態コード 301 (永続的なリダイレクト) を返すことで動作します。 301 は、訪問者が要求した URL と同じ URL へ要求をリダイレクトしますが、要求の HTTP 部分は HTTPS で置き換えられます。 たとえば、`HTTP://contoso.com` は `HTTPS://contoso.com` に制限されます。 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>HTTP Strict Transport Security (HSTS) を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [OWASP HTTP Strict Transport Security チート シートを有効にする](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **手順** | <p>HTTP Strict Transport Security (HSTS) は、特別な応答ヘッダーを使用して Web アプリケーションで指定されるオプトイン セキュリティ拡張機能です。 サポートされているブラウザーがこのヘッダーを受け取ると、そのブラウザーでは、指定したドメインへの HTTP 経由の通信を送信できなくなり、代わりにすべての通信が HTTPS 経由で送信されます。 HTTPS クリックスルー メッセージもブラウザーに表示されなくなります。</p><p>HSTS を実装するには、コードまたは config で、応答ヘッダー Strict-Transport-Security: max-age=300; includeSubDomains を、Web サイトに対してグローバルに構成する必要があります。HSTS では、次の脅威に対応します。</p><ul><li>ユーザーは `https://example.com` をブックマークするか手動で入力し、中間者攻撃を受ける。HSTS は HTTP 要求をターゲット ドメインの HTTPS に自動的にリダイレクトします。</li><li>HTTPS のみを目的とした Web アプリケーションで、うっかり HTTP リンクを含めてしまうかまたは HTTP 経由でコンテンツを提供する。HSTS は HTTP 要求をターゲット ドメインの HTTPS に自動的にリダイレクトします。</li><li>中間者攻撃で、無効な証明書を使用して攻撃対象ユーザーからのトラフィックを傍受しようとしており、ユーザーに不正な証明書を受け入れさせようとしている。HSTS は、無効な証明書のメッセージを上書きするユーザーを許可しません。</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>SQL Server 接続の暗号化と証明書の検証を確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure  |
| **属性**              | SQL バージョン - V12 |
| **参照**              | [SQL Database 用のセキュリティで保護された接続文字列の書き込みに関するベスト プラクティス](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **手順** | <p>SQL Database とクライアント アプリケーションの間の通信はすべて、以前は Secure Sockets Layer (SSL) と呼ばれていた Transport Layer Security (TLS) を使用して常に暗号化されます。 SQL Database では、暗号化されていない接続はサポートされません。 アプリケーション コードやツールで証明書を検証するには、暗号化された接続を明示的に要求し、サーバー証明書は信頼しないようにします。 アプリケーション コードやツールが、暗号化された接続を要求しない場合でも、暗号化された接続を受け付けることはできます</p><p>ただし、サーバー証明書は検証されず、"man in the middle" 攻撃を受けやすくなります。 ADO.NET アプリケーション コードで証明書を検証するには、データベース接続文字列で `Encrypt=True` と `TrustServerCertificate=False` を設定します。 SQL Server Management Studio を使用して証明書を検証するには、[サーバーに接続] ダイアログ ボックスを開きます。 [接続プロパティ] タブの [暗号化接続] をクリックします</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>SQL サーバーへの通信を強制的に暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | OnPrem |
| **属性**              | SQL バージョン - MsSQL2016、SQL バージョン - MsSQL2012、SQL バージョン - MsSQL2014 |
| **参照**              | [データベース エンジンへの暗号化接続の有効化](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **手順** | TLS 暗号化を有効にすると、SQL Server のインスタンスとアプリケーションの間で行われるネットワーク経由データ転送のセキュリティが向上します。 |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Azure Storage への通信が HTTPS 経由であることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Storage トランスポート レベルの暗号化 - HTTPS の使用](../../storage/blobs/security-recommendations.md#networking) |
| **手順** | 転送中の Azure Storage データのセキュリティを確保するには、REST API を呼び出すとき、またはストレージのオブジェクトにアクセスするときに必ず HTTPS プロトコルを使用します。 また、Azure Storage オブジェクトへのアクセス権を委任するときに使用できる Shared Access Signatureには、Shared Access Signature の使用時には HTTPS プロトコルのみを使用できると指定するオプションがあるので、誰でも SAS トークンを指定したリンクを送信すると適切なプロトコルが使用されます。|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>HTTPS を有効にできない場合は、BLOB のダウンロード後に MD5 ハッシュを検証する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | StorageType - BLOB |
| **参照**              | [Windows Azure BLOB MD5 の概要](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **手順** | <p>Windows Azure Blob service は、アプリケーション層とトランスポート層の両方のデータ整合性を確保するためのメカニズムを提供します。 何らかの理由で HTTPS ではなく HTTP を使用する必要があり、ブロック BLOB を使用している場合、MD5 チェックを使用して、転送中の BLOB の整合性を検証することができます</p><p>これは、ネットワーク/トランスポート層のエラーからの保護には役立ちますが、中間攻撃には必ずしも役立つとは言えません。 トランスポート レベルのセキュリティを提供する HTTPS を使用できる場合は、MD5 チェックを使用することは冗長となり、不要です。</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>SMB 3.0 対応クライアントを使用して Azure ファイル共有に転送中のデータの暗号化を確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | モバイル クライアント | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | StorageType - ファイル |
| **参照**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931)、[Windows クライアントでの Azure File Storage SMB のサポート](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **手順** | Azure File Storage は、REST API の使用時に HTTPS をサポートしていますが、VM にアタッチされる SMB ファイル共有として使用する方が一般的です。 SMB 2.1 は暗号化をサポートしていないので、Azure の同じリージョン内でのみ接続が許可されます。 一方、SMB 3.0 は暗号化をサポートしており、Windows Server 2012 R2、Windows 8、Windows 8.1、および Windows 10 で使用できるので、リージョンをまたがるアクセスや、デスクトップ上のアクセスも許可されます。 |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>証明書のピン留めを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、Windows Phone |
| **属性**              | 該当なし  |
| **参照**              | [証明書と公開キーのピン留め](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **手順** | <p>証明書のピン留めは Man-In-The-Middle (MITM) 攻撃に対する防御策で、 ホストを、予想される X509 証明書または公開キーに関連付けます。 ホストに認識または表示された証明書や公開キーは、そのホストに関連付けられます。つまり、"ピン留め" されます。 </p><p>TLS ハンドシェイク中に 敵が TLS MITM 攻撃を実行しようとしたとき、攻撃者のサーバーのキーが、ピン留めされた証明書のキーが異なっていると、その要求は破棄されます。したがって、MITM 証明書のピン留めを回避するには、ServicePointManager の `ServerCertificateValidationCallback` 委任を実装します。</p>|

### <a name="example"></a>例
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>HTTPS - セキュリティで保護されたトランスポート チャネルを有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **手順** | アプリケーションは、機密情報へのすべてのアクセスに対して HTTPS が確実に使用されるよう構成されている必要があります。<ul><li>**説明:** アプリケーションが機密情報を処理しており、メッセージ レベルの暗号化が使用されていない場合は、暗号化されたトランスポート チャネル経由での通信のみを許可します。</li><li>**推奨:** HTTP トランスポートが無効になっていることを確認し、代わりに HTTPS トランスポートを有効にします。 たとえば、`<httpTransport/>` を `<httpsTransport/>` タグに置き換えます。 アプリケーションへのアクセス チャネルを確実にセキュリティで保護したい場合は、ネットワーク構成 (ファイアウォール) には依存しないでください。 論理的に言えば、アプリケーションのセキュリティをネットワークに依存するべきではありません。</li></ul><p>実際的な観点から言うと、ネットワーク セキュリティ担当者は、アプリケーションの進化に伴うアプリケーションのセキュリティ要件を常に把握しているわけではありません。</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: メッセージのセキュリティ保護レベルを EncryptAndSign に設定する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **手順** | <ul><li>**説明:** 保護レベルが "none" に設定されていると、メッセージの保護が無効になります。 機密性と整合性を実現するには、適切な設定レベルを使用します。</li><li>**推奨:**<ul><li>`Mode=None` の場合 - メッセージ保護を無効にします</li><li>`Mode=Sign` の場合 - メッセージに署名します。ただし、暗号化しません。データの整合性が重要である場合に使用します</li><li>`Mode=EncryptAndSign` の場合 - メッセージに署名し、暗号化します</li></ul></li></ul><p>機密性については心配がなく、情報の整合性のみを検証する場合は、暗号化を無効にして、メッセージへの署名のみを検討してください。 送信者を検証する必要があるが、機密データが送信されない操作またはサービス コントラクトについては、これが役に立つ場合があります。 保護レベルを下げるときは、個人データがメッセージに含まれないように注意してください。</p>|

### <a name="example"></a>例
次の例では、メッセージへの署名のみを行うようにサービスと操作を構成します。 `ProtectionLevel.Sign` のサービス コントラクトの例: サービス コントラクト レベルで ProtectionLevel.Sign を使用する例を次に示します。 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>例
`ProtectionLevel.Sign` の操作コントラクトの例 (詳細な制御): 次に示すのは、`ProtectionLevel.Sign`OperationContract レベルでの使用例です。

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: 最小権限のアカウントを使用して WCF サービスを実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **手順** | <ul><li>**説明:** 管理者または高い権限を持つアカウントで WCF サービスを実行しないでください。 サービスが侵害された場合、その影響が大きくなります。</li><li>**推奨:** 最小権限のアカウントを使用して WCF サービスをホストします。これにより、アプリケーションの攻撃対象領域を抑え、攻撃を受けた場合の損害の可能性を低くすることができます。 サービス アカウントに、MSMQ、イベント ログ、パフォーマンス カウンター、ファイル システムなどのインフラストラクチャ リソースに対する追加のアクセス権が必要な場合は、WCF サービスが正常に実行されるように、こうしたリソースに適切なアクセス許可を付与する必要があります。</li></ul><p>使用しているサービスが、最初の呼び出し元に代わって特定のリソースにアクセスする必要がある場合は、偽装と委任を使用して、ダウンストリームの承認チェックのために呼び出し元の ID をフローします。 開発シナリオでは、ローカル ネットワーク サービス アカウントを使用してください。これは、権限が制限された特別な組み込みアカウントです。 運用環境のシナリオでは、最小権限のカスタム ドメイン サービス アカウントを作成します。</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Web API へのすべてのトラフィックに HTTPS 接続を強制する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [Web API コント ローラーでの SSL の適用](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **手順** | アプリケーションに HTTPS と HTTP の両方のバインドがある場合、クライアントは引き続き HTTP を使用してサイトにアクセスできます。 これを防ぐには、アクション フィルターを使用して、保護された API への要求が常に HTTPS を経由するように指定します。|

### <a name="example"></a>例 
次のコードは、TLS をチェックする Web API 認証フィルターを示しています。 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
このフィルターを、TLS を必要とする Web API アクションすべてに追加します。 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Azure Cache for Redis への通信が TLS 経由であることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Cache for Redis | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Redis TLS サポート](../../azure-cache-for-redis/cache-faq.md) |
| **手順** | Redis サーバーは既定で TLS をサポートしませんが、Azure Cache for Redis では TLS がサポートされます。 Azure Cache for Redis に接続しようとしていて、クライアントが StackExchange.Redis のように TLS をサポートしている場合は、TLS を使用する必要があります。 既定では、新しい Azure Cache for Redis インスタンスの TLS 以外のポートは無効になっています。 Redis クライアントの TLS サポートに対する依存関係がない限り、このセキュリティで保護された既定値が変更されていないことを確認してください。 |

Redis は、信頼された環境の信頼されたクライアントによってアクセスされるように設計されています。 つまり、Redis インスタンスを、インターネット (一般的には、信頼されていないクライアントが Redis TCP ポートまたは UNIX ソケットに直接アクセスする環境) に直接公開することはお勧めしません。 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>デバイスからフィールド ゲートウェイへの通信をセキュリティで保護する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | IP ベースのデバイスの場合、通信プロトコルは、通常、転送中のデータを保護するために、SSL/TLS チャネルでカプセル化できます。 SSL/TLS をサポートしていないその他のプロトコルについては、トランスポート層またはメッセージ層でセキュリティを提供する、セキュリティで保護されたプロトコル バージョンがあるかどうかを調べます。 |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>デバイスからクラウド ゲートウェイへの通信を SSL/TLS を使用してセキュリティで保護する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [通信プロトコルの選択](../../iot-hub/iot-hub-devguide.md) |
| **手順** | SSL や TLS を使用して HTTP/AMQP または MQTT のプロトコルをセキュリティで保護します。 |