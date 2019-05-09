---
title: App Service 環境で内部ロード バランサーを作成する - Azure
description: インターネットから分離された Azure App Service Environment を作成および使用する方法について説明します
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 01d982d91d772ccfd468ccdac6391f971be4f43b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546544"
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment で内部ロード バランサーを作成して使用する #

 Azure App Service Environment は、Azure 仮想ネットワーク (VNet) 内のサブネットに Azure App Service をデプロイしたものです。 App Service Environment (ASE) をデプロイするには、次の 2 つの方法があります。 

- 外部 IP アドレスの VIP を使用する。外部 ASE と呼ばれます。
- 内部 IP アドレスの VIP を使用する。内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれます。 

この記事では、ILB ASE を作成する方法について説明します。 ASE の概要については、「[App Service Environment の概要][Intro]」をご覧ください。 外部 ASE を作成する方法については、[外部 ASE を作成する][MakeExternalASE]方法についての記事をご覧ください。

## <a name="overview"></a>概要 ##

ASE は、インターネットにアクセスできるエンドポイント、または VNet の IP アドレスを使用してデプロイできます。 IP アドレスを VNet のアドレスに設定するには、ASE を ILB でデプロイする必要があります。 ASE を ILB でデプロイするときは、次を指定する必要があります。

-   アプリを作成するときに使用する独自のドメイン。
-   HTTPS で使用する証明書。
-   ドメインの DNS 管理。

これに対して、次を行うことができます。

-   サイト間 VPN または Azure ExpressRoute VPN でアクセスするクラウドでイントラネット アプリケーションを安全にホストできます。
-   パブリック DNS サーバーに示されていないクラウドでアプリケーションをホストできます。
-   インターネットから分離されたバックエンド アプリを作成して、ご使用のフロントエンド アプリを安全に統合できます。

### <a name="disabled-functionality"></a>無効な機能 ###

ILB ASE を使用する際に実行できないことがいくつかあります。

-   IP ベース SSL の使用。
-   特定のアプリへの IP アドレスの割り当て。
-   Azure Portal からのアプリの証明書の購入と使用。 証明書は、証明機関から直接入手して、アプリで使用できます。 Azure Portal では入手できません。

## <a name="create-an-ilb-ase"></a>ILB ASE を作成する ##

ILB ASE を作成する方法は次のとおりです。

1. Azure portal で、 **[リソースの作成]** > **[Web]** > **[App Service 環境]** の順に選択します。

2. サブスクリプションを選択します。

3. リソース グループを選択または作成します。

4. VNet を選択するか、作成します。

5. 既存の VNet を選択した場合は、ASE を保持するサブネットを作成する必要があります。 サブネットのサイズは ASE の将来の拡大を考慮した大きさにする必要があります。 推奨されるサイズは、最大サイズの ASE とあらゆるスケーリング ニーズを処理できる 256 のアドレスを持つ `/24` です。 

6.  **[仮想ネットワーク/場所]** > **[仮想ネットワーク構成]** を選択します。  **[VIP の種類]** を **[内部]** に設定します。

7. ドメイン名を入力します。 このドメインが、この ASE に作成されるアプリで使用されます。 いくつかの制限が適用されます。 次の名前にすることはできません。

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   [既存の DNS 名をアプリにマップ][customdomain]することができます。 アプリで使用するカスタム ドメイン名と、ご使用の ASE によって使用されるドメイン名を重複させることはできません。 ILB ASE のドメイン名が _contoso.com_ である場合、次のようなカスタム ドメイン名はご使用のアプリで使用できません。

   * www\.contoso.com

   * abcd.def.contoso.com

   * abcd.contoso.com

   ご使用のアプリのカスタム ドメイン名がわかっている場合は、これらのカスタム ドメイン名と競合しない ILB ASE のドメインを選択します。 この例では、ASE のドメインに *contoso-internal.com* のようなものを使用できます。これは、*.contoso.com* で終わるカスタム ドメイン名と重複しないためです。

