---
title: 外部 App Service 環境の作成 - Azure
description: アプリまたはスタンドアロンの作成中に Azure App Service Environment を作成する方法について説明します。
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e04dfa4148213e88aa46e464a31cdd9b6125e0bf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705760"
---
# <a name="create-an-external-app-service-environment"></a>外部 App Service Environment の作成

Azure App Service Environment は、Azure 仮想ネットワーク (VNet) 内のサブネットに Azure App Service をデプロイしたものです。

> [!NOTE]
> 各 App Service Environment には仮想 IP (VIP) があり、これを使用して App Service Environment と通信できます。

App Service Environment (ASE) をデプロイするには、次の 2 つの方法があります。

- 外部 IP アドレスの VIP を使用する。外部 ASE と呼ばれます。
- 内部 IP アドレスの VIP を使用する。内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれます。

この記事では、外部 ASE を作成する方法について説明します。 ASE の概要については、[App Service Environment の概要][Intro]. For information on how to create an ILB ASE, see [Create and use an ILB ASE][MakeILBASE]に関するページをご覧ください。

## <a name="before-you-create-your-ase"></a>ASE を作成する前に

ASE を作成した後は、次の変更は行えません。

- Location
- サブスクリプション
- リソース グループ
- 使用する VNet
- 使用するサブネット
- サブネットのサイズ

> [!NOTE]
> VNet を選択し、サブネットを指定するときは、将来的な規模の拡大縮小に対応できる大きさにしてください。 推奨されるサイズは、256 のアドレスを持つ `/24` です。
>

## <a name="three-ways-to-create-an-ase"></a>ASE を作成する 3 つの方法

ASE を作成する方法は、3 つあります。

- **App Service プランを作成中に作成する**。 この方法では、1 つの手順で、ASE と App Service プランを作成します。
- **スタンドアロン アクションとして作成する**。 この方法では、何も含まない ASE である、スタンドアロン ASE を作成します。 この方法は、ASE を作成するより高度なプロセスです。 これを使用して ILB を備えた ASE を作成します。
- **Azure Resource Manager テンプレートから作成する**。 この方法は、高度な知識を持つユーザー向けです。 詳細については、[テンプレートからの ASE の作成][MakeASEfromTemplate]に関するページをご覧ください。

外部 ASE はパブリック VIP を持ちます。つまり、ASE 内のアプリへのすべての HTTP/HTTPS トラフィックは、インターネットからアクセス可能な IP アドレスに届きます。 ILB を備えた ASE には、ASE によって使用されるサブネットの IP アドレスがあります。 ILB ASE 内でホストされているアプリは、インターネットに直接は公開されません。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>ASE と App Service プランを一緒に作成する

App Service プランは、アプリのコンテナーです。 App Service でアプリを作成するときは、App Service プランを選択または作成します。 App Service 環境に App Service プランが存在し、App Service プランにアプリが存在します。

App Service プランを作成中に ASE を作成するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で、 **[リソースの作成]**  >  **[Web + モバイル]**  >  **[Web アプリ]** を選択します。

    ![Web アプリの作成][1]

2. サブスクリプションを選択します。 アプリと ASE は、同じサブスクリプションに作成されます。

3. リソース グループを選択または作成します。 リソース グループを使用すると、関連する複数の Azure リソースを 1 つの単位として管理できます。 リソース グループは、アプリ用にロールベースのアクセス制御規則を作成する際にも便利です。 詳細については、「[Azure Resource Manager の概要][ARMOverview]」を参照してください。

4. お使いの OS (Windows、Linux、Docker) を選択します。 

5. App Service プランを選択し、 **[新規作成]** を選択します。 Linux Web アプリと Windows Web アプリを同じ App Service プランに追加することはできませんが、同じ App Service 環境に追加することはできます。 

    ![新しい App Service プラン][2]

