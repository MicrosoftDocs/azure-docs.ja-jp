---
title: App Service 環境で内部ロード バランサーを作成して使用する - Azure | Microsoft Docs
description: ILB を含む ASE の作成と使用
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310472"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>App Service 環境での内部ロード バランサーの使用

> [!NOTE] 
> この記事は、App Service Environment v1 に関するものです。 より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。
>

App Service Environment (ASE) 機能は Azure App Service の Premium サービス オプションであり、マルチテナント スタンプでは使用できない高度な構成機能を提供します。 ASE 機能は、基本的に Azure App Service を顧客の Azure Virtual Network (VNet) にデプロイします。 App Service 環境が提供される機能の詳細については、[App Service 環境の概要][WhatisASE]に関するページを参照してください。 VNet での稼働の利点についての詳細は、「[Azure 仮想ネットワークに関する FAQ][virtualnetwork]」を参照してください。 

## <a name="overview"></a>概要
ASE は、インターネットにアクセスできるエンドポイント、または VNet の IP アドレスを使用して展開できます。 IP アドレスを VNet アドレスに設定するには、内部ロード バランサー (ILB) を含む ASE を展開する必要があります。 ILB を含むように ASE を構成する場合は、次の項目を指定します。

* 独自のドメインまたはサブドメイン。 単純化するために、このドキュメントではサブドメインを想定していますが、どちらの方法でも構成できます。 
* HTTPS で使用される証明書
* サブドメインの DNS 管理。 

これに対して、次を行うことができます。

* イントラネット アプリケーション (基幹業務アプリケーションなど) を、サイト間 VPN または ExpressRoute VPN でアクセスするクラウドで安全にホストできます
* パブリック DNS サーバーに示されていないクラウドでアプリケーションをホストできます。
* インターネットから分離されたバックエンド アプリを作成して、フロントエンド アプリを安全に統合することができます

#### <a name="disabled-functionality"></a>無効な機能
ILB ASE を使用する際に実行できないことがいくつかあります。 これらを次に示します。

* IPSSL の使用
* 特定のアプリへの IP アドレスの割り当て
* ポータルからのアプリの証明書の購入と使用。 もちろん、証明機関から証明書を直接取得してアプリでご利用いただけます。ただし、Azure Portal は使用できません。

## <a name="creating-an-ilb-ase"></a>ILB ASE の作成
ILB ASE の作成は、ASE を作成する通常の方法と特に変わりません。 ASE 作成の詳細については、「[App Service Environment の作成方法][HowtoCreateASE]」を参照してください。 ILB ASE を作成するプロセスは、ASE 作成時に VNet を作成する場合も、既存の VNet を選択する場合も同じです。 ILB ASE を作成する方法は次のとおりです。 

1. Azure Portal で、**[リソースの作成] -> [Web + モバイル] -> [App Service Environment]** の順に選択します。
2. サブスクリプションを選択します。
3. リソース グループを選択または作成します。
4. VNet を選択するか、作成します。
5. VNet を選択した場合は、サブネットを作成します。
6. **[仮想ネットワーク/場所] -> [VNet 構成]** の順に選択し、[VIP の種類] を [内部] に設定します。
7. サブドメイン名を指定します (この名前が、この ASE に作成されるアプリで使用されるサブドメインです)。
8. **[OK]**、**[作成]** の順に選択します。

![][1]

[Virtual Network] ウィンドウに、外部 VIP または内部 VIP を選択できる VNet 構成オプションがあります。 既定値は[外部] です。 [外部] に設定した場合、ASE は、インターネット アクセス可能な VIP を使用します。 [内部] を選択した場合、ASE は、VNet 内の IP アドレスの ILB で構成されます。 

[内部] を選択すると、ASE に IP アドレスを追加する機能がなくなり、代わりに ASE のサブドメインを指定する必要があります。 外部 VIP の ASE では、その ASE で作成されるアプリのためのサブドメイン内で ASE の名前が使用されます。 ASE の名前が ***contosotest*** で、その ASE 内のアプリの名前が ***mytest*** の場合、サブドメインの形式は ***contosotest.p.azurewebsites.net*** となり、そのアプリの URL は ***mytest.contosotest.p.azurewebsites.net*** になります。 VIP タイプを [内部] に設定すると、ASE 名はその ASE のサブドメイン内で使用されません。 サブドメインを明示的に指定します。 ご使用のサブドメインが ***contoso.corp.net*** で、その ASE 内に ***timereporting*** という名前のアプリを作成した場合、そのアプリの URL は ***timereporting.contoso.corp.net*** になります。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 内のアプリ
ILB ASE でのアプリの作成は、通常の ASE でのアプリの作成と同じです。 

1. Azure Portal で、**[リソースの作成] -> [Web + モバイル] -> [Web]** の順に選択するか、**[モバイル]** または **[API アプリ]** を選択します。
2. アプリの名前を入力します。
3. サブスクリプションを選択します。
4. リソース グループを選択または作成します。
5. App Service プラン (ASP) を選択または作成します。 新しい ASP を作成している場合は、ASE を場所として選択し、ASP を作成するワーカー プールを選択します。 ASP を作成するときに、場所として ASE を選択し、ワーカー プールを選択します。 アプリの名前を指定すると、アプリ名の下のサブドメインが、ASE のサブドメインによって置き換えられます。 
6. **作成**を選択します。 アプリをダッシュボードに表示する場合は、必ず **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。 

![][2]

アプリ名の下で、サブドメイン名が ASE のサブドメインを反映するように更新されます。 

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 作成後の検証
ILB ASE は、ILB を含まない ASE とは少し異なります。 既に説明したように、独自の DNS を管理する必要があります。また、HTTPS 接続に独自の証明書も用意する必要があります。 

