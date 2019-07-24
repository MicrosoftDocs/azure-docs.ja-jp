---
title: Azure App Service Environment の使用 - Azure
description: Azure App Service Environment でアプリを作成、発行、スケールする方法
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c332b20650bef2e341a935dacae835403dc56c9b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630667"
---
# <a name="use-an-app-service-environment"></a>App Service Environment の使用 #

## <a name="overview"></a>概要 ##

Azure App Service Environment は、ユーザーの Azure 仮想ネットワーク内のサブネットに Azure App Service をデプロイしたものです。 構成は次のとおりです。

- **フロントエンド:** App Service Environment (ASE) で HTTP または HTTPS が終了する場所です。
- **ワーカー**:アプリのホストとなるリソースです。
- **データベース**:環境を定義する情報が格納されます。
- **ストレージ**:ユーザーによって発行されたアプリのホストとして使用されます。

> [!NOTE]
> App Service Environment には、ASEv1 と ASEv2 の 2 つのバージョンがあります。 ASEv1 では、使用するリソースを事前に管理する必要があります。 ASEv1 の構成と管理の方法については、[App Service Environment v1 の構成][ConfigureASEv1]に関するページを参照してください。 この記事の残りの部分では、ASEv2 について説明します。
>
>

ASE は (ASEv1 と ASEv2 のどちらも)、アプリ アクセスに外部 VIP を使用したデプロイと内部 VIP を使用したデプロイが可能です。 外部 VIP を使用したデプロイは、外部 ASE と呼ばれます。 内部 VIP を使用したデプロイは、内部ロード バランサー (ILB) を使用するので、ILB ASE と呼ばれます。 ILB ASE の詳細については、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。

## <a name="create-an-app-in-an-ase"></a>ASE 内にアプリを作成する ##

ASE でアプリを作成する方法は通常の作成方法とほとんど同じですが、いくつか違いがあります。 新しい App Service プランを作成する場合は、次の点が異なります。

- アプリのデプロイ先の場所として地理的な場所ではなく ASE を選択します。
- ASE で作成する App Service プランはすべて、"分離" 価格レベルに属している必要があります。

ASE をご利用でない場合は、[App Service Environment の作成][MakeExternalASE]に関するページの手順に従って作成できます。

ASE 内にアプリを作成するには:

1. **[リソースの作成]** > **[Web + モバイル]** > **[Web アプリ]** を選択します。

2. アプリの名前を入力します。 ASE 内で既に App Service プランが選択されている場合、アプリのドメイン名に、ASE のドメイン名が反映されます。

    ![アプリ名の選択][1]

1. サブスクリプションを選択します。

1. 新しいリソース グループの名前を指定するか、**[既存のものを使用]** を選択して、ボックスの一覧からいずれかを選択します。

1. OS を選択します。 

    * ASE での Linux アプリのホストは新しいプレビュー機能です。そのため、実稼働ワークロードが現在実行されている ASE に Linux アプリを追加しないようお勧めします。 
    * ASE に Linux アプリを追加することは、ASE もプレビュー モードになることを意味します。 

1. ASE で既存の App Service プランを選択するか、次の手順で新しく作成します。

    a. **[新規作成]** を選択します。

    b. App Service プランの名前を入力します。

    c. **[場所]** ボックスの一覧から該当する ASE を選択します。 ASE での Linux アプリのホスティングが有効なのは、現時点で、**米国西部、米国東部、西ヨーロッパ、北ヨーロッパ、オーストラリア東部、東南アジア**の 6 つのリージョンのみです。 

    d. **[分離]** 価格レベルを選択します。 **[選択]** を選択します。

    e. **[OK]** を選択します。
    
    ![[分離] 価格レベル][2]

    > [!NOTE]
    > Linux アプリと Windows アプリを同じ App Service プランに追加することはできませんが、同じ App Service 環境に追加することはできます。 
    >

2. **作成**を選択します。

## <a name="how-scale-works"></a>スケールのしくみ ##

すべての App Service アプリは、App Service プランで実行されます。 対象環境に App Service プランが存在し、App Service プランにアプリが存在するコンテナー モデルになっています。 アプリをスケールするときは、App Service プランをスケールすることになります。そうすることによって同じプラン内のすべてのアプリがスケールされます。