6. **[場所]** ドロップダウン リストで、ASE を作成するリージョンを選択します。 既存の ASE を選択した場合、新しい ASE は作成されません。 選択した ASE に、App Service プランが作成されます。 

7. **[価格レベル]** を選択し、 **[分離]** 価格の SKU のいずれかを選択します。 **[分離]** SKU カードと ASE 以外の場所を選択すると、新しい ASE がその場所に作成されます。 ASE を作成するプロセスを開始するには、 **[選択]** を選択します。 **[分離]** SKU は、ASE と組み合わせた場合にのみ使用できます。 また、ASE では、 **[分離]** 以外の他の価格 SKU は使用できません。 

    ![価格レベルの選択][3]

8. ASE の名前を入力します。 この名前は、アプリのアドレス可能な名前に使用されます。 たとえば、ASE の名前が _appsvcenvdemo_ の場合、ドメイン名は *.appsvcenvdemo.p.azurewebsites.net* になります。 ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは mytestapp.appsvcenvdemo.p.azurewebsites.net になります。 名前に空白文字は使用できません。 大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。

    ![新しい App Service プラン名][4]

9. Azure の仮想ネットワークの詳細を指定します。 **[新規作成]** または **[既存のものを選択]** のいずれかを選択します。 既存の VNet を選択するオプションは、選択したリージョンに VNet がある場合にのみ使用できます。 **[新規作成]** を選択した場合、VNet の名前を入力します。 その名前の新しい Resource Manager VNet が作成されます。 選択したリージョンのアドレス空間 `192.168.250.0/23` が使用されます。 **[既存のものを選択]** を選択した場合は、次のことを行う必要があります。

    a. 複数ある場合は、VNet のアドレス ブロックを選択します。

    b. 新しいサブネット名を入力します。

    c. サブネットのサイズを選択します。 *ご使用の ASE の将来的な規模の拡大に対応できるサイズを選択するのを忘れないでください。* 128 のアドレスを持ち、最大サイズの ASE に対応できる `/24` が推奨されています。 たとえば、`/28` は 16 のアドレスしか使用できないため、推奨されません。 インフラストラクチャは少なくとも 7 つのアドレスを使用し、Azure のネットワークはさらに 5 つを使用します。 `/28` サブネットでは、外部 ASE に対して最大スケーリングの 4 App Service プラン インスタンス、ILB ASE に対して 3 App Service プラン インスタンスのみが残されています。

    d. サブネット IP の範囲を選択します。

10. **[作成]** を選択して、ASE を作成します。 このプロセスにより、App Service プランとアプリも作成されます。 ASE、App Service プラン、およびアプリはすべて同じサブスクリプションの下に作成され、同じリソース グループに入ります。 ご使用の ASE に別のリソース グループが必要な場合、または ILB ASE が必要な場合は、単独で ASE を作成する手順に進みます。

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>カスタム Docker イメージを一緒に使用して ASE と Linux Web アプリを作成する

