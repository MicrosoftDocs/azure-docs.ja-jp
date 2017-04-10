---
title: "Azure における Active Directory フェデレーション サービス | Microsoft Docs"
description: "このドキュメントでは、Azure に AD FS をデプロイして高可用性を確保する方法について説明します。"
keywords: "azure への AD FS のデプロイ, azure adfs のデプロイ, azure adfs, azure ad fs,adfs のデプロイ, ad fs のデプロイ, azure での adfs, azure への adfs のデプロイ, azure への AD FS のデプロイ, adfs azure, AD FS の概要, Azure, Azure での AD FS, iaas, ADFS, azure への adfs の移行"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: anandy;billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: ed3b3b114af2844405779f65fa8c3e89ae6a6c35
ms.lasthandoff: 03/08/2017

---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Azure での Active Directory フェデレーション サービスのデプロイ
AD FS は、単純かつ安全な ID フェデレーションと Web シングル サインオン (SSO) 機能を実現します。 Azure AD または O365 とのフェデレーションによって、ユーザーはオンプレミスの資格情報を認証に使用し、クラウド内のあらゆるリソースにアクセスすることができます。 そのため、オンプレミスとクラウドの両方のリソースに確実にアクセスできるよう、AD FS インフラストラクチャには、高い可用性を確保することが重要となります。 AD FS を Azure にデプロイすると、必要な高可用性を最小限の手間で確保できます。
AD FS を Azure にデプロイする利点はいくつかありますが、その一部を以下に示します。

* **高可用性** - Azure の可用性セットを使用してインフラストラクチャに高い可用性を確保できます。
* **拡張が容易** - パフォーマンスを強化する必要がある場合、 Azure からの簡単なクリック操作で簡単に、より高性能なマシンに移行できます。
* **地域を越えた冗長性** - Azure Geo Redundancy によって地域を越えた世界規模での高可用性がインフラストラクチャに確保されます。
* **管理しやすい** - 高度に単純化された管理オプションが Azure ポータルに用意されているため、インフラストラクチャ管理の手間がかからず、ごく簡単に管理することができます。 

## <a name="design-principles"></a>設計原則
![Deployment design](./media/active-directory-aadconnect-azure-adfs/deployment.png)

上の図は、これから AD FS インフラストラクチャを Azure にデプロイしようとする場合に推奨される基本的なトポロジを示したものです。 このトポロジの各種コンポーネントの要点を以下に示します。

* **DC / ADFS サーバー**: ユーザー数が 1,000 人未満である場合、ドメイン コントローラーに AD FS ロールをインストールするだけでかまいません。 ドメイン コントローラーへのパフォーマンスの影響が懸念される場合や、ユーザー数が 1,000 人を超える場合は、単独のサーバーに AD FS をデプロイします。
* **WAP サーバー** : ユーザーが会社のネットワークに接続していないときでも AD FS に到達できるよう WAP (Web アプリケーション プロキシ) サーバーをデプロイする必要があります。
* **DMZ**: Web アプリケーション プロキシ サーバーは DMZ に配置し、DMZ と内部サブネットとの間の通信には TCP/443 アクセスのみを許可します。
* **ロード バランサー**: AD FS サーバーと Web アプリケーション プロキシ サーバーの高可用性を確保するために、AD FS サーバーには内部ロード バランサーの使用を、Web アプリケーション プロキシ サーバーには Azure Load Balancer の使用をそれぞれお勧めします。
* **可用性セット**: AD FS のデプロイに冗長性を確保するために、同様のワークロードを処理する複数の仮想マシンは、1 つの可用性セットとしてグループ化するようお勧めします。 このように構成されていれば、計画済みまたは計画外メンテナンスの間も、少なくとも&1; つの仮想マシンを使用できます。
* **ストレージ アカウント**: ストレージ アカウントは&2; つ用意することをお勧めします。 ストレージ アカウントが&1; つである場合、単一障害点が形成され、万一ストレージ アカウントがダウンした場合に、環境が利用できなくなる可能性があります。 ストレージ アカウントが&2; つあれば、障害系統ごとに&1; つのストレージ アカウントを関連付けることができます。
* **ネットワークの分離**: Web アプリケーション プロキシ サーバーは、単独の DMZ ネットワークにデプロイする必要があります。 1 つの仮想ネットワークを&2; つのサブネットに分割したうえで、分離したサブネットに Web アプリケーション プロキシ サーバーをデプロイすることができます。 単にネットワーク セキュリティ グループの設定をサブネットごとに構成し、その&2; つのサブネット間に必要な通信のみを許可してください。 以降、デプロイのシナリオに従って詳しく説明します。

