---
title: ARM で ILB ASE を作成する
description: Azure Resource Manager テンプレートを使用して、内部ロード バランサーを含んだ App Service Environment (ILB ASE) を作成する方法について説明します。 アプリは、インターネットから完全に隔離されます。
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009553"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>App Service Environment で内部ロード バランサーを作成して使用する 

Azure App Service Environment は、Azure 仮想ネットワーク (VNet) 内のサブネットに Azure App Service をデプロイしたものです。 App Service Environment (ASE) をデプロイするには、次の 2 つの方法があります。 

- 外部 IP アドレスの VIP を使用する。外部 ASE と呼ばれます。
- 内部 IP アドレスの VIP を使用する。内部エンドポイントは内部ロード バランサー (ILB) であるため、ILB ASE と呼ばれます。 

この記事では、ILB ASE を作成する方法について説明します。 ASE の概要については、「[App Service Environment の概要][Intro]」をご覧ください。 外部 ASE を作成する方法については、[外部 ASE を作成する][MakeExternalASE]方法についての記事をご覧ください。

## <a name="overview"></a>概要 

ASE は、インターネットにアクセスできるエンドポイント、または VNet の IP アドレスを使用してデプロイできます。 IP アドレスを VNet のアドレスに設定するには、ASE を ILB でデプロイする必要があります。 ASE を ILB でデプロイする際は、使用する ASE の名前を指定する必要があります。 ASE の名前は、ASE 内のアプリのドメイン サフィックスで使用されます。  ILB ASE のドメイン サフィックスは &lt;ASE 名&gt;.appserviceenvironment.net です。 ILB ASE 内で作成されるアプリは、パブリック DNS 内には配置されません。 

以前のバージョンの ILB ASE では、ドメイン サフィックスと既定の証明書を HTTPS 接続用に指定する必要がありました。 ドメイン サフィックスは ILB ASE の作成時に収集されなくなり、既定の証明書も収集されなくなっています。 現在 ILB ASE を作成する際は、Microsoft が提供する、ブラウザーによって信頼された証明書が既定で使用されます。 ASE でアプリにカスタム ドメイン名を設定し、それらのカスタム ドメイン名に証明書を設定することは、まだ可能です。 

ILB ASE を使用すると、次のようなことができます。

-   サイト間接続または ExpressRoute でアクセスするクラウドでイントラネット アプリケーションを安全にホストできます。
-   WAF デバイスを使用してアプリを保護できます。
-   パブリック DNS サーバーに示されていないクラウドでアプリケーションをホストできます。
-   インターネットから分離されたバックエンド アプリを作成して、ご使用のフロントエンド アプリを安全に統合できます。

### <a name="disabled-functionality"></a>無効な機能 ###

ILB ASE を使用する際に実行できないことがいくつかあります。

-   IP ベース SSL の使用。
-   特定のアプリへの IP アドレスの割り当て。
-   Azure Portal からのアプリの証明書の購入と使用。 証明書は、証明機関から直接入手して、アプリで使用できます。 Azure Portal では入手できません。

## <a name="create-an-ilb-ase"></a>ILB ASE を作成する ##

ILB ASE を作成する方法は次のとおりです。

1. Azure portal で、 **[リソースの作成]**  >  **[Web]**  >  **[App Service Environment]** の順に選択します。

2. サブスクリプションを選択します。

3. リソース グループを選択または作成します。

4. 使用する App Service Environment の名前を入力します。

