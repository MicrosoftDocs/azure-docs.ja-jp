---
title: Service Fabric クライアント認証用に Azure Active Directory を設定する | Microsoft Docs
description: Service Fabric クラスターのクライアントを認証するための Azure Active Directory (Azure AD) の設定方法について学習します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 6c195357c4a037534307571a53589b2ae861d88b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486023"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>クライアント認証用に Azure Active Directory をセットアップする

Azure で実行されているクラスターの場合、Azure Active Directory (Azure AD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することをお勧めします。  この記事では、Service Fabric クラスターのクライアントを認証するための Azure AD の設定方法について説明します。この操作は、[クラスターを作成する](service-fabric-cluster-creation-via-arm.md)前に行う必要があります。  Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や and [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。  アプリケーションを作成した後は、ユーザーを読み取り専用ロールおよび管理者ロールに割り当てます。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

## <a name="prerequisites"></a>前提条件
この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず、[Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]に関するページをお読みください。

Azure AD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

1. コンピューターに[リポジトリをクローン](https://github.com/Azure-Samples/service-fabric-aad-helpers)します。
2. インストールされたスクリプトのための[すべての前提条件が満たされていることを確認](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started)してください。

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD アプリケーションを作成し、ユーザーをロールに割り当てる

クラスターへのアクセスを制御するために、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成するスクリプトを使用します。 クラスターを表すアプリケーションを作成したら、[Service Fabric によってサポートされるロール](service-fabric-cluster-security-roles.md) (読み取り専用と管理者) 用にユーザーを作成します。

`SetupApplications.ps1` を実行します。パラメーターとして、テナント ID、クラスター名、および Web アプリケーション応答 URL を指定します。  ユーザー名とユーザーのパスワードも指定します。 例:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 国内のクラウド (たとえば Azure Government、Azure China、Azure Germany) については、`-Location` パラメーターも指定する必要があります。

*TenantId* は、PowerShell コマンド `Get-AzureSubscription` を実行することで検索できます。 このコマンドを実行すると、すべてのサブスクリプションの TenantId が表示されます。

*ClusterName* は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、一緒に使用される Service Fabric クラスターに対して、Azure AD のアーティファクトのマッピングを容易にするためだけに使用します。

*WebApplicationReplyUrl* は、サインインの完了後に Azure AD によってユーザーに返される既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure portal][azure-portal] でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

[クラスターを作成](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory の設定に関するトラブルシューティング
Azure AD の設定時や使用時には問題が発生することがあります。そこで、問題のデバッグに役立つポインターをいくつか紹介します。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer に、証明書の選択を求めるメッセージが表示される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD に正常にサインインすると、ブラウザーはホームページに戻りますが、証明書の選択を求めるメッセージが表示されます。

![SFX 証明書ダイアログ][sfx-select-certificate-dialog]

#### <a name="reason"></a>理由
ユーザーに Azure AD クラスター アプリケーション内でロールが割り当てられていません。 このため、Service Fabric クラスターで Azure AD の認証が失敗します。 Service Fabric エクスプローラーは、証明書認証にフォールバックします。

#### <a name="solution"></a>解決策
次の説明に従って Azure AD をセットアップしてユーザー ロールを割り当てます。 また、`SetupApplications.ps1` のように [アプリにアクセスするにはユーザー割り当てが必要] をオンにすることをお勧めします。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell を使用した接続が失敗し、次のエラーが返される:"指定した資格情報が無効です"
#### <a name="problem"></a>問題点
"AzureActiveDirectory" セキュリティ モードで PowerShell を使用してクラスターに接続すると、Azure AD に正常にサインインした後で接続に失敗し、次のエラーが返されます:"指定した資格情報が無効です"。

#### <a name="solution"></a>解決策
前の問題の解決策と同じです。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>サインインするときに Service Fabric Explorer で次のエラーが返される:"AADSTS50011"
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD にサインインしようとすると、次のエラーが返されます:"AADSTS50011:応答アドレス &lt;url&gt; が、アプリケーションに対して構成された応答アドレス: &lt;guid&gt; と一致していません"。

![SFX の応答アドレスが一致しない][sfx-reply-address-not-match]

#### <a name="reason"></a>理由
Service Fabric Explorer に相当するクラスター (Web) アプリケーションは Azure AD に対する認証を試み、要求の一部として、戻り先のリダイレクト URL を指定しています。 しかし、その URL が Azure AD アプリケーションの **[応答 URL]** のリストに表示されません。

#### <a name="solution"></a>解決策
AAD ページで [アプリの登録] を選び、クラスター アプリケーションを選んで、 **[応答 URL]** ボタンを選びます。 [応答 URL] ページで、Service Fabric Explorer の URL をリストに追加するか、リスト内の項目のいずれかと置き換えます。 完了したら、変更を保存します。

![Web アプリケーションの応答 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell で Azure AD 認証を使用してクラスターに接続する
Service Fabric クラスターに接続するには、次の PowerShell コマンド例を使用します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

詳細については、[Connect-ServiceFabricCluster コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)に関するページを参照してください。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>複数のクラスターで同じ Azure AD テナントを再利用できますか?
はい。 ただし、Service Fabric Explorer の URL をクラスター (Web) アプリケーションに追加することを忘れないでください。 そうしないと、Service Fabric Explorer は動作しません。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD が有効になっているときもサーバーの証明書が必要なのはどうしてですか?
FabricClient と FabricGateway では、相互認証が実行されます。 Azure AD 認証中、Azure AD 統合がクライアント ID をサーバーに提供し、サーバー証明書を使用してサーバー ID の確認が行われます。 Service Fabric の証明書の詳細については、「[X.509 証明書と Service Fabric][x509-certificates-and-service-fabric]」を参照してください。

## <a name="next-steps"></a>次の手順
Azure Active Directory アプリケーションを設定し、ユーザーのロールを設定したら、[クラスターを構成してデプロイ](service-fabric-cluster-creation-via-arm.md)します。


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
