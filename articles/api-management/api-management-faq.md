---
title: "Azure API Management の FAQ | Microsoft Docs"
description: "Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: ea6b80e289f039a5924fcc2ccf9d71dbbb432982
ms.openlocfilehash: 2f2676d85a513a152832cfd336c3b643577341b9
ms.lasthandoff: 02/24/2017

---
# <a name="azure-api-management-faqs"></a>Azure API Management の FAQ
Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。

## <a name="contact-us"></a>お問い合わせ
* [Microsoft Azure API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)


## <a name="frequently-asked-questions"></a>よく寄せられる質問
* [機能がプレビュー段階とはどういう意味ですか。](#what-does-it-mean-when-a-feature-is-in-preview)
* [API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護するにはどうすればよいですか。](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [新しいインスタンスに API Management サービス インスタンスをコピーするにはどうすればよいですか。](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [API Management インスタンスはプログラムで管理できますか。](#can-i-manage-my-api-management-instance-programmatically)
* [ユーザーを Administrators グループに追加するにはどうすればよいですか。](#how-do-i-add-a-user-to-the-administrators-group)
* [追加するポリシーがポリシー エディターで利用できないのはなぜですか。](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [API Management で API のバージョン管理を使用するにはどうすればよいですか。](#how-do-i-use-api-versioning-in-api-management)
* [1 つの API で複数の環境をセットアップするにはどうすればよいですか。](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [API Management で SOAP を使用することはできますか。](#can-i-use-soap-with-api-management)
* [API Management ゲートウェイ IP アドレスは一定ですか。それをファイアウォール ルールで使用できますか。](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [AD FS セキュリティを使用して OAuth 2.0 承認サーバーを構成できますか。](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [複数の地理的な場所へのデプロイでは、API Management はどのようなルーティング方法を使用しますか。](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Azure Resource Manager テンプレートを使用して API Management サービス インスタンスを作成することはできますか。](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [バックエンドに自己署名 SSL 証明書を使用できますか。](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [リポジトリを複製しようとすると認証に失敗します。原因は何でしょうか。](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [API Management は Azure ExpressRoute と共に使用できますか。](#does-api-management-work-with-azure-expressroute)
* [あるサブスクリプションから別のサブスクリプションに API Management サービスを移動できますか。](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [API のインポートには制限事項や既知の問題はありますか。](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Microsoft Azure API Management チームに質問するにはどうすればよいですか。
次の方法のいずれかを使用して、チームに問い合わせることができます。

* [API Management MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)に質問を投稿する。
* <mailto:apimgmt@microsoft.com> に電子メールを送信する。
* [Azure フィードバック フォーラム](https://feedback.azure.com/forums/248703-api-management)に機能に関する要望を送信する。

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>機能がプレビュー段階とはどういう意味ですか。
機能がプレビュー段階にあるとき、Microsoft ではその機能がユーザーにどのように役立っているかについてフィードバックを積極的に求めています。 プレビュー段階の機能は機能的に完成していますが、お客様からのフィードバックに応じて大きな変更を加える可能性があります。 そのため、プレビュー段階にある機能は運用環境では使用しないことをお勧めします。 プレビュー段階の機能に関するご意見やご感想がありましたら、「[Microsoft Azure API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)」に記載された連絡方法のいずれかを使用してお知らせください。

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護するにはどうすればよいですか。
API Management ゲートウェイとバックエンド サービス間の接続は複数の方法で保護できます。 そのための方法は次のとおりです。

* HTTP 基本認証を使用します。 詳細については、「 [API 設定の構成](api-management-howto-create-apis.md#configure-api-settings)」を参照してください。
* 「[Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法](api-management-howto-mutual-certificates.md)」の説明に従って、SSL 相互認証を使用します。
* バックエンド サービスで IP ホワイトリストを使用します。 Standard レベルまたは Premium レベルの API Management インスタンスを使用している場合は、ゲートウェイの IP アドレスが変わることはありません。 この IP アドレスを許可するようにホワイトリストを設定できます。 API Management インスタンスの IP アドレスは、Azure Portal のダッシュボードで取得できます。
* API Management インスタンスを Azure Virtual Network に接続します。 

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>新しいインスタンスに API Management サービス インスタンスをコピーするにはどうすればよいですか。
複数の方法で API Management インスタンスを新しいインスタンスにコピーできます。 そのための方法は次のとおりです。

* API Management のバックアップと復元の機能を使用します。 詳細については、「[Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法](api-management-howto-disaster-recovery-backup-restore.md)」を参照してください。
* [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) を使用して独自のバックアップと復元の機能を作成します。 REST API を使用すると、任意のサービス インスタンスからエンティティを保存して復元できます。
* Git を使用してサービス構成をダウンロードし、新しいインスタンスにアップロードします。 詳細については、「[Git を使用して API Management サービス構成を保存および構成する方法](api-management-configuration-repository-git.md)」を参照してください。

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>API Management インスタンスはプログラムで管理できますか。
はい、API Management は次の手段を使用してプログラムで管理できます。

* [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)
* [Microsoft Azure ApiManagement Service Management Library SDK](http://aka.ms/apimsdk)
* [サービスのデプロイ](https://msdn.microsoft.com/library/mt619282.aspx)および[サービスの管理](https://msdn.microsoft.com/library/mt613507.aspx)用の PowerShell コマンドレット

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>ユーザーを Administrators グループに追加するにはどうすればよいですか。
次の手順に従って、ユーザーを Administrators グループに追加できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 更新する API Management インスタンスが含まれているリソース グループに移動します。
3. API Management で、目的のユーザーに **API Management 共同作成者**ロールを割り当てます。

新しく追加した共同作成者は Azure PowerShell [コマンドレット](https://msdn.microsoft.com/library/mt613507.aspx)を使用できます。 次の手順に従って、管理者としてサインインできます。

1. `Login-AzureRmAccount` コマンドレットを使用してサインインします。
2. `Set-AzureRmContext -SubscriptionID <subscriptionGUID>` を使用して、このサービスが含まれているサブスクリプションのコンテキストを設定します。
3. `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` を使用して、シングル サインオン URL を取得します。
4. この URL を使って管理ポータルにアクセスできます。
### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>追加するポリシーがポリシー エディターで利用できないのはなぜですか。
追加するポリシーがポリシー エディターで薄く表示されたり網掛けになったりしている場合は、そのポリシー用の正しいスコープが選択されていることを確認してください。 各ポリシー ステートメントは、特定のスコープおよびポリシー セクションで使用するように設計されています。 ポリシーのポリシー セクションとスコープを確認するには、「[API Management policies (API Management ポリシー)](https://msdn.microsoft.com/library/azure/dn894080.aspx)」で、目的のポリシーの「Usage (使用方法)」セクションを参照してください。

### <a name="how-do-i-use-api-versioning-in-api-management"></a>API Management で API のバージョン管理を使用するにはどうすればよいですか。
API Management では API のバージョン管理を複数の方法で使用できます。

* API Management では、バージョンが異なる複数の API を構成できます。 たとえば、MyAPIv1 と MyAPIv2 という&2; つの API を構成できます。 こうすれば、開発者は自分でバージョンを選ぶことができます。
* バージョン セグメントを含まないサービス URL で API を構成することもできます (例: https://my.api )。 その後で、各操作の[書き換え URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) テンプレートでバージョン セグメントを構成できます。 たとえば、/resource という [URL](api-management-howto-add-operations.md#url-template) テンプレートと /v1/Resource という[書き換え URL](api-management-howto-add-operations.md#rewrite-url-template) テンプレートで操作を作成することができます。 バージョン セグメントの値は各操作で個別に変更できます。
* API のサービス URL 内に "既定" のバージョン セグメントを保持する場合は、選択した操作に対して、[バックエンド サービスの設定](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) ポリシーを使用してバックエンド要求パスを変更するポリシーを設定できます。
### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>1 つの API で複数の環境をセットアップするにはどうすればよいですか。
テスト環境と運用環境など、複数の環境を&1; つの API でセットアップする方法は&2; つあります。 そのための方法は次のとおりです。

* 同じテナントで複数の API をホストする。
* 異なるテナントで同じ API をホストする。

### <a name="can-i-use-soap-with-api-management"></a>API Management で SOAP を使用することはできますか。
[SOAP パススルー](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/)がサポートされるようになりました。 管理者が SOAP サービスの WSDL をインポートすると、Azure API Management によって SOAP フロント エンドが作成されます。 開発者ポータル ドキュメント、テスト コンソール、ポリシーと分析を、SOAP サービスで利用できます。

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>API Management ゲートウェイ IP アドレスは一定ですか。 それをファイアウォール ルールで使用できますか。
Standard レベルと Premium レベルでは、API Management テナントのパブリック IP アドレス (VIP) は、テナントの有効期間中、静的です。ただし、いくつかの例外があります。 IP アドレスは次の状況では変化します。

* サービスが削除された後、再作成された。
* サービスのサブスクリプションが (未払いなどの理由により) 中断された後、復元された。
* Azure Virtual Network を追加または削除した (Virtual Network は Premium レベルでのみ使用できます)。

複数のリージョンにデプロイしている場合、リージョンが廃止された後で再開されると、リージョンのアドレスが変わります (複数リージョンのデプロイは Premium レベルでのみ使用できます)。

複数リージョンのデプロイ用に構成された Premium レベルのテナントには、リージョンごとに&1; つのパブリック IP アドレスが割り当てられます。

IP アドレス (複数リージョンのデプロイの場合は複数個) は Azure Portal のテナント ページで取得できます。

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>AD FS セキュリティを使用して OAuth 2.0 承認サーバーを構成できますか。
Active Directory フェデレーション サービス (AD FS) セキュリティを使用して OAuth 2.0 承認サーバーを構成する方法の詳細については、「[Using ADFS in API Management (API Management での AD FS の使用)](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)」を参照してください。

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>複数の地理的な場所へのデプロイでは、API Management はどのようなルーティング方法を使用しますか。
API Management では、複数の地理的な場所へのデプロイで[パフォーマンス トラフィック ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method)を使用します。 着信トラフィックは、最も近い API ゲートウェイにルーティングされます。 1 つのリージョンがオフラインになった場合、着信トラフィックは自動的に次に最も近いゲートウェイにルーティングされます。 ルーティング方法の詳細については、[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)に関するページを参照してください。

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Azure Resource Manager テンプレートを使用して API Management サービス インスタンスを作成することはできますか。
はい。 詳細については、[Azure API Management サービス](http://aka.ms/apimtemplate)のクイックスタート テンプレートを参照してください。

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>バックエンドに自己署名 SSL 証明書を使用できますか。
はい。 次の手順に従って、バックエンドに自己署名 Secure Sockets Layer (SSL) 証明書を使用できます。

1. API Management を使用して[バックエンド](https://msdn.microsoft.com/library/azure/dn935030.aspx) エンティティを作成します。
2. **skipCertificateChainValidation** プロパティを **true** に設定します。
3. 自己署名証明書を許可する必要がなくなったときは、バックエンド エンティティを削除するか、**skipCertificateChainValidation** プロパティを **false** に設定します。

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Git リポジトリを複製しようとすると認証に失敗します。原因は何でしょうか。
Git Credential Manager を使用しているか、Visual Studio を使用して Git リポジトリを複製しようとしている場合は、Windows の資格情報ダイアログ ボックスで、既知の問題が発生する場合があります。 この問題では、ダイアログ ボックスでパスワードの文字数が 127 文字に制限され、生成されたパスワードが切り詰められます。 現在、パスワードの短縮を進めています。 現時点では、Git リポジトリの複製には Git Bash をお使いください。

### <a name="does-api-management-work-with-azure-expressroute"></a>API Management は Azure ExpressRoute と共に使用できますか。
はい。 API Management は Azure ExpressRoute と共に使用できます。

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>あるサブスクリプションから別のサブスクリプションに API Management サービスを移動できますか。
はい。 方法の詳細については、「[Move resources to a new resource group or subscription (新しいリソース グループまたはサブスクリプションへのリソースの移動)](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>API のインポートには制限事項や既知の問題はありますか。
Open API (Swagger)、WSDL、WADL の形式についての[既知の問題と制限事項](api-management-api-import-restrictions.md)をご覧ください。