ASEv2 では、App Service プランをスケールすると、必要なインフラストラクチャが自動的に追加されます。 インフラストラクチャが追加されるまでの間、スケール操作に時間差が生じます。 ASEv1 の場合は、App Service プランを作成したりスケールアウトしたりする前に、必要なインフラストラクチャをあらかじめ追加しておく必要があります。 

マルチテナントの App Service では、通常スケーリングが即座に実行されます。スケールアウトへの対応としてすぐに利用できるリソースのプールが存在するためです。 ASE にはそのようなバッファーが存在せず、リソースは必要に応じて割り当てられます。

ASE では、最大 100 インスタンスまでスケールアップすることができます。 この 100 インスタンスは、すべて 1 つの App Service プランに含めることも、複数の App Service プランに分散させることもできます。

## <a name="ip-addresses"></a>IP アドレス ##

App Service には、アプリに専用の IP アドレスを割り当てる機能があります。 [既存のカスタム SSL 証明書の Azure App Service へのバインド][ConfigureSSL]に関する記事で説明されているように、この機能は IP ベースの SSL を構成した後に利用できます。 ただし、ASE の場合は重要な例外があります。 ILB ASE では IP ベースの SSL に使用する追加の IP アドレスを追加することはできません。

ASEv1 では、使用する IP アドレスを事前にリソースとして割り当てておく必要があります。 ASEv2 では、マルチテナントの App Service の場合と同様、アプリの IP アドレスを使用します。 ASEv2 には、IP アドレスが 30 個を上限として、常に予備のアドレスが 1 つ存在します。 すぐに利用できる IP アドレスを常時確保するために、アドレスを 1 つ使用すると、そのたびに、別のアドレスが追加されます。 別の IP アドレスを割り当てるまでに、どうしても時間差が発生するので、IP アドレスを立て続けに追加することはできません。

## <a name="front-end-scaling"></a>フロント エンドのスケーリング ##

ASEv2 では、App Service プランをスケールアウトすると、そのために必要なワーカーが自動的に追加されます。 各 ASE は 2 つのフロント エンドで作成されます。 さらに、フロント エンドは App Service プラン内の 15 インスタンスにつき 1 つの割合で自動的にスケールアウトされます。 たとえば、15 個のインスタンスが存在する場合、フロント エンド数は 3 つになります。 同様に、30 インスタンスまでスケールした場合は、フロント エンド数は 4 つです。

ほとんどのシナリオで、この数のフロント エンドがあれば十分ですが、 より高速にスケールアウトすることもできます。 最小で 5 インスタンスにつき 1 フロント エンドの割合でスケールアウトするよう変更することができますが、 スケールアウト率の変更には料金がかかります。 詳細については、[Azure App Service の価格][Pricing]に関するページを参照してください。

フロント エンド リソースは、ASE の HTTP/HTTPS エンドポイントです。 既定のフロント エンド構成では、各フロント エンドのメモリ使用量が常時 60% 程度となります。 顧客のワークロードがフロント エンドで実行されることはありません。 スケールに関して重要となるフロント エンドの要素は CPU で、これは主に HTTPS トラフィックによって決まります。

## <a name="app-access"></a>アプリのアクセス ##

外部 ASE では、アプリを作成するときに使用されるドメインはマルチテナント App Service とは異なり、 ASE の名前を含みます。 外部 ASE の作成について詳しくは、[App Service Environment の作成][MakeExternalASE]に関するページを参照してください。 外部 ASE のドメイン名は、たとえば *.&lt;asename&gt;.p.azurewebsites.net* という形式になります。 たとえば、実際の ASE の名前が _external-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、そのアプリには次の URL でアクセスすることになります。

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

contoso.scm.external-ase.p.azurewebsites.net は、Kudu コンソールにアクセスするとき、または Web 配置でアプリを発行するときに使用する URL です。 Kudu コンソールについては、[Azure App Service の Kudu コンソール][Kudu]に関するページを参照してください。 Kudu コンソールを使用すると、デバッグやファイルのアップロード、ファイルの編集など、さまざまな作業を Web UI で行うことができます。