ASE を作成すると、指定したサブドメインがサブドメインとして表示され、**[設定]** メニューに **[ILB 証明書]** という新しい項目が表示されます。 ASE は、HTTPS をテストしやすくするために自己署名証明書付きで作成されます。 HTTPS 用の独自の証明書を提供する必要がある旨のメッセージが表示されますが、それはサブドメインで有効な証明書を用意することを促すためのメッセージです。 

![][3]

ただ操作を試しているだけで、証明書の作成方法がわからない場合は、IIS MMC コンソール アプリケーションを使用して、自己署名証明書を作成できます。 この証明書を作成した後は、.pfx ファイルとしてエクスポートし、[ILB 証明書] の UI でアップロードできます。 自己署名証明書でセキュリティ保護されたサイトにアクセスすると、証明書を検証できないためアクセス先のサイトは安全ではないとブラウザーによって警告されます。 この警告を回避するには、サブドメインと一致し、ブラウザーによって認識される信頼チェーンを持つ、適切に署名された証明書が必要です。

![][6]

独自の証明書を使用するフローで、ASE に対する HTTP と HTTPS の両方をテストする場合は、次の手順に従います。

1. ASE の作成後、ASE UI に移動します (**[ASE] -> [設定] -> [ILB 証明書]**)。
2. 証明書 pfx ファイルを選択して ILB 証明書を設定し、パスワードを指定します。 この手順の処理には少し時間がかかり、拡大縮小操作が進行中というメッセージが表示されます。
3. ASE の ILB アドレスを取得します (**[ASE] -> [プロパティ] -> [仮想 IP アドレス]**)。
4. 作成後に、ASE にWeb アプリを作成します。 
5. VNET に VM がない場合は作成します (ASE と同じサブネットには作成しないでください。作成すると問題が発生します)。
6. サブドメインの DNS を設定します。 DNS のサブドメインでワイルドカードを使用できます。または、単純なテストを行いたい場合は、VM 上のホスト ファイルを編集して、Web アプリ名を VIP IP アドレスに設定します。 ASE のサブドメイン名が .ilbase.com の場合、Web アプリを mytestapp にすると、アドレスは mytestapp.ilbase.com になります。ホスト ファイルでそのように設定します  (Windows でのホスト ファイルの場所は C:\Windows\System32\drivers\etc\)。
7. この VM 上でブラウザーを使用し、 https://mytestapp.ilbase.com (または、Web アプリ名とサブドメインの組み合わせ) に移動します。
8. その VM でブラウザーを使用して、 https://mytestapp.ilbase.com に移動します。 自己署名証明書を使用する場合は、セキュリティが確保されないことを受け入れる必要があります。 

ILB の IP アドレスは、仮想 IP アドレスとして [プロパティ] にリスト表示されます。

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE の使用
#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ILB ASE によって、アプリをネットワークから分離できます。 アプリはインターネットからアクセスできず、認識されてもいません。 このアプローチは、基幹業務アプリケーションなどのイントラネット サイトをホストするうえで非常に便利です。 さらにアクセスを制限する必要がある場合は、ネットワーク セキュリティ グループ (NSG) を使用してネットワーク レベルでアクセスを制御できます。 

NSG を使用してさらにアクセスを制限しようとするときは、ASE が作動するために必要な通信を切断しないように注意する必要があります。 HTTP/HTTPS アクセスは ASE によって使用される ILB を介してのみ行われますが、ASE は依然として VNet 外部のリソースに依存しています。 どのネットワーク アクセスが引き続き必要かを確認するには、「[App Service 環境への受信トラフィックを制御する方法][ControlInbound]」および「[ExpressRoute を使用した App Service 環境のネットワーク構成の詳細][ExpressRoute]」を参照してください。 

NSG を構成するには、Azure が ASE を管理するために使用する IP アドレスを把握する必要があります。 この IP アドレスは、ASE がインターネット要求を行う場合の発信 IP アドレスでもあります。 ASE の送信 IP アドレスは、ASE が有効な限り、静的なままです。 ASE を削除して再作成すると、新しい IP アドレスを取得できます。 この IP アドレスを調べるには、**[設定] -> [プロパティ]** の順に移動し、**[送信 IP アドレス]** を確認します。 

![][5]

#### <a name="general-ilb-ase-management"></a>ILB ASE の一般的な管理
ILB ASE の管理は、通常の ASE の管理方法とほぼ同じです。 ホストする ASP インスタンス数を増やすには、ワーカー プールをスケールアップします。また、処理する HTTP/HTTPS トラフィックの量を増やすには、フロントエンド サーバーをスケールアップします。 ASE の構成を管理するための一般的な情報については、「[App Service Environment の構成][ASEConfig]」を参照してください。 

追加の管理項目は、証明書の管理と DNS の管理です。 ILB ASE の作成後にHTTPS で使用する証明書を取得してアップロードし、期限が切れる前に置き換える必要があります。 Azure は基本のドメインを所有しているため、外部 VIP を含む ASE の証明書を提供できます。 ILB ASE で使用されるサブドメインは何でもよいため、HTTPS 用に独自の証明書を提供する必要があります。 

#### <a name="dns-configuration"></a>DNS の構成
外部 VIP を使用する場合、DNS は Azure によって管理されます。 ASE に作成されるすべてのアプリは、Azure DNS (パブリック DNS) に自動的に追加されます。 ILB ASE では、独自の DNS を管理する必要があります。 contoso.corp.net など特定のサブドメインについて、次のように ILB アドレスを示す DNS A レコードを作成します。

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>使用の開始
App Service 環境の使用を開始するには、「[App Service 環境の概要][WhatisASE]」をご覧ください。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