8. **[OK]** を選択し、**[作成]** を選択します。

    ![ASE の作成][1]

**[仮想ネットワーク]** ブレードに、**[仮想ネットワーク構成]** オプションがあります。 これを使用して外部 VIP または内部 VIP を選択します。 既定値は **[外部]** です。 **[外部]** を選択した場合、ASE はインターネット アクセス可能な VIP を使用します。 **[内部]** を選択した場合は、VNet 内の IP アドレスに、ILB を含む ASE が構成されます。

**[内部]** を選択した後、IP アドレスを ASE に追加できなくなります。 代わりに、ASE のドメインを指定する必要があります。 外部 VIP の ASE では、ASE 名はその ASE で作成されたアプリのドメインで使用されます。

**[VIP の種類]** を **[内部]** に設定すると、ASE 名はその ASE のドメインで使用されません。 ドメインを明示的に指定します。 ご使用のドメインが *contoso.corp.net*  で、その ASE 内に  *timereporting* という名前のアプリを作成した場合、そのアプリの URL は timereporting.contoso.corp.net になります。


## <a name="create-an-app-in-an-ilb-ase"></a>ILB ASE 内にアプリを作成する ##

通常 ASE 内にアプリを作成するのと同じ方法で、ILB ASE 内にアプリを作成します。

1. Azure portal で  **[リソースの作成]** > **[Web + モバイル]** > **[Web アプリ]** の順に選択します。

1. アプリの名前を入力します。

1. サブスクリプションを選択します。

1. リソース グループを選択または作成します。

1. OS を選択します。 

    * カスタム Docker コンテナーを使用して Linux アプリを作成する場合は、 [こちら][linuxapp]の手順に従って独自のコンテナーを持ち込むことができます。 

1. App Service プランを選択または作成します。 新しい App Service プランを作成する場合は、ご使用の ASE を作成場所に選択します。 App Service プランを作成するワーカー プールを選択します。 App Service プランを作成するときに、ご使用の ASE を作成場所に選択し、ワーカー プールを選択します。 アプリの名前を指定すると、アプリ名の下のドメインが、ASE のドメインによって置き換えられます。

1. **作成** を選択します。 アプリをダッシュボードに表示する場合は、 **[ダッシュボードにピン留めする]**  チェックボックスを選択します。

    ![App Service プランの作成][2]

    **[アプリ名]** の下で、ドメイン名がご使用の ASE のドメインを反映するように更新されます。

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 作成後の検証 ##

ILB ASE は、ILB を含まない ASE とは少し異なります。 既に説明したように、お客様独自の DNS を管理する必要があります。 また、HTTPS 接続用にお客様独自の証明書を指定する必要もあります。

ASE を作成すると、ドメイン名には、お客様が指定したドメインが表示されます。 新しい項目は、 **[ILB 証明書]** という  **[設定]**  メニューに表示されます。 ILB ASE ドメインを指定しない証明書が ASE とともに作成されます。 ASE をその証明書とともに使用すると、ご使用のブラウザーによってそれが無効であると表示されます。 この証明書は、HTTPS をテストしやすくしますが、ご使用の ILB ASE ドメインに関連付けられている独自の証明書をアップロードする必要があります。 この手順は、証明書が自己署名されているか証明機関から取得したかに関係なく必要です。

![ILB ASE ドメイン名][3]

ILB ASE には、有効な SSL 証明書が必要です。 内部証明機関を使用して、外部の発行者から証明書を購入するか、または自己署名証明書を使用します。 どのようなソースから SSL 証明書を取得した場合でも、以下の証明書属性を適切に構成する必要があります。

* **サブジェクト**:この属性は、*.your-root-domain-here に設定する必要があります。
* **サブジェクトの別名**:この属性は、**.your-root-domain-here* と **.scm.your-root-domain-here* を両方とも含むように設定する必要があります。 各アプリに関連付けられた SCM/Kudu サイトへの SSL 接続が、*your-app-name.scm.your-root-domain-here* 形式のアドレスを使用します。

