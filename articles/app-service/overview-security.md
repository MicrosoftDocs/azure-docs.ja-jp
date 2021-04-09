---
title: Security
description: App Service でアプリをセキュリティで保護する方法と、アプリを脅威からさらに保護する方法について説明します。
keywords: azure app service, web アプリ, モバイル アプリ, api アプリ, 関数アプリ, セキュリティ, セキュア, セキュリティ保護, コンプライアンス, 準拠, 証明書, https, ftps, tls, 信頼, 暗号化, 暗号化する, 暗号化済み, ip の制限, 認証, 認可, authn, autho, msi, マネージド サービス ID, マネージド ID, シークレット, 秘密, パッチ処理, パッチ, バージョン, 分離, ネットワークの分離, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736107"
---
# <a name="security-in-azure-app-service"></a>Azure App Service のセキュリティ

この記事では、[Azure App Service](overview.md) で Web アプリ、モバイル アプリ バック エンド、API アプリ、および[関数アプリ](../azure-functions/index.yml)をセキュリティで保護する方法について説明します。 また、組み込みの App Service 機能を使用してアプリをさらに保護する方法についても説明します。

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

以下のセクションでは、App Service アプリを脅威からさらに保護する方法について説明します。

## <a name="https-and-certificates"></a>HTTPS と証明書