1. [Azure Portal](https://portal.azure.com/) で、 **[リソースの作成]**  >  **[Web + モバイル]**  >  **[Web App for Containers]** をクリックします。 

    ![Web アプリの作成][7]

1. サブスクリプションを選択します。 アプリと ASE は、同じサブスクリプションに作成されます。

1. リソース グループを選択または作成します。 リソース グループを使用すると、関連する複数の Azure リソースを 1 つの単位として管理できます。 リソース グループは、アプリ用にロールベースのアクセス制御規則を作成する際にも便利です。 詳細については、「[Azure Resource Manager の概要][ARMOverview]」を参照してください。

1. App Service プランを選択し、 **[新規作成]** を選択します。 Linux Web アプリと Windows Web アプリを同じ App Service プランに追加することはできませんが、同じ App Service 環境に追加することはできます。 

    ![新しい App Service プラン][8]

1. **[場所]** ドロップダウン リストで、ASE を作成するリージョンを選択します。 既存の ASE を選択した場合、新しい ASE は作成されません。 選択した ASE に、App Service プランが作成されます。 

1. **[価格レベル]** を選択し、 **[分離]** 価格の SKU のいずれかを選択します。 **[分離]** SKU カードと ASE 以外の場所を選択すると、新しい ASE がその場所に作成されます。 ASE を作成するプロセスを開始するには、 **[選択]** を選択します。 **[分離]** SKU は、ASE と組み合わせた場合にのみ使用できます。 また、ASE では、 **[分離]** 以外の他の価格 SKU は使用できません。 

    ![価格レベルの選択][3]

1. ASE の名前を入力します。 この名前は、アプリのアドレス可能な名前に使用されます。 たとえば、ASE の名前が _appsvcenvdemo_ の場合、ドメイン名は *.appsvcenvdemo.p.azurewebsites.net* になります。 ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは mytestapp.appsvcenvdemo.p.azurewebsites.net になります。 名前に空白文字は使用できません。 大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。

    ![新しい App Service プラン名][4]

1. Azure の仮想ネットワークの詳細を指定します。 **[新規作成]** または **[既存のものを選択]** のいずれかを選択します。 既存の VNet を選択するオプションは、選択したリージョンに VNet がある場合にのみ使用できます。 **[新規作成]** を選択した場合、VNet の名前を入力します。 その名前の新しい Resource Manager VNet が作成されます。 選択したリージョンのアドレス空間 `192.168.250.0/23` が使用されます。 **[既存のものを選択]** を選択した場合は、次のことを行う必要があります。

    a. 複数ある場合は、VNet のアドレス ブロックを選択します。

    b. 新しいサブネット名を入力します。

    c. サブネットのサイズを選択します。 *ご使用の ASE の将来的な規模の拡大に対応できるサイズを選択するのを忘れないでください。* 128 のアドレスを持ち、最大サイズの ASE に対応できる `/24` が推奨されています。 たとえば、`/28` は 16 のアドレスしか使用できないため、推奨されません。 インフラストラクチャは少なくとも 7 つのアドレスを使用し、Azure のネットワークはさらに 5 つを使用します。 `/28` サブネットでは、外部 ASE に対して最大スケーリングの 4 App Service プラン インスタンス、ILB ASE に対して 3 App Service プラン インスタンスのみが残されています。

    d. サブネット IP の範囲を選択します。

1.  [コンテナーの構成] を選択します。
    * カスタム イメージ名を入力します (Azure Container Registry、Docker Hub、および独自のプライベート レジストリを使用できます)。 独自のカスタム コンテナーを使用しない場合は、上記の手順に従って、コードを持ち込み、App Service on Linux と組み込みイメージを使用します。 

    ![コンテナーの構成][9]

1. **[作成]** を選択して、ASE を作成します。 このプロセスにより、App Service プランとアプリも作成されます。 ASE、App Service プラン、およびアプリはすべて同じサブスクリプションの下に作成され、同じリソース グループに入ります。 ご使用の ASE に別のリソース グループが必要な場合、または ILB ASE が必要な場合は、単独で ASE を作成する手順に進みます。


## <a name="create-an-ase-by-itself"></a>ASE を単独で作成する

ASE スタンドアロンを作成する場合、ASE には何も含まれません。 空の ASE でも、インフラストラクチャに対して月額料金が発生します。 以下の手順は、ILB を備えた ASE を作成するか、独自のリソース グループ内に ASE を作成する場合に行います。 ASE を作成した後は、通常のプロセスを使用してその中にアプリを作成できます。 場所として、新しい ASE を選択します。

1. Azure Marketplace で **App Service Environment** を検索するか、 **[リソースの作成]**  >  **[Web + モバイル]**  >  **[App Service Environment]** の順に選択します。 

1. ご使用の ASE の名前を入力します。 この名前は、ASE で作成されるアプリで使用されます。 名前が *mynewdemoase* の場合、サブドメイン名は *.mynewdemoase.p.azurewebsites.net* になります。 ここで *mytestapp* という名前のアプリを作成した場合、この Web アプリのアドレスは mytestapp.mynewdemoase.p.azurewebsites.net になります。 名前に空白文字は使用できません。 大文字を使用した場合、ドメイン名はその名前をすべて小文字で表記したバージョンになります。 ILB を使用する場合、ASE の名前はサブドメインでは使用されず、ASE の作成時に明示的に指定されます。

    ![ASE の名前付け][5]

1. サブスクリプションを選択します。 このサブスクリプションは、ASE 内のすべてのアプリが使用するものでもあります。 サブスクリプションが異なる VNet に ASE を配置することはできません。

1. 新しいリソース グループを選択するか指定します。 ASE で使用されるリソース グループは、VNet で使用されるものと同じである必要があります。 既存の VNet を選択すると、ASE のリソース グループの選択は、VNet のリソース グループを反映するように更新されます。 *ASE は、Resource Manager テンプレートを使用する場合は、VNet のリソース グループとは異なるリソース グループで作成できます。* テンプレートから ASE を作成するには、[テンプレートからの App Service Environment の作成][MakeASEfromTemplate]に関するページをご覧ください。

    ![Resource group selection][6]

1. VNet と場所を選択します。 新しい VNet を作成するか、既存の VNet を選択できます。 

    * 新しい VNet を選択した場合は、名前と場所を指定できます。 
    
    * 新しい VNet のアドレス範囲は 192.168.250.0/23 になり、サブネットの名前は [既定] になります。 サブネットは、192.168.250.0/24 として定義されます。 Resource Manager VNet のみを選択できます。 選択した **[VIP の種類]** によって、インターネット (外部) から ASE に直接アクセスできるか、ILB を使用するかが決まります。 これらのオプションについて詳しくは、「[App Service Environment で内部ロード バランサーを作成して使用する][MakeILBASE]」をご覧ください。 

      * **[VIP の種類]** として **[外部]** を選択した場合は、システムが IP ベースの SSL 用に作成する外部 IP アドレスの数を選択できます。 
    
      * **[VIP の種類]** として **[内部]** を選択した場合は、ASE で使用するドメインを指定する必要があります。 パブリックまたはプライベートのアドレス範囲を使用する VNet に ASE をデプロイできます。 パブリック アドレス範囲の VNet を使用するには、あらかじめ VNet を作成しておく必要があります。 
    
    * 既存の VNet を選択した場合、ASE が作成されたときに、新しいサブネットが作成されます。 *ポータルで事前に作成したサブネットを使用することはできません。Resource Manager テンプレートを使用する場合は、既存のサブネットを持つ ASE を作成できます。* テンプレートから ASE を作成するには、[テンプレートからの App Service Environment の作成][MakeASEfromTemplate]に関するページをご覧ください。

## <a name="app-service-environment-v1"></a>App Service Environment v1

最初のバージョンの App Service Environment (ASEv1) のインスタンスを作成することもできます。 そのプロセスを開始するには、Marketplace で **App Service Environment v1** を検索します。 スタンドアロン ASE を作成するのと同じ方法で、ASE を作成します。 完了すると、ASEv1 は 2 つのフロントエンドと 2 つの worker を持ちます。 ASEv1 では、フロントエンドや worker を管理する必要があります。 App Service プランを作成するときに自動的には追加されません。 フロントエンドは HTTP/HTTPS エンドポイントとして動作し、worker にトラフィックを送信します。 worker は、アプリをホストするロールです。 ASE を作成した後は、フロントエンドと worker の数を調整できます。 

ASEv1 について詳しくは、[App Service Environment v1 の概要][ASEv1Intro]. For more information on scaling, managing, and monitoring ASEv1, see [How to configure an App Service Environment][ConfigureASEv1]に関するページをご覧ください。

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