SSL 証明書を .pfx ファイルとして変換、保存します。 .pfx ファイルは、すべての中間証明書とルート証明書を含んでいる必要があります。 パスワードで保護してください。

自己署名証明書を作成する場合は、ここで、PowerShell コマンドを使用できます。 *internal.contoso.com* の代わりに、ご使用の ILB ASE ドメイン名を使用してください。 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

これらの PowerShell コマンドが生成する証明書は、お使いのブラウザーの信頼チェーンに含まれる証明機関によって作成されなかったため、ブラウザーによってフラグを設定されます。 お使いのブラウザーが信頼する証明書を取得するには、ブラウザーの信頼チェーンに含まれる商用証明機関から購入します。 

![ILB 証明書の設定][4]

次のようにして独自の証明書をアップロードし、アクセスをテストします。

1. ASE を作成した後は、ASE UI に移動します。  **[ASE]** > **[設定]** > **[ILB 証明書]** の順に選択します。

1. ILB 証明書を設定するには、証明書 .pfx ファイルを選択してパスワードを入力します。 この手順では、処理に時間がかかります。 アップロード操作が進行中であることを示すメッセージが表示されます。

1. ご使用の ASE の ILB アドレスを取得します。 **[ASE]** > **[プロパティ]** > **[仮想 IP アドレス]** の順に選択します。

2. ASE 作成後に、その ASE にアプリを作成します。

3. その VNet 内にない場合は VM を作成します。

    > [!NOTE] 
    > 失敗または問題が発生することがあるため、ASE と同じサブネットにこの VM を作成しないでください。
    >

4. ASE ドメインで使用する DNS を設定します。 お使いの DNS のドメインにワイルドカードを使用できます。 いくつかの簡単なテストを行うには、ご使用の VM 上の hosts ファイルを編集して、アプリの名前を VIP の IP アドレスに設定します。

    a. ASE のドメイン名が _.ilbase.com_ で、_mytestapp_ という名前のアプリを作成した場合、アドレスは _mytestapp.ilbase.com_ になります。 その後 _mytestapp.ilbase.com_ を設定して ILB アドレスを解決します。 (Windows では、ホスト ファイルは _C:\Windows\System32\drivers\etc\\_ にあります。)

    b. Web デプロイの発行または高度なコンソールへのアクセスをテストするには、_mytestapp.scm.ilbase.com_ のレコードを作成します。

5. その VM でブラウザーを使用して、 https://mytestapp.ilbase.com に移動します。 (またはドメインにアプリ名が含まれるいずれかのアドレスに移動します)。