ILB ASE では、デプロイ時にドメインを決定します。 ILB ASE の作成方法について詳しくは、[ILB ASE の作成と使用][MakeILBASE]に関するページを参照してください。 ドメイン名として _ilb-ase.info_ を指定した場合、その ASE 内のアプリには、その指定したドメインをアプリの作成時に使用します。 たとえば _contoso_ という名前のアプリの URL は次のようになります。

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>発行 ##

マルチテナントの App Service と同様、ASE では次の発行方法を利用できます。

- Web デプロイ。
- FTP。
- 継続的インテグレーション。
- Kudu コンソールでのドラッグ アンド ドロップ。
- IDE (Visual Studio、Eclipse、Intellij IDEA など)。

外部 ASE では、これらの発行オプションの動作はすべて同じです。 詳細については、[Azure App Service へのデプロイ][AppDeploy]に関するページを参照してください。 

発行方法に大きな違いがあるのは ILB ASE です。 ILB ASE では、発行エンドポイントは ILB を介してしか利用できません。 ILB は、仮想ネットワークの ASE サブネット内のプライベート IP 上に存在します。 ILB へのネットワーク アクセスができなければ、その ASE にアプリを発行することができません。 [ILB ASE の作成と使用][MakeILBASE]に関するページで述べられているように、システム内にアプリ用の DNS を構成する必要があります。 SCM のエンドポイントも対象となります。 これらが適切に定義されてないと発行できません。 ご利用の IDE から直接発行するためには、ILB へのネットワーク アクセスが IDE にも必要です。

インターネットベースの CI システム (GitHub、Azure DevOps など) は ILB ASE では動作しません。発行エンドポイントにインターネットでアクセスすることができないためです。 プル モデルを使用した CI システム (Dropbox など) で代用する必要があります。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 これはアプリの発行プロファイルのほか、アプリのポータル ブレード (**[概要]** > **[要点]**、**[プロパティ]** など) で確認することができます。 

## <a name="pricing"></a>価格 ##

"**分離**" という価格 SKU は ASEv2 専用として作成されました。 ASEv2 でホストされるすべての App Service プランは、"分離" 価格 SKU に属します。 Isolated App Service プランのリージョンは地域によって異なる場合があります。 

App Service プランの料金に加え、ASE そのものの固定料金がかかります。 固定料金は ASE のサイズによって変わることはありません。App Service プラン インスタンス 15 個ごとに追加フロント エンド 1 という既定スケール率で、ASE インフラストラクチャの料金が発生します。  

15 個の App Service プラン インスタンスごとに 1 フロント エンドという既定スケール率が十分でない場合は、フロント エンドが追加されるペースまたはフロント エンドのサイズを調整できます。  スケール率またはサイズを調整した場合は、既定では追加されないフロント エンド コアの料金が発生します。  

たとえば、スケール率を調整して 10 にした場合、App Service プラン内の 10 インスタンスにつきフロント エンドが 1 つ追加されます。 固定料金で対応されるスケール率は、15 インスタンスにつき 1 フロント エンドです。 スケール率が 10 の場合、App Service プランの 10 インスタンスに対して追加される 3 つ目のフロント エンドの料金が追加でかかります。 インスタンス数が 15 になっても、フロント エンドは自動的に追加されるので追加料金は必要ありません。

フロント エンドのサイズを 2 コアに調整して、スケール率を調整しなかった場合、追加のコアの料金が発生します。  ASE は 2 つのフロント エンドで作成されています。このため、自動スケーリングしきい値を下回っていても、フロント エンドのサイズを 2 コアに拡張した場合は、追加の 2 つのコアの料金を支払うことになります。

詳細については、[Azure App Service の価格][Pricing]に関するページを参照してください。

## <a name="delete-an-ase"></a>ASE の削除 ##

ASE を削除するには、次の手順に従います。 

1. **[App Service Environment]** ブレードの上部にある **[削除]** を使用します。 

1. ASE の名前を入力して削除を確定します。 ASE を削除すると、その内部のコンテンツもすべて削除されます。 

    ![ASE の削除][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
