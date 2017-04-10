---
title: "App Service Environment での内部ロード バランサーの作成と使用 | Microsoft Docs"
description: "ILB を含む ASE の作成と使用"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21223eb0cfbff607e15713e4726761c1bdb01774


---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>App Service 環境での内部ロード バランサーの使用
App Service 環境 (ASE) 機能は Azure App Service の Premium サービス オプションであり、マルチテナント スタンプでは使用できない高度な構成機能を提供します。  ASE 機能は、基本的に Azure App Service を顧客の Azure Virtual Network (VNet) にデプロイします。  App Service 環境で提供される機能の詳細については、「[App Service 環境の概要][WhatisASE]」を参照してください。  VNet での稼働の利点がわからない場合は、「[Virtual Network FAQ][virtualnetwork]」を参照してください。  

## <a name="overview"></a>概要
ASE は、インターネットにアクセスできるエンドポイント、または VNet の IP アドレスを使用して展開できます。  IP アドレスを VNet アドレスに設定するには、内部ロード バランサー (ILB) を含む ASE を展開する必要があります。  ILB を含むように ASE を構成する場合は、次の項目を指定します。

* 独自のドメインまたはサブドメイン。  単純化するために、このドキュメントではサブドメインを想定していますが、どちらの方法でも構成できます。  
* HTTPS で使用される証明書
* サブドメインの DNS 管理。  

これに対して、次を行うことができます。

* イントラネット アプリケーション (基幹業務アプリケーションなど) を、サイト間 VPN または ExpressRoute VPN でアクセスするクラウドで安全にホストできます。
* パブリック DNS サーバーに示されていないクラウドでアプリケーションをホストできます。
* インターネットから分離されたバックエンド アプリを作成して、フロント エンド アプリを安全に統合することができます。

#### <a name="disabled-functionality"></a>無効な機能
ILB ASE を使用する際に実行できないことがいくつかあります。  これらを次に示します。

* IPSSL の使用
* 特定のアプリへの IP アドレスの割り当て
* ポータルからのアプリの証明書の購入と使用。  もちろん、証明機関から証明書を直接取得してアプリで使用できます。Azure ポータルが使用できないだけです。

## <a name="creating-an-ilb-ase"></a>ILB ASE の作成
ILB ASE の作成は、ASE を作成する通常の方法と特に変わりません。  ASE の作成方法の詳細については、「[App Service 環境の作成方法][HowtoCreateASE]」を参照してください。  ILB ASE を作成するプロセスは、ASE 作成時に VNet を作成する場合も、既存の VNet を選択する場合も同じです。  ILB ASE を作成する方法は次のとおりです。 

1. Azure ポータルで、**[新規]、[Web + モバイル]、[App Service 環境]** の順に選択します。
2. サブスクリプションを選択します。
3. リソース グループを選択または作成します。
4. VNet を選択するか、作成します。
5. VNet を選択した場合は、サブネットを作成します。
6. **[仮想ネットワーク/場所]、[VNet 構成]** の順に選択し、[VIP の種類] を [内部] に設定します。
7. サブドメイン名を指定します (このサブドメインが、この ASE に作成されるアプリで使用されます)。
8. [OK] をクリックして作成します。

![][1]

[仮想ネットワーク] ブレード内に、VNet 構成オプションがあります。  これを使用して、外部 VIP または内部 VIP を選択できます。  既定値は[外部] です。  [外部] に設定した場合、ASE は、インターネット アクセス可能な VIP を使用します。  [内部] を選択した場合は、VNet 内の IP アドレスに、ILB を含む ASE が構成されます。  

[内部] を選択すると、ASE に IP アドレスを追加する機能がなくなり、代わりに ASE のサブドメインを指定する必要があります。  外部 VIP の ASE では、その ASE で作成されるアプリのためのサブドメイン内で ASE の名前が使用されます。  
ASE の名前が ***contosotest*** で、その ASE内のアプリの名前が ***mytest*** の場合、サブドメインの形式は ***contosotest.p.azurewebsites.net*** となり、そのアプリの URL は ***mytest.contosotest.p.azurewebsites.net*** になります。  
VIP タイプを [内部] に設定すると、ASE 名はその ASE のサブドメイン内で使用されません。  サブドメインを明示的に指定します。  サブドメインが ***contoso.corp.net*** で、その ASE 内に ***timereporting*** という名前のアプリを作成した場合、そのアプリの URL は ***timereporting.contoso.corp.net*** になります。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 内のアプリ
ILB ASE でのアプリの作成は、通常の ASE でのアプリの作成と同じです。  

1. Azure ポータルで、**[新規]、[Web + モバイル]、[Web]** の順に選択するか、**[モバイル]** または **[API アプリ]** を選択します。
2. アプリの名前を入力します。
3. サブスクリプションを選択します。
4. リソース グループを選択または作成します。
5. App Service プラン (ASP) を選択または作成します。  新しい ASP を作成している場合は、ASE を場所として選択し、ASP を作成するワーカー プールを選択します。  ASP を作成するときに、場所として ASE を選択し、ワーカー プールを選択します。  アプリの名前を指定すると、アプリ名の下のサブドメインが、ASE のサブドメインによって置き換えられます。   
6. [作成] を選択します。  アプリをダッシュボードに表示する場合は、 **[ダッシュボードにピン留めする]** チェックボックスを選択する必要があります。  

![][2]

アプリ名の下で、サブドメイン名が ASE のサブドメインを反映するように更新されます。  

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 作成後の検証
ILB ASE は、ILB を含まない ASE とは少し異なります。  既に説明したように、独自の DNS を管理する必要があります。また、HTTPS 接続に独自の証明書も用意する必要があります。  