6. その VM でブラウザーを使用して、 https://mytestapp.ilbase.com に移動します。 自己署名証明書を使用する場合は、セキュリティが確保されないことを受け入れます。

    ご使用の ILB の IP アドレスは **[IP アドレス]** の下の一覧に表示されます。 この一覧には、外部 VIP で使用される IP アドレスや受信管理トラフィック用の IP アドレスも含まれます。

    ![ILB の IP アドレス][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Web ジョブ、関数および ILB ASE ##

関数と Web ジョブはどちらも ILB ASE 上でサポートされますが、それらとポータルを連動するには、SCM サイトへのネットワーク アクセスが必要です。  つまり、お使いのブラウザーが、仮想ネットワーク内のホストまたは仮想ネットワークに接続しているホストに存在する必要があります。  

ILB ASE で Azure Functions を使用するときに、"現在、関数を取得できません。 後でもう一度やり直してください。" というエラー メッセージが表示されることがあります。 このエラーが発生するのは、Functions UIが SCM サイトを HTTPS で利用し、ルート証明書がブラウザーの信頼チェーンに存在しないためです。 Web ジョブにも同様の問題があります。 この問題を回避するには、次のいずれかの操作を行います。

- 信頼された証明書ストアへの証明書の追加。 これで、Microsoft Edge や Internet Explorer のブロックが解除されます。
- Chrome を使用し、先に SCM サイトにアクセスし、信頼されていない証明書を受け入れてからポータルに移動します。
- ブラウザーの信頼チェーンにある商用証明書を使用します。  これが最適な方法です。  

## <a name="dns-configuration"></a>DNS の構成 ##

外部 VIP を使用する場合、DNS は Azure によって管理されます。 ASE に作成されるすべてのアプリは、Azure DNS (パブリック DNS) に自動的に追加されます。 ILB ASE では、独自の DNS を管理する必要があります。 _contoso.net_ など特定のサブドメインについて、次のような ILB アドレスを示す DNS A レコードを DNS に作成する必要があります。

- *.contoso.net
- *.scm.contoso.net

ご使用の ILB ASE ドメインがこの ASE 以外の複数の用途で使用される場合、DNS をアプリ名ごとに管理する必要があることがあります。 この方法は、作成時に新しいアプリ名をそれぞれ DNS に追加する必要があるため、煩雑です。 このため、専用のドメインを使用することをお勧めします。

## <a name="publish-with-an-ilb-ase"></a>ILB ASE で発行する ##

作成されるすべてのアプリにはエンドポイントが 2 つあります。 ILB ASE には、*&lt;アプリ名>.&lt;ILB ASE ドメイン>* と*&lt;アプリ名>.scm.&lt;ILB ASE ドメイン>* があります。 

SCM サイト名をクリックすると、Azure Portal 内の Kudu コンソールに移動します。これは、**高度なポータル**と呼ばれます。 Kudu コンソールでは、環境変数の表示、ディスクの確認、コンソールの使用などができます。 詳しくは、[Azure App Service の Kudu コンソール][Kudu]に関するビデオをご覧ください。 

マルチテナント App Service と外部 ASE では、Azure Portal と Kudu コンソールの間にシングル サインオンが存在します。 ただし、ILB ASE の場合は発行証明書を使用して Kudu コンソールにサインインする必要があります。

インターネット ベースの CI システム (GitHub や Azure DevOps など) は、ビルド エージェントがインターネットにアクセス可能であり、かつ ILB ASE と同じネットワーク上に存在すれば、引き続き機能します。 したがって、Azure DevOps の場合、ビルド エージェントが ILB ASE と同じ VNET 上に作成されていれば (サブネットは異なっていてもかまいません)、Azure DevOps git からコードをプルして ILB ASE にデプロイできます。 独自のビルド エージェントを作成しない場合は、プル モデルを使用している CI システム (Dropbox など) を使用する必要があります。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 このドメインは、アプリの発行プロファイルとアプリのポータル ブレード (**[概要]** > **[要点]**、**[プロパティ]** など) に表示されます。 サブドメインが *contoso.net* でアプリ名が *mytest* の ILB ASE では、FTP で *mytest.contoso.net* を使用し、Web デプロイで *mytest.scm.contoso.net* を使用します。

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>ILB ASE を WAF デバイスと結合する ##

Azure App Service では、システムを保護する多くのセキュリティ対策を提供します。 これらは、アプリがハッキングされたかどうかの判定もサポートします。 Web アプリケーションの保護を確保する最も優れた方法は、ホスト プラットフォームを結合することです。たとえば、Azure App Service を Web Application Firewall (WAF) と結合します。 ILB ASE にはネットワークから分離されたアプリケーション エンドポイントが存在するため、このような用途に適しています。

ILB ASE と WAF デバイスを構成する方法について詳しくは、「[App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する][ASEWAF]」をご覧ください。 この記事では、Barracuda 仮想アプライアンスを ASE と使用する方法について示します。 Azure Application Gateway を使用する方法もあります。 Application Gateway は OWASP コア ルールを使用して、その背後に置かれたすべてのアプリケーションのセキュリティを確保します。 Application Gateway について詳しくは、「[Web アプリケーション ファイアウォール (WAF)][AppGW]」をご覧ください。

## <a name="get-started"></a>作業開始 ##

* ASE の使用を開始するには、「[App Service 環境の概要][Intro]」をご覧ください。
 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