5. [仮想 IP の種類] で [内部] を選択します。

    ![ASE の作成](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> App Service Environment の名前は、37 文字以下にする必要があります。

6. [ネットワーク] を選択します。

7. Virtual Network を選択または作成します。 ここで新しい VNet を作成する場合は、192.168.250.0/23 のアドレス範囲で定義されます。 ASE とは別のアドレス範囲またはリソース グループ内に VNet を作成するには、Azure Virtual Network の作成ポータルを使用します。 

8. 空のサブネットを選択または作成します。 サブネットを選択する場合、そのサブネットは、委任されておらず空である必要があります。 ASE の作成後は、サブネットのサイズを変更できません。 推奨されるサイズは、最大サイズの ASE とあらゆるスケーリング ニーズを処理できる 256 のアドレスを持つ `/24` です。 

    ![ASE の [ネットワーク]][1]

7. **[確認と作成]** を選択し、次に **[作成]** を選択します。


## <a name="create-an-app-in-an-ilb-ase"></a>ILB ASE 内にアプリを作成する ##

通常 ASE 内にアプリを作成するのと同じ方法で、ILB ASE 内にアプリを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[Web]**  >  **[Web アプリ]** の順に選択します。

1. アプリの名前を入力します。

1. サブスクリプションを選択します。

1. リソース グループを選択または作成します。

1. [発行]、[ランタイム スタック]、[オペレーティング システム] を選択します。

1. 既存の ILB ASE の場所を選択します。  Isolated App Service プランを選択して、アプリの作成中に新しい ASE を作成することもできます。 新しい ASE を作成する場合は、ASE の作成先のリージョンを選択します。

1. App Service プランを選択または作成します。 

1. **[確認と作成]** を選択し、準備ができたら **[作成]** を選択します。

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Web ジョブ、関数および ILB ASE 

関数と Web ジョブはどちらも ILB ASE 上でサポートされますが、それらとポータルを連動するには、SCM サイトへのネットワーク アクセスが必要です。  つまり、お使いのブラウザーが、仮想ネットワーク内のホストまたは仮想ネットワークに接続しているホストに存在する必要があります。 ILB ASE で使用しているドメイン名が *appserviceenvironment.net* で終わらない場合は、お使いのブラウザーで、SCM サイトで使用されている HTTPS 証明書を信頼する必要があります。

## <a name="dns-configuration"></a>DNS の構成 

外部 ASE を使用する場合、ASE で作成されたアプリは Azure DNS で登録されます。 外部 ASE には、アプリを公開するための追加の手順はその後ありません。 ILB ASE を使う場合は、独自の DNS を管理する必要があります。 これは、独自の DNS サーバーで、または Azure DNS プライベート ゾーンを使用して行うことができます。

ILB ASE を使用して独自の DNS サーバーで DNS を構成するには、次の操作を行ってください。

1. &lt;ASE 名&gt;.appserviceenvironment.net 用のゾーンを作成する
2. そのゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する
3. そのゾーンに、ILB の IP アドレスに @ を指定する A レコードを作成する
4. &lt;ASE 名&gt;.appserviceenvironment.net に scm という名前のゾーンを作成する
5. scm ゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する

Azure DNS プライベート ゾーンで DNS を構成するには、次の操作を行ってください。

1. &lt;ASE 名&gt;.appserviceenvironment.net という名前の Azure DNS プライベート ゾーンを作成する
2. そのゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する
3. そのゾーンに、ILB の IP アドレスに @ を指定する A レコードを作成する
4. そのゾーンに、ILB の IP アドレスに *.scm を指定する A レコードを作成する

ASE の既定のドメイン サフィックスの DNS 設定では、アプリがこれらの名前によってのみアクセスできるように制限されていません。 ILB ASE では、アプリの検証なしでカスタム ドメイン名を設定できます。 その後、contoso.net という名前のゾーンを作成する場合は、ILB IP アドレスを指すようにすることができます。 カスタム ドメイン名はアプリ要求に対して機能しますが、scm サイトでは使用できません。 scm サイトは、&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net でのみ使用できます。

.&lt;asename&gt;.appserviceenvironment.net という名前のゾーンはグローバルに一意です。 2019 年 5 月より前のユーザーは、ILB ASE のドメイン サフィックスを指定できました。 ドメイン サフィックスで .contoso.com を使用した場合は、scm サイトが含まれていることになります。 このモデルには、既定の SSL 証明書の管理、scm サイトでのシングル サインオンの欠如、ワイルドカード証明書の使用要件といった課題がありました。 ILB ASE の既定の証明書アップグレード プロセスも中断され、アプリケーションが再起動されました。 これらの問題を解決するため、ILB ASE の動作が、ASE の名前と Microsoft の所有するサフィックスに基づくドメイン サフィックスを使用するように変更されました。 ILB ASE の動作の変更は、2019 年 5 月以降に作成された ILB ASE にのみ影響します。 既存の ILB ASE では、引き続き ASE の既定の証明書とその DNS 構成を管理する必要があります。

## <a name="publish-with-an-ilb-ase"></a>ILB ASE で発行する

作成されるすべてのアプリにはエンドポイントが 2 つあります。 ILB ASE には、" *&lt;アプリ名&gt;.&lt;ILB ASE ドメイン&gt;* " と " *&lt;アプリ名&gt;.scm.&lt;ILB ASE ドメイン&gt;* " があります。 

SCM サイト名をクリックすると、Azure Portal 内の Kudu コンソールに移動します。これは、**高度なポータル** と呼ばれます。 Kudu コンソールでは、環境変数の表示、ディスクの確認、コンソールの使用などができます。 詳しくは、[Azure App Service の Kudu コンソール][Kudu]に関するビデオをご覧ください。 

インターネット ベースの CI システム (GitHub や Azure DevOps など) は、ビルド エージェントがインターネットにアクセス可能であり、かつ ILB ASE と同じネットワーク上に存在すれば、引き続き機能します。 したがって、Azure DevOps の場合、ビルド エージェントが ILB ASE と同じ VNET 上に作成されていれば (サブネットは異なっていてもかまいません)、Azure DevOps git からコードをプルして ILB ASE にデプロイできます。 独自のビルド エージェントを作成しない場合は、プル モデルを使用している CI システム (Dropbox など) を使用する必要があります。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 このドメインは、アプリの発行プロファイルとアプリのポータル ブレード ( **[概要]**  >  **[要点]** 、 **[プロパティ]** など) に表示されます。 ILB ASE のドメイン サフィックスが " *&lt;ASE 名&gt;.appserviceenvironment.net*" で、アプリの名前が *mytest* である場合、FTP では "*mytest.&lt;ASE 名&gt;.appserviceenvironment.net*" となり、Web デプロイでは *mytest.scm.contoso.net* となります。

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>WAF デバイスを使用して ILB ASE を構成する ##

Web アプリケーション ファイアウォール (WAF) デバイスを ILB ASE と組み合わせることで、指定したアプリだけをインターネットに公開し、それ以外は VNet 内からしかアクセスできないようにすることができます。 これにより、セキュリティで特に保護された多層アプリケーションを構築できます。

WAF デバイスを使用して ILB ASE を構成する方法の詳細については、App Service Environment での Web アプリケーション ファイアウォールの構成][ASEWAF]に関するページを参照してください。 この記事では、Barracuda 仮想アプライアンスを ASE と使用する方法について示します。 Azure Application Gateway を使用する方法もあります。 Application Gateway は OWASP コア ルールを使用して、その背後に置かれたすべてのアプリケーションのセキュリティを確保します。 Application Gateway について詳しくは、「[Web アプリケーション ファイアウォール (WAF)][AppGW]」をご覧ください。

## <a name="ilb-ases-made-before-may-2019"></a>2019 年 5 月より前に作成された ILB ASE

2019 年 5 月より前に作成された ILB ASE では、ASE の作成中にドメイン サフィックスを設定する必要がありました。 また、そのドメイン サフィックスに基づいた既定の証明書をアップロードする必要もありました。 さらに、古い ILB ASE では、その ILB ASE 内のアプリを使用して Kudu コンソールにシングル サインオンすることはできませんでした。 古い ILB ASE 向けに DNS を構成するときは、ドメイン サフィックスと一致するゾーン内にワイルドカードの A レコードを設定する必要があります。 

## <a name="get-started"></a>はじめに ##

* ASE の使用を開始するには、「[App Service 環境の概要][Intro]」をご覧ください。 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