ASE を作成すると、指定したサブドメインがサブドメインとして表示され、**[設定]** メニューに **[ILB 証明書]** という項目が表示されます。  ASE は、HTTPS をテストしやすくするために自己署名証明書付きで作成されます。  HTTPS 用の独自の証明書を提供する必要がある旨のメッセージが表示されますが、それはサブドメインで有効な証明書を用意することを促すためのメッセージです。  

![][3]

ただ操作を試しているだけで、証明書の作成方法がわからない場合は、IIS MMC コンソール アプリケーションを使用して、自己署名証明書を作成できます。  この証明書を作成した後は、.pfx ファイルとしてエクスポートし、[ILB 証明書] の UI でアップロードできます。 自己署名証明書でセキュリティ保護されたサイトにアクセスすると、証明書を検証できないためアクセス先のサイトは安全ではないとブラウザーによって警告されます。  この警告を回避するには、サブドメインと一致し、ブラウザーによって認識される信頼チェーンを持つ、適切に署名された証明書が必要です。

![][6]

独自の証明書を使用するフローで、ASE に対する HTTP と HTTPS の両方をテストする場合は、次の手順に従います。

1. ASE の作成後に ASE UI に移動します (**[ASE]、[設定]、[ILB 証明書]** を順に選択)。
2. 証明書 pfx ファイルを選択して ILB 証明書を設定し、パスワードを指定します。  この手順の処理には少し時間がかかります。スケーリング処理が進行中というメッセージが表示されます。
3. ASE の ILB アドレスを取得します (**[ASE]、[プロパティ]、[仮想 IP アドレス]** を順に選択)。
4. 作成後に、ASE に web アプリを作成します。 
5. VNET に VM がない場合は作成します (ASE と同じサブネットには作成しないでください。そうすると問題が発生します)。
6. サブドメインの DNS を設定します。  DNS のサブドメインでワイルドカードを使用できます。または、単純なテストを行いたい場合は、VM 上のホスト ファイルを編集して、Web アプリ名を VIP IP アドレスに設定します。  ASE のサブドメイン名が .ilbase.com の場合、Web アプリを mytestapp にすると、アドレスは mytestapp.ilbase.com になります。ホスト ファイルでそのように設定します。  (Windows では、ホスト ファイルは C:\Windows\System32\drivers\etc\ にあります。)
7. この VM 上でブラウザーを使用し、http://mytestapp.ilbase.com (または、Web アプリ名とサブドメインの組み合わせ) にアクセスします。
8. この VM 上でブラウザーを使用し、https://mytestapp.ilbase.com にアクセスします。自己署名証明書を使用する場合は、セキュリティの欠如を受け入れる必要があります。  

ILB の IP アドレスは、仮想 IP アドレスとして [プロパティ] にリスト表示されます。

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE の使用
#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ILB ASE によって、アプリをネットワークから分離することができます。アプリはインターネットからアクセスできず、認識されてもいません。  これは、基幹業務アプリケーションなどのイントラネット サイトをホストするために非常に便利です。  さらにアクセスを制限する必要がある場合は、ネットワーク セキュリティ グループ (NSG) を使用してネットワーク レベルでアクセスを制御できます。 

NSG を使用してさらにアクセスを制限しようとするときは、ASE が作動するために必要な通信を切断しないように注意する必要があります。  HTTP/HTTPS アクセスは ASE によって使用される ILB を介してのみ行われますが、ASE は依然として VNet 外部のリソースに依存しています。  どのネットワーク アクセスが引き続き必要かを確認するには、「[App Service 環境への受信トラフィックを制御する方法][ControlInbound]」および「[ExpressRoute を使用した App Service 環境のネットワーク構成の詳細][ExpressRoute]」の情報を参照してください。  

NSG を構成するには、Azure が ASE を管理するために使用する IP アドレスを把握する必要があります。  この IP アドレスは、ASE がインターネット要求を行う場合の発信 IP アドレスでもあります。  この IP アドレスを調べるには、**[設定]、[プロパティ]** の順に選択し、**[送信 IP アドレス]** を確認します。  

![][5]

#### <a name="general-ilb-ase-management"></a>ILB ASE の一般的な管理
ILB ASE の管理は、通常の ASE の管理方法とほぼ同じです。  ホストする ASP インスタンス数を増やすためにワーカー プールをスケールアップし、処理する HTTP/HTTPS トラフィックの量を増やすためにフロント エンド サーバーをスケールアップする必要があります。  ASE の構成を管理するための一般的な情報については、「[App Service 環境の構成][ASEConfig]」を参照してください。  

追加の管理項目は、証明書の管理と DNS の管理です。  ILB ASE の作成後にHTTPS で使用する証明書を取得してアップロードし、期限が切れる前に置き換える必要があります。  Azure は基本のドメインを所有しているため、外部 VIP を含む ASE の証明書を提供できます。  ILB ASE で使用されるサブドメインは何でもよいため、HTTPS 用に独自の証明書を提供する必要があります。 

#### <a name="dns-configuration"></a>DNS の構成
外部 VIP を使用する場合、DNS は Azure によって管理されます。  ASE に作成されるすべてのアプリは、Azure DNS (パブリック DNS) に自動的に追加されます。  ILB ASE では、独自の DNS を管理する必要があります。  contoso.corp.net など特定のサブドメインについて、次のように ILB アドレスを示す DNS A レコードを作成する必要があります。

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>使用の開始
App Service 環境に関するすべての記事と作業方法は [App Service 環境の README](../app-service/app-service-app-service-environments-readme.md)を参照してください。

App Service Environment の使用を開始するには、「[App Service Environment の概要][WhatisASE]」を参照してください。

Azure App Service プラットフォームの詳細については、「[Azure App Service とは][AzureAppService]」を参照してください。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/



<!--HONumber=Nov16_HO3-->