App Service を使用すると、[HTTPS](https://wikipedia.org/wiki/HTTPS) でアプリをセキュリティで保護できます。 アプリを作成すると、HTTPS を使用して既定のドメイン名 (\<app_name>.azurewebsites.net) にアクセスできます。 [アプリのカスタム ドメインを構成する](app-service-web-tutorial-custom-domain.md)場合は、クライアント ブラウザーがカスタム ドメインに対して安全な HTTPS 接続を実行できるように、[TLS/SSL 証明書を使用してセキュリティで保護する](configure-ssl-bindings.md)必要があります。 App Service では、いくつかの種類の証明書がサポートされています。

- Free App Service マネージド証明書
- App Service 証明書
- サードパーティの証明書
- Azure Key Vault からインポートされた証明書

詳細については、[Azure App Service への TLS/SSL 証明書の追加](configure-ssl-certificate.md)に関する記事をご覧ください。

## <a name="insecure-protocols-http-tls-10-ftp"></a>セキュリティで保護されていないプロトコル (HTTP、TLS 1.0、FTP)

すべての暗号化されていない (HTTP) 接続からアプリをセキュリティで保護するために、App Service には 1 クリックで HTTPS を適用できる構成が用意されています。 セキュリティで保護されていない要求は、アプリケーション コードに到達する前に拒否されます。 詳細については、「[HTTPS の適用](configure-ssl-bindings.md#enforce-https)」を参照してください。

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 は、[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) などの業界標準によって安全であると見なされなくなっています。 App Service で [TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions) を適用することで、古いプロトコルを無効にすることができます。

App Service は、ファイルの展開に FTP と FTPS の両方をサポートしています。 ただし、可能な限り FTP ではなく FTPS を使用することをお勧めします。 これらのプロトコルの 1 つまたは両方が使用されていない場合は、[無効にする](deploy-ftp.md#enforce-ftps)ことをお勧めします。

## <a name="static-ip-restrictions"></a>静的 IP の制限事項

既定で、App Service アプリはインターネットを介したすべての IP アドレスの要求を受け入れますが、そのアクセスをごく一部の IP アドレスに制限することができます。 Windows 上の App Service では、アプリへのアクセスを許可されている IP アドレスの一覧を定義できます。 許可一覧には、個々 の IP アドレスまたはサブネット マスクによって定義された IP アドレスの範囲を含めることができます。 詳細については、「[Azure App Service 静的 IP 制限](app-service-ip-restrictions.md)」を参照してください。

Windows 上の App Service の場合、_web.config_ を構成して IP アドレスを動的に制限することもできます。詳細については、「[Dynamic IP Security\<dynamicIpSecurity>](/iis/configuration/system.webServer/security/dynamicIpSecurity/)」(動的 IP セキュリティ) を参照してください。

## <a name="client-authentication-and-authorization"></a>クライアントの認証と承認

Azure App Service には、ユーザーまたはクライアント アプリのターンキーの認証および承認機能があります。 有効にすると、アプリ コードをほとんど、またはまったく作成せずに、ユーザーとクライアント アプリにサインインすることができます。 独自の認証および承認ソリューションを実装するか、App Service でその処理を自動的に実行することができます。 認証および承認モジュールは、Web 要求をアプリケーション コードに渡す前に Web 要求を処理し、未承認の要求をコードに到達する前に拒否します。

App Service の認証および承認は、Azure Active Directory、Microsoft アカウント、Facebook、Google、Twitter などの複数の認証プロバイダーをサポートしています。 詳細については、「 [Azure App Service での認証および承認](overview-authentication-authorization.md)」を参照してください。

## <a name="service-to-service-authentication"></a>サービス間認証

バックエンド サービスに対して認証する場合、App Service には必要に応じて 2 つの異なるメカニズムが用意されています。

- **サービス ID** - アプリ自体の ID を使用してリモート リソースにサインインします。 App Service を使用すると、[マネージド ID](overview-managed-identity.md) を簡単に作成できます。この ID は、[Azure SQL Database](/azure/sql-database/)、[Azure Key Vault](../key-vault/index.yml) などの他のサービスで認証するために使用できます。 この方法のエンドツーエンドのチュートリアルについては、「[マネージド ID を使用した App Service からの Secure Azure SQL Database 接続のセキュリティ保護](app-service-web-tutorial-connect-msi.md)」を参照してください。
- **代理 (OBO)** - ユーザーの代理でリモート リソースへの委任されたアクセスを行います。 Azure Active Directory を認証プロバイダーとして使用すると、App Service アプリは、[Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) や App Service のリモート API アプリなどのリモート サービスへの委任されたサインインを実行できます。 この方法のエンドツーエンドのチュートリアルについては、「[Linux 用 Azure App Service でユーザーをエンドツーエンドで認証および承認する](tutorial-auth-aad.md)」を参照してください。

## <a name="connectivity-to-remote-resources"></a>リモート リソースへの接続性

アプリからアクセスする必要があるリモート リソースには、次の 3 種類があります。 

- [Azure リソース](#azure-resources)
- [Azure Virtual Network 内のリソース](#resources-inside-an-azure-virtual-network)
- [オンプレミスのリソース](#on-premises-resources)

それぞれの場合について、App Service にはセキュリティで保護された接続を確立する方法が用意されていますが、セキュリティのベスト プラクティスを守ることをお勧めします。 たとえば、バックエンド リソースで暗号化されていない接続が許可されている場合でも、常に暗号化された接続を使用します。 さらに、バックエンドの Azure サービスで最小限の IP アドレスを許可するようにします。 アプリの送信 IP アドレスについては、「[Azure App Service における受信 IP アドレスと送信 IP アドレス](overview-inbound-outbound-ips.md)」を参照してください。

### <a name="azure-resources"></a>Azure リソース

アプリが [SQL Database](https://azure.microsoft.com/services/sql-database/) や [Azure Storage](../storage/index.yml) などの Azure リソースに接続しても、接続は Azure 内にとどまり、ネットワーク境界を越えません。 ただし、接続は Azure の共有ネットワークを経由するので、接続は常に暗号化してください。 

アプリが [App Service 環境](environment/intro.md)でホストされている場合、[仮想ネットワーク サービス エンドポイントを使用して、サポートされている Azure サービスに接続する](../virtual-network/virtual-network-service-endpoints-overview.md)必要があります。

### <a name="resources-inside-an-azure-virtual-network"></a>Azure Virtual Network 内のリソース

アプリは、[Azure Virtual Network](../virtual-network/index.yml) 内のリソースに [Virtual Network 統合](web-sites-integrate-with-vnet.md)を介してアクセスできます。 Virtual Network との統合は、ポイント対サイトの VPN を使用して確立されます。 アプリは、プライベート IP アドレスを使用して Virtual Network 内のリソースにアクセスできるようになります。 ただし、ポイント対サイト接続は Azure の共有ネットワークを経由します。 

リソース接続を Azure の共有ネットワークから完全に分離するには、[App Service 環境](environment/intro.md)でアプリを作成します。 App Service 環境は常に専用の Virtual Network に展開されるので、アプリと Virtual Network 内のリソースとの接続は完全に分離されます。 App Service 環境におけるネットワーク セキュリティのその他の側面については、「[ネットワークの分離](#network-isolation)」を参照してください。

### <a name="on-premises-resources"></a>オンプレミスのリソース

データベースなどのオンプレミス リソースには、次の 3 つの方法で安全にアクセスできます。 

- [ハイブリッド接続](app-service-hybrid-connections.md) - TCP トンネルを介してリモート リソースへのポイント間接続を確立します。 TCP トンネルは、TLS 1.2 と Shared Access Signature (SAS) キーを使用して確立されます。
- [サイト間 VPN を使用した Virtual Network 統合](web-sites-integrate-with-vnet.md) - 「[Azure Virtual Network 内のリソース](#resources-inside-an-azure-virtual-network)」で説明されているように、Virtual Network は[サイト間 VPN](../vpn-gateway/tutorial-site-to-site-portal.md) を介してオンプレミス ネットワークに接続できます。 このネットワーク トポロジでは、アプリは Virtual Network 内の他のリソースなどのオンプレミス リソースに接続できます。
- [サイト間 VPN を使用した App Service 環境](environment/intro.md) - 「[Azure Virtual Network 内のリソース](#resources-inside-an-azure-virtual-network)」で説明されているように、Virtual Network は[サイト間 VPN](../vpn-gateway/tutorial-site-to-site-portal.md) を介してオンプレミス ネットワークに接続できます。 このネットワーク トポロジでは、アプリは Virtual Network 内の他のリソースなどのオンプレミス リソースに接続できます。

## <a name="application-secrets"></a>アプリケーション シークレット

データベースの資格情報、API トークン、秘密キーなどのアプリケーション シークレット情報をコードや構成ファイルに保存しないでください。 一般に受け入れられているのは、選択した言語の標準パターンを使用して[環境変数](https://wikipedia.org/wiki/Environment_variable)としてアクセスする方法です。 App Service では、[アプリ設定](configure-common.md#configure-app-settings) (と .NET アプリケーションの場合は特別に[接続文字列](configure-common.md#configure-connection-strings)) を使用して環境変数を定義します。 アプリ設定と接続文字列は、Azure で暗号化されて保存され、アプリの起動時にアプリのプロセス メモリに挿入される前に暗号化が解除されます。 暗号化キーは定期的に回転されます。

また、高度なシークレット管理のために、App Service アプリを [Azure Key Vault](../key-vault/index.yml) と統合することもできます。 [マネージド ID を使用してキー コンテナーにアクセスする](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)ことで、App Service アプリは必要なシークレットに安全にアクセスできます。

## <a name="network-isolation"></a>ネットワークの分離

**Isolated** 価格レベルを除くすべての価格レベルでは、App Service の共有ネットワーク インフラストラクチャ上でアプリが実行されます。 たとえば、パブリック IP アドレスとフロントエンド ロード バランサーは他のテナントと共有されます。 **Isolated** 価格レベルでは、専用の [App Service 環境](environment/intro.md)内でアプリを実行することで完全なネットワークの分離を実現しています。 App Service 環境は、[Azure Virtual Network](../virtual-network/index.yml) の独自のインスタンスで実行されます。 以下を実行できます。 

- 専用のフロント エンドを使用し、専用のパブリック エンドポイントを介してアプリを提供する。
- 内部ロードバランサー (ILB) を使用して内部アプリケーションを提供する。これによって、Azure Virtual Network 内からのアクセスのみが許可されます。 ILB にはプライベート サブネットの IP アドレスがあり、アプリはインターネットから完全に分離されます。
- [Web アプリケーション ファイアウォール (WAF) の背後で ILB を使用する](environment/integrate-with-application-gateway.md)。 WAF は、DDoS 保護、URI フィルター処理、SQL のインジェクション防止など、一般公開されているアプリケーションにエンタープライズレベルの保護を提供します。

詳細については、[Azure App Service 環境の概要](environment/intro.md)に関するページを参照してください。