## <a name="steps-to-deploy-ad-fs-in-azure"></a>AD FS を Azure にデプロイする手順
このセクションで取り上げる手順は、以下に示した AD FS インフラストラクチャを Azure にデプロイするうえでの指針を概説したものです。

### <a name="1-deploying-the-network"></a>1.ネットワークをデプロイする
既に述べたように、1 つの仮想ネットワークに&2; つのサブネットを作成するか、まったく異なる&2; つの仮想ネットワーク (VNet) を作成することができます。 この記事では、1 つの仮想ネットワークをデプロイして&2; つのサブネットに分割する方法について重点的に説明します。 現時点では、その方が簡単です。2 つの独立した VNet を使用した場合、通信に VNet 間ゲートウェイが必要となります。

**1.1 仮想ネットワークを作成する**

![Create virtual network](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Azure ポータルで仮想ネットワークを選択すると、その仮想ネットワークと&1; つのサブネットを&1; 回のクリックですぐにデプロイできます。 INT サブネットも定義されて VM を追加する準備が整います。
次の手順でこのネットワークにもう&1; つのサブネット (DMZ サブネット) を追加します。 DMZ サブネットを作成するには、次の手順を実行するだけです。

* 新しく作成したネットワークを選択する
* プロパティからサブネットを選択する
* サブネット パネルで追加ボタンをクリックする
* サブネットの名前とアドレス空間情報を指定してサブネットを作成する

![サブネット](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![Subnet DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2.ネットワーク セキュリティ グループを作成する**

ネットワーク セキュリティ グループ (NSG) には、仮想ネットワークの VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。
このガイドでは、その目的上、内部ネットワーク用と DMZ 用の&2; つの NSG を作成します。 それぞれ NSG_INT と NSG_DMZ という名前を付けます。

![Create NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

NSG の作成直後に存在する受信規則と送信規則は、いずれも 0 個です。 それぞれのサーバー上にロールがインストールされ正しく動作する状態になってから、必要なセキュリティ レベルに従って受信規則と送信規則を作成することができます。

![Initialize NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

NSG の作成後、NSG_INT をサブネット INT に、NSG_DMZ をサブネット DMZ に関連付けます。 以下のスクリーンショットはその例です。

![NSG configure](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* [サブネット] をクリックしてサブネットのパネルを開きます。
* NSG に関連付けるサブネットを選択します。 

構成後の [サブネット] のパネルは次のように表示されている必要があります。

![Subnets after NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3.オンプレミスへの接続を作成する**

Azure にドメイン コントローラー (DC) をデプロイするためには、オンプレミスとの接続が必要となります。 Azure には、オンプレミスのインフラストラクチャを Azure のインフラストラクチャに接続するためのさまざまな接続方法が用意されています。

* ポイント対サイト
* Virtual Network サイト間
* ExpressRoute

推奨されるのは、ExpressRoute を使用する方法です。 ExpressRoute を使用すると、Azure のデータセンターとオンプレミスや共用環境にあるインフラストラクチャ間にプライベート接続を作成できます。 ExpressRoute 接続では、公共のインターネットを利用できません。 ExpressRoute 接続は一般的なインターネットでの接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供します。
ExpressRoute の使用をお勧めしますが、所属する組織に合った任意の接続方法を選ぶことができます。 ExpressRoute と ExpressRoute を使用した各種の接続方法の詳細については、「 [ExpressRoute の技術概要](https://aka.ms/Azure/ExpressRoute)」を参照してください。

### <a name="2-create-storage-accounts"></a>2.ストレージ アカウントを作成する
高い可用性を維持し、1 つのストレージ アカウントへの依存を回避するために、ストレージ アカウントは&2; つ作成することができます。 可用性セットごとの&2; つのグループにマシンを分けたうえで、それぞれのグループに別個のストレージ アカウントを割り当てます。

![Create storage accounts](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3.可用性セットを作成する
それぞれのロール (DC/AD FS と WAP) について、最低 2 つのマシンを含んだ可用性セットを作成します。 そうすることで各ロールの可用性を高めることができます。 可用性セットを作成する際は、次の点についての意思決定が必須となります。

* **障害ドメイン**: 同じ障害ドメインの仮想マシンは、同じ電源と物理ネットワーク スイッチを共有します。 障害ドメインは 2 以上とすることをお勧めします。 ここでは既定値の 3 をそのまま使用します。
* **更新ドメイン**: 同じ更新ドメインに属しているマシンは、更新時に一緒に再起動されます。 更新ドメインは 2 以上とすることをお勧めします。 ここでは既定値の 5 をそのまま使用します。

![可用性セット](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

次の可用性セットを作成します。

| 可用性セット | 役割 | 障害ドメイン | 更新ドメイン |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4----deploy-virtual-machines"></a>4.  仮想マシンをデプロイする
次に、インフラストラクチャ内の各ロールのホストとなる仮想マシンをデプロイします。 それぞれの可用性セットには、最低でも&2; つのマシンをデプロイすることをお勧めします。 基本的なデプロイでは&6; つの仮想マシンを作成します。

| マシン | 役割 | サブネット | 可用性セット | ストレージ アカウント | IP アドレス |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |静的 |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |静的 |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |静的 |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |静的 |

お気付きかもしれませんが、NSG が指定されていません。 これは NSG が、Azure ではサブネット レベルで使用できるためです。 マシンのネットワーク トラフィックは、サブネットまたは NIC オブジェクトに関連付けられている個々の NSG を使って制御できます。 詳しくは、「 [ネットワーク セキュリティ グループ (NSG) について](https://aka.ms/Azure/NSG)」をご覧ください。
DNS が管理下にある場合は、静的 IP アドレスをお勧めします。 Azure DNS を使用し、ドメインの DNS レコードの方から、対応する Azure FQDN で新しいマシンを参照することができます。
デプロイの完了後、[仮想マシン] ウィンドウは次のように表示されている必要があります。

![Virtual Machines deployed](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5.ドメイン コントローラー / AD FS サーバーを構成する
 受信要求を認証するためには、AD FS がドメイン コントローラーに問い合わせを行う必要があります。 認証のたびに Azure がオンプレミスの DC とやり取りするのでは無駄が大きいため、ドメイン コントローラーのレプリカを Azure にデプロイすることをお勧めします。 高い可用性を確保するために、少なくとも 2 つのドメイン コントローラーから成る可用性セットを作成することをお勧めします。

| ドメイン コントローラー | 役割 | ストレージ アカウント |
|:---:|:---:|:---:|
| contosodc1 |レプリカ |contososac1 |
| contosodc2 |レプリカ |contososac2 |

* 2 台のサーバーをレプリカ ドメイン コントローラー (DNS を含む) として昇格させます。
* サーバー マネージャーを使用して AD FS ロールをインストールし、AD FS サーバーを構成します。

### <a name="6----deploying-internal-load-balancer-ilb"></a>6.  内部ロード バランサー (ILB) をデプロイする
**6.1.  ILB を作成する**

ILB をデプロイするには、Azure ポータルで [ロード バランサー] を選択し、[追加] \(+) をクリックします。

> [!NOTE]
> メニューに **[ロード バランサー]** が表示されない場合は、ポータルの左下にある **[参照]** をクリックし、**[ロード バランサー]** が表示されるまでスクロールします。  その後、黄色の星印をクリックするとメニューに追加されます。 この新しいロード バランサー アイコンを選択してパネルを開き、ロード バランサーの構成を開始してください。
> 
> 

![Browse load balancer](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **[名前]**: ロード バランサーに適切な名前を付けます。
* **[スキーム]**: このロード バランサーは AD FS サーバーの前に配置されます。つまり内部的なネットワーク接続のみを意図したものであるため、[内部] を選択します。
* **[仮想ネットワーク]**: AD FS をデプロイする仮想ネットワークを選択します。
* **[サブネット]**: 内部サブネットを選択します。
* **[IP アドレスの割り当て]**: 動的

![内部ロード バランサー](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

[作成] をクリックして ILB をデプロイすると、その ILB がロード バランサーの一覧に表示されます。

![Load balancers after ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

次の手順では、バックエンド プールとバックエンド プローブを構成します。

**6.2.  ILB バックエンド プールを構成する**

新しく作成した ILB を [ロード バランサー] パネルで選択し、 設定パネルを開きます。 

1. 設定パネルからバックエンド プールを選択します。
2. [バックエンド プールの追加] パネルで [仮想マシンの追加] をクリックします。
3. パネルが表示され、可用性セットを選択することができます。
4. AD FS の可用性セットを選択します。

![Configure ILB backend pool](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3.  プローブを構成する**

[ILB 設定] パネルで [プローブ] を選択します。

1. [追加] をクリックします。
2. プローブの詳細を入力します。a.  **[名前]**: プローブ名。b.  **[プロトコル]**: TCP。c.  **[ポート]**: 443 (HTTPS)。d.  **[間隔]**: 5 (既定値) - この値は、ILB がバックエンド プール内の仮想マシンをプローブする間隔です。e.  **[Unhealthy threshold limit (異常しきい値)]**: 2 (既定値) - これはプローブ エラーの連続回数のしきい値です。この値を超えると、ILB はバックエンド プール内の仮想マシンが応答していないと判断し、トラフィックの送信を停止します。

![Configure ILB probe](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4.  負荷分散規則を作成する**

トラフィックのバランスを効果的に調整するためには、負荷分散規則を使って ILB を構成する必要があります。 負荷分散規則を作成するには、 

1. ILB の設定パネルから負荷分散規則を選択します。
2. [負荷分散規則] パネルの [追加] をクリックします。
3. [負荷分散規則の追加] パネルで、a.  **[名前]**: 規則の名前を入力します。b.  **[プロトコル]**: [TCP] を選択します。c.  **[ポート]**: 443。d.  **[バックエンド ポート]**: 443。e.  **[バックエンド プール]**: AD FS クラスター用に作成しておいたプールを選択します。f.  **[プローブ]**: AD FS サーバー用に作成しておいたプローブを選択します。

![Configure ILB balancing rules](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5.  DNS に ILB を反映する**

DNS サーバーに移動して、ILB の CNAME を作成します。 CNAME には、IP アドレスが ILB の IP アドレスを指すフェデレーション サービスの値を指定する必要があります。 たとえば ILB の DIP アドレスが 10.3.0.8 であるとき、インストールされているフェデレーション サービスが fs.contoso.com である場合は、10.3.0.8 を指す fs.contoso.com の CNAME を作成します。
これで fs.contoso.com についてのすべての通信は最終的に ILB に到達し、適切にルーティングされます。

### <a name="7----configuring-the-web-application-proxy-server"></a>7.  Web アプリケーション プロキシ サーバーを構成する
**7.1.  AD FS サーバーに到達するための構成を Web アプリケーション プロキシ サーバーに対して行う**

Web アプリケーション プロキシ サーバーが ILB の内側にある AD FS サーバーに到達するためには、%systemroot%\system32\drivers\etc\hosts にその ILB のレコードを作成する必要があります。 識別名 (DN) は、フェデレーション サービスの名前 (例: fs.contoso.com) となることに注意してください。 また IP には、ILB の IP アドレスを入力する必要があります (この例では&10;.3.0.8)。

**7.2.  Web アプリケーション プロキシ ロールをインストールする**

Web アプリケーション プロキシ サーバーから ILB の内側の AD FS サーバーに到達できる状態を確保したら、続けて Web アプリケーション プロキシ サーバーをインストールすることができます。 Web アプリケーション プロキシ サーバーはドメインに参加しません。 リモート アクセス ロールを選択して、2 つの Web アプリケーション プロキシ サーバーに Web アプリケーション プロキシ ロールをインストールします。 サーバー マネージャーの指示に従って、WAP のインストールを実行してください。
WAP のデプロイ方法について詳しくは、「 [Web アプリケーション プロキシ サーバーをインストールし、構成する](https://technet.microsoft.com/library/dn383662.aspx)」をご覧ください。

### <a name="8----deploying-the-internet-facing-public-load-balancer"></a>8.  インターネット接続 (パブリック) ロード バランサーをデプロイする
**8.1.  インターネット接続 (パブリック) ロード バランサーを作成する**

Azure ポータルで [ロード バランサー] を選択し、[追加] をクリックします。 [ロード バランサーの作成] パネルで次の情報を入力します。

1. **[名前]**: ロード バランサーの名前。
2. **[スキーム]**: [パブリック] - このオプションを選択することで、作成するロード バランサーにパブリック アドレスが必要であるという情報が Azure に提供されます。
3. **[IP アドレス]**: 新しい IP アドレス (動的) を作成します。

![インターネットに接続するロード バランサー](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

デプロイしたロード バランサーは、[ロード バランサー] の一覧に表示されます。

![Load balancer list](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2.  パブリック IP に DNS ラベルを割り当てる**

新しく作成したロード バランサーのエントリを [ロード バランサー] パネルでクリックして、構成用のパネルを表示します。 次の手順に従ってパブリック IP の DNS ラベルを構成してください。

1. 対象のパブリック IP アドレスをクリックします。 パブリック IP とその設定に必要なパネルが開きます。
2. [構成] をクリックします。
3. DNS ラベルを指定します。 これが、任意の場所からアクセスできるパブリック DNS ラベルになります (例: contosofs.westus.cloudapp.azure.com)。 外部 DNS には、この外部ロード バランサーの DNS ラベル (contosofs.westus.cloudapp.azure.com) に解決されるフェデレーション サービスのエントリ (例: fs.contoso.com) を追加してください。

![Configure internet facing load balancer](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configure internet facing load balancer (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3.  インターネット接続 (パブリック) ロード バランサーのバックエンド プールを構成する** 

内部ロード バランサーの作成と同じ手順に従って、インターネット接続 (パブリック) ロード バランサーのバックエンド プールを WAP サーバーの可用性セットとして構成します。 たとえば「contosowapset」とします。

![Configure backend pool of Internet Facing Load Balancer](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4.  プローブを構成する**

内部ロード バランサーの構成と同じ手順に従って、WAP サーバーのバックエンド プールに使用するプローブを構成します。

![Configure probe of Internet Facing Load Balancer](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5.  負荷分散規則を作成する**

ILB と同じ手順に従って、TCP 443 の負荷分散規則を構成します。

![Configure balancing rules of Internet Facing Load Balancer](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9----securing-the-network"></a>9.  ネットワークを保護する
**9.1.  内部サブネットを保護する**

内部サブネットを効率的に保護するには、一般に次の規則が (以下に記載された順序で) 必要となります。

| ルール | 説明 | フロー |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |DMZ からの HTTPS 通信を許可します。 |受信 |
| DenyInternetOutbound |インターネットへのアクセスを禁止します。 |送信 |

![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comment]: <> (![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comment]: <> (![INT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))

**9.2.  DMZ サブネットを保護する**

| ルール | 説明 | フロー |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |インターネットから DMZ への HTTPS を許可します。 |受信 |
| DenyInternetOutbound |インターネットへの通信は HTTPS を除きすべてブロックします。 |送信 |

![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comment]: <> (![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comment]: <> (![EXT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

> [!NOTE]
> クライアント ユーザー証明書認証 (X509 ユーザー証明書を使用した clientTLS 認証) が必要である場合、AD FS の要件として、受信アクセス用に TCP ポート 49443 を有効にする必要があります。
> 
> 

### <a name="10----test-the-ad-fs-sign-in"></a>10.  AD FS サインインをテストする
AD FS のテストは、IdpInitiatedSignon.aspx ページを使用して行うのが最も簡単です。 そのためには、AD FS のプロパティで IdpInitiatedSignOn を有効にする必要があります。 以下の手順に従って AD FS の設定を確認してください。

1. AD FS サーバーで PowerShell から以下のコマンドレットを実行し、このプロパティを有効にします。
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. 外部のコンピューターから https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx にアクセスします。  
3. 以下の AD FS ページが表示されたら成功です。

![Test login page](./media/active-directory-aadconnect-azure-adfs/test1.png)

サインインに成功すると、その旨のメッセージが表示されます (下図)。

![Test success](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Azure で AD FS をデプロイするためのテンプレート
このテンプレートでは、ドメイン コントローラー、AD FS、WAP にそれぞれ 2 つずつ使用する、6 つのマシンのセットアップをデプロイします。

[Azure での AD FS のデプロイ テンプレート](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

このテンプレートのデプロイ中、既存の仮想ネットワークを使用するか、新しい VNET を作成できます。 デプロイをカスタマイズするために使用できる各種のパラメーターについて、デプロイ プロセスにおけるパラメーターの使用方法の説明と共に次の一覧に記載します。 

| パラメーター | Description |
|:--- |:--- |
| 場所 |リソースのデプロイ先となるリージョン (例: 米国東部) |
| StorageAccountType |作成するストレージ アカウントの種類 |
| VirtualNetworkUsage |新しい仮想ネットワークを作成するか既存のものを使用するかを示します |
| VirtualNetworkName |作成する仮想ネットワークの名前 (新しい仮想ネットワークを使用する場合と既存のものを使用する場合の両方で必須) |
| VirtualNetworkResourceGroupName |既存の仮想ネットワークが存在するリソース グループの名前を指定します。 これは、既存の仮想ネットワークを使用している場合、既存の仮想ネットワークの ID がデプロイによって検出されるようにするための必須のパラメーターになります |
| VirtualNetworkAddressRange |新しい VNET のアドレス範囲 (新しい仮想ネットワークを作成する場合は必須) |
| InternalSubnetName |内部サブネットの名前 (新しい仮想ネットワークと既存の仮想ネットワークの両方で必須) |
| InternalSubnetAddressRange |ドメイン コントローラーと ADFS サーバーが含まれる内部サブネットのアドレス範囲 (新しい仮想ネットワークを作成する場合は必須) |
| DMZSubnetAddressRange |Windows アプリケーション プロキシ サーバーが含まれる DMZ サブネットのアドレス範囲 (新しい仮想ネットワークを作成する場合は必須) |
| DMZSubnetName |内部サブネットの名前 (新しい仮想ネットワークと既存の仮想ネットワークの両方で必須) |
| ADDC01NICIPAddress |最初のドメイン コントローラーの内部 IP アドレス。DC に静的に割り当てられるこの IP アドレスは、内部サブネットで有効な IP アドレスである必要があります |
| ADDC02NICIPAddress |2 番目のドメイン コントローラーの内部 IP アドレス。DC に静的に割り当てられるこの IP アドレスは、内部サブネットで有効な IP アドレスである必要があります |
| ADFS01NICIPAddress |最初の ADFS サーバーの内部 IP アドレス。ADFS サーバーに静的に割り当てられるこの IP アドレスは、内部サブネットで有効な IP アドレスである必要があります |
| ADFS02NICIPAddress |2 番目の ADFS サーバーの内部 IP アドレス。ADFS サーバーに静的に割り当てられるこの IP アドレスは、内部サブネットで有効な IP アドレスである必要があります |
| WAP01NICIPAddress |最初の WAP サーバーの内部 IP アドレス。WAP サーバーに静的に割り当てられるこの IP アドレスは、DMZ サブネットで有効な IP アドレスである必要があります |
| WAP02NICIPAddress |2 番目の WAP サーバーの内部 IP アドレス。WAP サーバーに静的に割り当てられるこの IP アドレスは、DMZ サブネットで有効な IP アドレスである必要があります |
| ADFSLoadBalancerPrivateIPAddress |ADFS ロード バランサーの内部 IP アドレス。ロード バランサーに静的に割り当てられるこの IP アドレスは、内部サブネットで有効な IP アドレスである必要があります |
| ADDCVMNamePrefix |ドメイン コントローラーの仮想マシン名のプレフィックス |
| ADFSVMNamePrefix |ADFS サーバーの仮想マシン名のプレフィックス |
| WAPVMNamePrefix |WAP サーバーの仮想マシン名のプレフィックス |
| ADDCVMSize |ドメイン コントローラーの VM サイズ |
| ADFSVMSize |ADFS サーバーの VM サイズ |
| WAPVMSize |WAP サーバーの VM サイズ |
| AdminUserName |仮想マシンのローカル管理者の名前 |
| AdminPassword |仮想マシンのローカル管理者アカウントのパスワード |

## <a name="additional-resources"></a>その他のリソース
* [可用性セット](https://aka.ms/Azure/Availability) 
* [Azure Load Balancer](https://aka.ms/Azure/ILB)
* [内部ロード バランサー](https://aka.ms/Azure/ILB/Internal)
* [インターネットに接続するロード バランサー](https://aka.ms/Azure/ILB/Internet)
* [ストレージ アカウント](https://aka.ms/Azure/Storage)
* [Azure 仮想ネットワーク](https://aka.ms/Azure/VNet)
* [AD FS と Web アプリケーション プロキシについてのリンク](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>次のステップ
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
* [Azure AD Connect を使用した AD FS の構成と管理](active-directory-aadconnectfed-whatis.md)
* [Azure Traffic Manager を使用した Azure への可用性に優れた地域間 AD FS デプロイ](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)


