---
title: "Azure Virtual Machines での Windows Server Active Directory のデプロイ ガイドライン | Microsoft Docs"
description: "AD ドメイン サービスと AD フェデレーション サービスをオンプレミスにデプロイする方法をご存じの方を対象に、Azure 仮想マシンへのデプロイ方法を説明します。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: c6d26aca309597cf9552e97a22e84b6c122fe58b
ms.lasthandoff: 03/08/2017


---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Azure 仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン
この記事では、Windows Server の Active Directory ドメイン サービス (AD DS) および Active Directory フェデレーション サービス (AD FS) に関して、オンプレミスへのデプロイと Microsoft Azure 仮想マシンへのデプロイとの大きな違いについて説明します。

## <a name="scope-and-audience"></a>対象範囲と対象読者
この記事は、過去に Active Directory をオンプレミスにデプロイしたことのある方を対象としています。 Active Directory を Microsoft Azure 仮想マシン/Azure 仮想ネットワークにデプロイする場合と、従来のオンプレミス Active Directory 環境にデプロイする場合との違いを取り上げます。 Azure 仮想マシンと Azure 仮想ネットワークは、クラウド内のコンピューティング リソースを組織的に利用するための Infrastructure-as-a-Service (IaaS) 製品の構成要素です。

AD をデプロイした経験のない方は、「[AD DS Deployment Guide (AD DS デプロイ ガイド)](https://technet.microsoft.com/library/cc753963)」または「[AD FS の配置を計画する](https://technet.microsoft.com/library/dn151324.aspx)」を適宜参照してください。

この記事は、次の概念を理解していることを前提としています。

* Windows Server AD DS のデプロイと管理
* Windows Server AD DS インフラストラクチャをサポートするための DNS のデプロイと構成
* Windows Server AD FS のデプロイと管理
* Windows Server AD FS のトークンを利用できる証明書利用者アプリケーション (Web サイトや Web サービス) のデプロイ、構成、管理
* 一般的な仮想マシンの概念 (仮想マシン、仮想ディスク、仮想ネットワークを構成する方法など)

この記事では、Windows Server AD DS または Windows Server AD FS のデプロイ先としてオンプレミスと Azure 仮想マシンの両方を使用するハイブリッド デプロイの要件について取り上げます。 まず、Windows Server AD DS と Windows Server AD FS を Azure 仮想マシンで実行することとオンプレミスで実行することの大きな違いを説明したうえで、設計とデプロイを左右する重要な意思決定について説明しています。 その他、意思決定の要点ごとのガイドラインを詳しく説明し、そのガイドラインを各種のデプロイ シナリオに適用する方法を紹介します。

[Azure Active Directory](http://azure.microsoft.com/services/active-directory/)は、クラウド アプリケーションの ID 管理とアクセス制御機能を持った REST ベースのサービスであり、その構成についてはこの記事では取り上げません。 ただし、今日見られるような異種混合の IT 環境と先進的なアプリケーションの ID 管理およびアクセス管理の機能を果たすために、Azure Active Directory (Azure AD) と Windows Server AD DS は互いに連携する設計になっています。 以下の点を踏まえておくと、Windows Server AD DS と Azure AD の違いや両者の関係がわかりやすくなります。

1. Azure を使って既存のオンプレミス データセンターをクラウドに拡張するときは、Windows Server AD DS をクラウドの Azure 仮想マシン上で実行します。
2. SaaS (Software-as-a-Service) アプリケーションに対してユーザーがシングル サインオンできるようにするには、Azure AD を使用します。 たとえば、Microsoft Office 365 ではこのテクノロジが使用されており、Azure やその他のクラウド プラットフォームで実行されるアプリケーションでも使用できます。
3. クラウドまたはオンプレミスでホストされているアプリケーションに対し、ユーザーが Facebook、Google、Microsoft など各種 ID プロバイダーに由来する ID を使ってサインインできるようにするには、Azure AD (Access Control Service) を使用します。

これらの相違点の詳細については、「 [Azure ID 管理の基礎](fundamentals-identity.md)」を参照してください。

## <a name="related-resources"></a>関連リソース
準備状況は、 [Azure Virtual Machines Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898)をダウンロードし、実行することによって評価できます。 現在のオンプレミス環境を自動的に検査し、このトピックの指針に基づいたカスタム レポートを生成して、Azure 環境への移行を支援する評価ツールです。

また、以下のトピックについてのチュートリアル、ガイド、ビデオを最初にご覧になるようお勧めします。

* [Azure Portal を使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Azure クラシック ポータルでサイト間 VPN 接続を使用して仮想ネットワークを作成する](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Azure の仮想ネットワークでの Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)
* [Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) 仮想マシンの基礎](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) 仮想ネットワークとクロスプレミス接続の作成](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>はじめに
Windows Server Active Directory を Azure 仮想マシンにデプロイするための基本的な要件は、オンプレミスの仮想マシンにデプロイする場合とほとんど変わりません (物理マシンにデプロイする場合とも、ある程度共通)。 たとえば Windows Server AD DS について言えば、Azure 仮想マシンにデプロイするドメイン コントローラー (DC) が、既にあるオンプレミスの企業ドメイン/フォレスト内のレプリカである場合、Azure へのデプロイも、Windows Server Active Directory サイトを追加するときと、ほぼ同じ方法で行うことができます。 つまり、Windows Server AD DS にサブネットを定義してサイトを作成し、サブネットとサイトを接続したうえで、適切なサイト リンクを使って他のサイトに接続する必要があります。 ただし、一部の違いは Azure へのどのデプロイ シナリオにも共通していますが、デプロイ シナリオに固有のものもあります。 以降、2 つの基本的な違いについて説明します。

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure 仮想マシンがオンプレミスの企業ネットワークに接続されている必要がある。
Azure 仮想マシンをオンプレミスの企業ネットワークに折り返し接続するためには、Azure 仮想マシンとオンプレミスのコンピューターとを Azure 仮想ネットワークでシームレスに接続できなければなりません。Azure 仮想ネットワークには、サイト間仮想プライベート ネットワーク (VPN) コンポーネントまたはサイト対ポイントの仮想プライベート ネットワーク (VPN) コンポーネントが含まれています。 ドメイン コントローラーが Azure 仮想マシンにのみホストされている Windows Server Active Directory ドメインに対して、オンプレミスのドメイン メンバー コンピューターからアクセスするときにも、この VPN コンポーネントが必要となります。 ただし、この VPN で障害が発生した場合、Windows Server Active Directory に依存する操作 (認証など) にも支障が生じます。 既にキャッシュされている資格情報を使ってユーザーがサインインできる場合もありますが、チケットがまだ発行されていない、または古くなっている、ピア ツー ピアまたはクライアント ツー サーバーの認証はすべて失敗します。

[Azure Portal でのサイト間 VPN の構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)など詳しい手順を説明した一連のチュートリアルとデモンストレーション ビデオについては、[Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) に関するページをご覧ください。

> [!NOTE]
> オンプレミス ネットワークに接続されていない Azure 仮想ネットワークで Windows Server Active Directory をデプロイすることもできます。 ただし Windows Server には IP アドレス指定機能が不可欠であるため、このトピックのガイドラインは、その機能を備えた Azure 仮想ネットワークが使用されていることを前提とします。
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>静的 IP アドレスは Azure PowerShell を使って構成する必要がある。
既定では動的アドレスが割り当てられますが、静的 IP アドレスを割り当てるには、Set-AzureStaticVNetIP コマンドレットを使用します。 このコマンドレットによって設定される静的 IP アドレスは、サービス復旧後や VM のシャットダウン/再起動後も変化しません。 詳細については、「 [Static internal IP address for virtual machines (仮想マシンに使用する静的な内部 IP アドレス)](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)」を参照してください。

## <a name="BKMK_Glossary"></a>用語と定義
以下、この記事に出現する各種 Azure テクノロジで用いられているいくつかの用語をピックアップしました。

* **Azure Virtual Machines**: Azure の IaaS 製品。この製品を使って VM をデプロイすることで、従来オンプレミスで運用されてきたほぼすべてのサーバー ワークロードを実行することができます。
* **Azure Virtual Network**: Azure のネットワーク サービス。Azure 内で仮想ネットワークを作成、管理し、仮想プライベート ネットワーク (VPN) で自社のオンプレミス ネットワーク インフラストラクチャに安全に接続することができます。
* **仮想 IP アドレス**: 特定のコンピューターやネットワーク インターフェイス カードに関連付けられていない、インターネットに接続する IP アドレス。 クラウド サービスには、Azure VM にリダイレクトされたネットワーク トラフィックを受信するための仮想 IP アドレスが割り当てられます。 仮想 IP アドレスは、1 つまたは複数の Azure 仮想マシンのホストとなるクラウド サービスのプロパティです。 Azure 仮想ネットワークに、1 つまたは複数のクラウド サービスが存在する場合もあります。 複数の仮想 IP アドレスによってネイティブの負荷分散機能が実現されます。
* **動的 IP アドレス**: 内部的にのみ使用される IP アドレス。 DC/DNS サーバー ロールのホストとなる VM に対しては、(Set-AzureStaticVNetIP コマンドレットを使用して) 静的 IP アドレスとして構成する必要があります。
* **サービス復旧**: Azure がサービスの障害を検出した後、自動的にサービスを稼働状態に戻すプロセス。 サービス復旧は、可用性と回復性をサポートする Azure の特性の一つです。 万一 VM 上で動作している DC でサービス復旧インシデントが発生した場合、計画外の再起動と同様の結果になります。ただしこれには、次のような副作用が伴います。
  
  * VM の仮想ネットワーク アダプターが変わる。
  * 仮想ネットワーク アダプターの MAC アドレスが変わる。
  * VM のプロセッサ/CPU ID が変わる。
  * VM が仮想ネットワークに接続され、VM の IP アドレスが静的アドレスである間は、仮想ネットワーク アダプターの IP 構成が変更されない。
  
  Windows Server Active Directory は MAC アドレスやプロセッサ/CPU ID には依存していないため、いずれの動作の影響も受けません。Azure に対するすべての Windows Server Active Directory デプロイは、前述のように Azure 仮想ネットワークで実行することをお勧めします。

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Windows Server Active Directory ドメイン コントローラーを仮想化しても安全か
Windows Server Active Directory DC を Azure 仮想マシンにデプロイする場合、オンプレミスの仮想マシンで DC を実行するときと同じガイドラインが適用されます。 DC のバックアップと復元のガイドラインに従っていれば、DC を仮想化して実行することに問題はありません。 DC を仮想化して実行する場合の制約とガイドラインの詳細については、「 [Hyper-V でのドメイン コントローラーの実行](https://technet.microsoft.com/library/dd363553)」を参照してください。

通常ならありえない手法がハイパーバイザーであれば簡単にできてしまい、それが Windows Server Active Directory を含め、さまざまな分散システムの問題を引き起こす場合があります。 たとえば物理サーバーでも、ディスクを複製したり、サポート外の手段 (SAN など) でサーバーの状態をロールバックしたりすることはできます。しかしそれはハイパーバイザーで仮想マシンのスナップショットを復元することに比べれば、はるかに敷居の高い作業です。 同様の不適切な状態は、Azure の機能においても起こりえます。 たとえば、DC の VHD ファイルはコピーするのではなく定期的にバックアップを行ってください。コピーした VHD ファイルを使用して DC を復元すると、スナップショットの復元機能を使った場合と似た状況になることがあります。

そのようなロールバックにより USN バブルが発生して DC 間で常に不整合が生じることがあります。 その結果、次のような状態が生じます。

* 残留オブジェクト
* パスワードの不整合
* 属性値の不整合
* スキーマの不一致 (スキーマ マスターがロールバックされた場合)

DC が受ける影響の詳細については、「 [Hyper-V でのドメイン コントローラーの実行](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback)」を参照してください。

Windows Server 2012 以降では、[特別な保護機能が AD DS に組み込まれています](https://technet.microsoft.com/library/hh831734.aspx)。 土台となるハイパーバイザー プラットフォームが VM-GenerationID に対応していれば、ここに挙げた問題から仮想ドメイン コントローラーを保護することが可能です。 Azure は VM-GenerationID に対応しています。つまり、Azure 仮想マシン上で Windows Server 2012 以降を実行しているドメイン コントローラーには、その特別な保護機能が備わっています。

> [!NOTE]
> Azure 内でドメイン コントローラー ロールを実行する VM のシャットダウンと再起動は、Azure Portal またはクラシック ポータルの **[シャットダウン]** オプションを使用せずに、ゲスト オペレーティング システム内で行う必要があります。 現在、ポータルを使用して VM をシャットダウンすると、VM の割り当てが解除されます。 割り当てが解除された VM は料金が発生しないという利点はありますが、VM-GenerationID がリセットされます。これは DC にとって好ましくありません。 VM-GenerationID がリセットされると、AD DS データベースの invocationID もリセットされ、RID プールは破棄され、SYSVOL は non-authoritative とマークされます。 詳細については、「[Active Directory Domain Services (AD DS) の仮想化 (レベル&100;) の概要](https://technet.microsoft.com/library/hh831734.aspx)」と「[Safely Virtualizing DFSR (DFSR の安全な仮想化)](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx)」を参照してください。
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Windows Server AD DS を Azure 仮想マシンにデプロイする理由
Windows Server AD DS は多くの場合、Azure 上の VM としてデプロイすることができます。 たとえば、ヨーロッパに拠点のある会社で、アジアのリモート オフィスにいるユーザーを認証する必要があるとします。 この会社はこれまでアジアに Windows Server Active Directory DC をデプロイしたことはありません。デプロイに必要なコストが大きいうえに、サーバー デプロイ後の管理に必要な知識や経験も限られていたためです。 やむを得ずアジアからの認証要求は、ヨーロッパにある DC で処理しており、パフォーマンスは決して最善とはいえません。 このような場合、アジアの Azure データセンター内で実行するよう指定した VM に DC をデプロイすることができます。 リモート オフィスに直接接続されている Azure 仮想ネットワークにその DC を接続すれば、認証パフォーマンスが向上します。

また、Azure は、コストが大きくなりがちな障害復旧 (DR) サイトに代わる有力な選択肢でもあります。 少数のドメイン コントローラーと&1; つの仮想ネットワークを Azure でホストした方がコストが小さくて済むため、魅力的な選択肢となっています。

さらに、Windows Server Active Directory は使用するものの、オンプレミスのネットワークや社内の Windows Server Active Directory には依存していないネットワーク アプリケーション (SharePoint など) があれば、ぜひ Azure にデプロイすることを検討してください。 そのようなケースでは、フォレストを分離して Azure にデプロイすることにより、SharePoint サーバーの要件を最適な形で満たすことができます。 もちろん、オンプレミス ネットワークと社内 Active Directory への接続を必要とするネットワーク アプリケーションのデプロイもサポートされています。

> [!NOTE]
> Azure 仮想ネットワーク内の VPN コンポーネントにより、オンプレミス ネットワークとの間でレイヤー&3; 接続が可能であるため、オンプレミスで動作するメンバー サーバーも、Azure 仮想ネットワークで Azure 仮想マシンとして動作する DC を活用できます。 しかし VPN が利用できない場合は、オンプレミスのコンピューターは Azure ベースのドメイン コントローラーと通信できず、認証エラーをはじめとする各種のエラーが発生します。  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Windows Server Active Directory ドメイン コントローラーを Azure 仮想マシンにデプロイするケースとオンプレミスにデプロイするケースの比較
* Windows Server Active Directory のデプロイで複数の VM を伴う場合は、IP アドレスの整合性を確保するために Azure 仮想ネットワークを使用する必要があります。 このガイドは、DC が Azure 仮想ネットワークで動作していることを前提としています。
* オンプレミスの DC と同様に、静的 IP アドレスをお勧めします。 静的 IP アドレスは Azure PowerShell を使って構成する必要があります。 詳細については、「 [Static internal IP address for VMs (仮想マシンに使用する静的な内部 IP アドレス)](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) 」を参照してください。 ゲスト オペレーティング システム内の静的 IP アドレスの構成をチェックする監視システムなどがある場合は、同じ静的 IP アドレスを VM のネットワーク アダプターのプロパティに割り当ててかまいません。 ただし、VM がサービス復旧を受けているか、またはクラシック ポータルでシャットダウンされ、そのアドレスの割り当てが解除されている場合は、ネットワーク アダプターは無視されることに注意してください。 その場合、ゲスト内の静的 IP アドレスをリセットする必要があります。
* 仮想ネットワークに VM をデプロイするからといって、オンプレミス ネットワークへの折り返し接続が暗黙に必要になるわけではありません。仮想ネットワークでそのような接続が可能であるというだけです。 Azure とオンプレミス ネットワークとの間でプライベートな通信を行うためには、仮想ネットワークを作成する必要があります。 オンプレミス ネットワークには VPN エンドポイントをデプロイする必要があります。 VPN は Azure 側からオンプレミス ネットワークに対して確立されます。 詳細については、「[仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」と「[Azure クラシック ポータルでサイト間 VPN 接続を使用して仮想ネットワークを作成する](../vpn-gateway/vpn-gateway-site-to-site-create.md)」を参照してください。

> [!NOTE]
> Windows ベースの各コンピューターを Azure 仮想ネットワークに直接接続するときには、 [ポイント対サイト VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) を作成するという選択肢もあります。
> 
> 

* 仮想ネットワークを作成するかどうかに関係なく、Azure の課金対象は送信トラフィックであって受信トラフィックではありません。 デプロイによって生成される送信トラフィックの量は、Windows Server Active Directory の設計上のさまざまな選択によって変わる可能性があります。 たとえばデプロイの対象が読み取り専用のドメイン コントローラー (RODC) である場合、送信方向のレプリケートがない分、送信トラフィックは限定的です。 しかし RODC をデプロイするという判断は、DC に対して書き込み操作を行う必要性や、サイト内のアプリケーションやサービスと RODC との [互換性](https://technet.microsoft.com/library/cc755190) を考慮して慎重に検討する必要があります。 トラフィックの料金の詳細については、「 [Azure の価格](http://azure.microsoft.com/pricing/)」を参照してください。
* オンプレミスの VM では、使用するサーバー リソース (RAM やディスクのサイズなど) をすべて自分で決定できますが、Azure では、あらかじめ構成された一連のサーバー サイズから選択する必要があります。 DC の場合、オペレーティング システム ディスクに加えて、Windows Server Active Directory データベースを保存するためのデータ ディスクも必要です。

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Windows Server AD FS を Azure 仮想マシンにデプロイできますか?
はい、できます。Azure 仮想マシンには Windows Server AD FS をデプロイすることができます。オンプレミスに [AD FS をデプロイする場合のベスト プラクティス](https://technet.microsoft.com/library/dn151324.aspx)が、Azure にデプロイする場合にも同じように適用されます。 ただし一部のベスト プラクティスには、負荷分散や高可用性など、AD FS そのものには備わっていないテクノロジが必要となります。 こうしたテクノロジは、基盤となるインフラストラクチャに備わっている必要があります。 以下、これらのベスト プラクティスをいくつか紹介し、それらを Azure VM と Azure 仮想ネットワークを使用して実現する方法を見ていきましょう。

1. **セキュリティ トークン サービス (STS) サーバーをインターネットに直接公開しない。**
   
    STS はセキュリティ トークンを発行するため、この点に注意が必要です。 そのため、AD FS サーバーなどの STS サーバーの扱いには、ドメイン コントローラーと同じレベルの保護が必要となります。 STS のセキュリティが侵害されると、悪意のあるユーザーが、証明書利用者アプリケーションや信頼関係にある組織内の他の STS サーバーに関して、独自に選んだ内容の要求をアクセス トークンに含めて発行できる状態となります。
2. **すべてのユーザー ドメインの Active Directory ドメイン コントローラーを AD FS サーバーと同じネットワークにデプロイする。**
   
    AD FS サーバーは、ユーザーの認証に Active Directory ドメイン サービスを使用します。 AD FS サーバーと同じネットワークにドメイン コントローラーをデプロイするようお勧めします。 そうすることで、Azure ネットワークとオンプレミス ネットワークとの間でリンク障害が発生してもビジネスの継続性が保たれ、待機時間が短くなり、ログインのパフォーマンスが向上します。
3. **複数の AD FS ノードをデプロイして高可用性を確保し、負荷を分散する。**
   
    ほとんどの場合、セキュリティ トークンを必要とするアプリケーションはミッション クリティカルであることが多いため、AD FS によって実現されるアプリケーションに障害は許されません。 このように AD FS は、ミッション クリティカルなアプリケーションにアクセスするためのクリティカル パスに存在することとなるため、複数の AD FS プロキシと AD FS サーバーを使って AD FS サービスには高い可用性を確保する必要があります。 要求を分散させるために、AD FS プロキシと AD FS サーバーの前には通常、ロード バランサーをデプロイします。
4. **インターネット アクセス用に Web アプリケーション プロキシ ノードをデプロイする。**
   
    AD FS サービスによって保護されているアプリケーションにユーザーがアクセスするときは、インターネットから AD FS サービスにアクセスできる必要があります。 これは Web アプリケーション プロキシ サービスをデプロイすることで実現できます。 高可用性を確保し負荷を分散させるために、複数のノードをデプロイすることを強くお勧めします。
5. **Web アプリケーション プロキシ ノードから内部ネットワーク リソースへのアクセスを制限する。**
   
    外部ユーザーがインターネットから AD FS にアクセスできるようにするには、Web アプリケーション プロキシ ノード (または以前のバージョンの Windows Server の AD FS プロキシ) をデプロイする必要があります。 Web アプリケーション プロキシ ノードは、直接インターネットに公開されます。 これらはドメインに参加している必要はなく、TCP ポート 443 と 80 経由で AD FS サーバーにアクセスできることのみ必要となります。 その他すべてのコンピューター (特にドメイン コントローラー) への通信はブロックすることを強くお勧めします。
   
    この作業は通常、オンプレミスで DMZ を使って行います。 ファイアウォールは、ホワイトリスト方式の動作モードで、DMZ からオンプレミス ネットワークへのトラフィックを制限します (つまり、指定 IP アドレスから指定ポートを経由するトラフィックのみを許可し、それ以外のトラフィックはすべてブロックします)。

次の図に示したのは、従来からあるオンプレミスへの AD FS のデプロイです。

![Diagram of a traditional on-premises Active Directory Federation Services deployment](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

一方 Azure にネイティブで備わっているファイアウォールは機能に限りがあるため、トラフィックを制限するためには他の方法が必要となります。 次の表に、それぞれの方法とその長所および短所を示します。

| オプション | 長所 | 短所 |
| --- | --- | --- |
| [Azure のネットワーク ACL](../virtual-network/virtual-networks-acl.md) |初期構成が低コストで単純 |新しい VM をデプロイに追加する場合は、ネットワーク ACL の構成が別途必要 |
| [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) |ホワイトリスト方式の動作モード (ネットワーク ACL の構成は不要) |初期セットアップのコストが大きく複雑 |

このケースで AD FS をデプロイする大まかな手順は次のとおりです。

1. VPN または [ExpressRoute](http://azure.microsoft.com/services/expressroute/)を使用して、クロスプレミス接続を備えた仮想ネットワークを作成します。
2. 仮想ネットワーク上にドメイン コントローラーをデプロイします。 この手順は省略可能ですが、実施することをお勧めします。
3. ドメインに参加している AD FS サーバーを仮想ネットワークにデプロイします。
4. AD FS サーバーを含み、仮想ネットワーク内で新しいプライベート IP アドレス (動的 IP アドレス) を使用する [内部負荷分散セット](http://azure.microsoft.com/blog/internal-load-balancing/) を作成します。
   
   1. DNS を更新して、内部負荷分散セットのプライベート (動的) IP アドレスを指す FQDN を作成します。
5. Web アプリケーション プロキシ ノードのクラウド サービス (または別個の仮想ネットワーク) を作成します。
6. クラウド サービスまたは仮想ネットワークに Web アプリケーション プロキシ ノードをデプロイします。
   
   1. Web アプリケーション プロキシ ノードを含む、外部負荷分散セットを作成します。
   2. クラウド サービスのパブリック IP アドレス (仮想 IP アドレス) を指すように外部 DNS 名 (FQDN) を更新します。
   3. AD FS サーバーの内部負荷分散セットに対応する FQDN を使用するように AD FS プロキシを構成します。
   4. 要求プロバイダーの外部 FQDN を使用するように、要求ベースの Web サイトを更新します。
7. Web アプリケーション プロキシとの間のアクセスを AD FS 仮想ネットワーク内のコンピューターに制限します。

トラフィックを制限するため、Azure 内部ロード バランサーの負荷分散セットは、TCP ポート 80 と 443 へのトラフィックのみを許可するように構成します。負荷分散セットの内部の動的 IP アドレスに対する他のトラフィックはすべて破棄します。

![Diagram of ADFS Network ACLs with TCP 443 + 80 permitted.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

AD FS サーバーへのトラフィックは、送信元が次に該当する場合にのみ許可されます。

* Azure 内部ロード バランサー。
* オンプレミス ネットワークの管理者の IP アドレス。

> [!WARNING]
> その他 Azure 仮想ネットワーク内の VM やオンプレミス ネットワーク上の場所に Web アプリケーション プロキシ ノードが到達できないように設計する必要があります。 そのためには、ExpressRoute 接続用のオンプレミスのアプライアンスまたはサイト間 VPN 接続用の VPN デバイスにファイアウォール ルールを構成します。
> 
> 

この方法の欠点は、内部ロード バランサーや AD FS サーバーなど、仮想ネットワークに追加するサーバーを含む複数のデバイスに対して、ネットワーク ACL を構成しなければならないことです。 デバイスをデプロイに追加するとき、そのデバイスへのトラフィックを制限するようにネットワーク ACL を構成しなかった場合、デプロイ全体が危険にさらされる可能性があります。 Web アプリケーション プロキシ ノードの IP アドレスが変化した場合、ネットワーク ACL をリセットする必要があります (そのためプロキシは、 [静的 IP アドレス](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)を使用するように構成することをお勧めします)。

![ADFS on Azure with network ACLS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

AD FS プロキシ サーバーと AD FS サーバーの間のトラフィックは、[Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) アプライアンスを使用して制御することもできます。 この方法は、セキュリティと高可用性の点でベスト プラクティスを満たしています。また、Barracuda NG Firewall アプライアンスはホワイトリスト モードのファイアウォール管理に対応しており、Azure 仮想ネットワークに直接設置できるため、初期セットアップ後はこちらの方が管理の手間が少なくて済みます。 新しいサーバーをデプロイに追加するたびにネットワーク ACL を構成する必要はありません。 ただしこの方法は、初期デプロイの複雑さとコストが大きくなります。

1 つ目の方法では、デプロイした仮想ネットワークが&1; つでしたが、このケースでは仮想ネットワークを&2; つデプロイします。 それらを VNet1 および VNet2 と呼ぶことにします。 VNet1 にはプロキシが、VNet2 には STS のほか、企業ネットワークへのネットワーク接続が存在します。 したがって、VNet1 は (あくまで仮想的にではありますが) 物理的に VNet2 から、ひいては企業ネットワークからも分離されます。 そのうえで VNet1 は、TINA (Transport Independent Network Architecture) という特殊なトンネリング テクノロジを使って VNet2 に接続されます。 TINA トンネルは、Barracuda NG Firewall を使用して個々の仮想ネットワークに接続されます (Barracuda は仮想ネットワークごとに&1; つ存在)。  高可用性を確保するために、各仮想ネットワークに&2; つの Barracuda (1 つはアクティブ、もう&1; つはパッシブ) をデプロイすることをお勧めします。 Barracuda が持つ豊富なファイアウォール機能によって、従来からあるオンプレミスの DMZ の動作を Azure で擬似的に再現することができます。

![ADFS on Azure with firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

詳細については、「 [AD FS: 要求対応のオンプレミス フロントエンド アプリケーションをインターネットに拡張する](#BKMK_CloudOnlyFed)」を参照してください。

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>AD FS デプロイに代わる方法 (Office 365 の SSO のみが目的である場合)
Office 365 のサインインに対応することのみが目的である場合は、AD FS をまったく使用しない別の選択肢があります。 単に DirSync とパスワード同期をオンプレミスにデプロイする方法です。この方法なら AD FS も Azure も必要ないため、デプロイに伴う複雑さを最小限にまったく同じ機能を実現できます。

次の表は、AD FS をデプロイした場合とデプロイしない場合のサインイン プロセスの動作を比較したものです。

| AD FS と DirSync を使用した Office 365 のシングル サインオン | DirSync とパスワード同期を使用した Office 365 の同一サインオン |
| --- | --- |
| 1.ユーザーが企業ネットワークにログオンして、Windows Server Active Directory に対して認証されます。 |1.ユーザーが企業ネットワークにログオンして、Windows Server Active Directory に対して認証されます。 |
| 2.ユーザーが Office 365 へのアクセスを試みます (@contoso.com を使用)。 |2.ユーザーが Office 365 へのアクセスを試みます (@contoso.com を使用)。 |
| 3.Office 365 がユーザーを Azure AD にリダイレクトします。 |手順 3.Office 365 がユーザーを Azure AD にリダイレクトします。 |
| 4.Azure AD はユーザーを認証できませんが、オンプレミスの AD FS との間に信頼関係があることがわかっているため、ユーザーを AD FS にリダイレクトします。 |4.Azure AD は Kerberos チケットを直接受理することができず、信頼関係が存在しないため、ユーザーに資格情報の入力を要請します。 |
| 5.ユーザーは Kerberos チケットを AD FS STS に送信します。 |5.ユーザーは、同じオンプレミスのパスワードを入力します。Azure AD は、DirSync によって同期されているユーザー名とパスワードに照らして入力内容を検証します。 |
| 6.AD FS は Kerberos チケットを必要なトークン形式/要求に変換し、ユーザーを Azure AD にリダイレクトします。 |6.Azure AD がユーザーを Office 365 にリダイレクトします。 |
| 7.ユーザーが Azure AD に対して認証されます (別の変換が行われます)。 |7.ユーザーは、Azure AD トークンを使用して、Office 365 と OWA にサインインできます。 |
| 8.Azure AD がユーザーを Office 365 にリダイレクトします。 | |
| 9.ユーザーは暗黙のうちに Office 365 にサインインします。 | |

Office 365 で DirSync とパスワード同期を使用する場合 (AD FS を使用しない場合)、シングル サインオンは "同一サインオン" で置き換えられます。ここで "同一" とは、ユーザーが Office 365 にアクセスするときに、オンプレミスと同じ資格情報を再入力する必要があることを意味します。 このデータをユーザーのブラウザーに記憶させることで、それ以降の入力回数を減らすことができます。

### <a name="additional-food-for-thought"></a>その他の考慮事項
* Azure 仮想マシンに AD FS プロキシをデプロイする場合は、AD FS サーバーへの接続が必要です。 AD FS サーバーがオンプレミスにある場合は、仮想ネットワークのサイト間 VPN 接続を利用して、Web アプリケーション プロキシ ノードが AD FS サーバーと通信できるようにすることをお勧めします。
* Azure 仮想マシンに AD FS サーバーをデプロイする場合は、Windows Server Active Directory ドメイン コントローラーと属性ストア、構成データベースへの接続が必要であり、Azure 仮想ネットワークとオンプレミス ネットワークの間に ExpressRoute 接続またはサイト間 VPN 接続が必要になる場合もあります。
* Azure 仮想マシンからのすべてのトラフィック (送信トラフィック) が課金の対象となります。 コストが最優先課題である場合は、Web アプリケーション プロキシ ノードを Azure にデプロイして、AD FS サーバーをオンプレミスに維持することをお勧めします。 AD FS サーバーを Azure 仮想マシンにもデプロイした場合、オンプレミス ユーザーの認証に別途コストが発生します。 送信トラフィックは、たとえ ExpressRoute または VPN サイト間接続を行き交うトラフィックであったとしてもコストが発生します。
* Azure にネイティブで備わっているサーバー負荷分散機能を使って AD FS サーバーの可用性を確保する場合、負荷分散機能からのプローブを使って、クラウド サービス内の仮想マシンの状態を調査できます。 Azure 仮想マシンの場合は (Web ロールや worker ロールとは違い、既定のプローブに応答するエージェントが存在しないため)、カスタム プローブを使用する必要があります。 簡単なしくみとしてはカスタム TCP プローブを使用できます。この場合 TCP 接続 (TCP SYN セグメントを送信し、TCP SYN ACK セグメントで応答) が正常に確立されるかどうかだけで、仮想マシンの正常性が判断されます。 対象の仮想マシンが常時リッスンしている TCP ポートを使用するようにカスタム プローブを構成してください。

> [!NOTE]
> 同じポートの組み合わせ (ポート 80 と 443 など) をインターネットに直接公開する必要があるマシンは同じクラウド サービスを共有できません。 アプリケーションと Windows Server AD FS のポートの要件が重複しないよう、Windows Server AD FS サーバー専用のクラウド サービスを作成することをお勧めします。
> 
> 

## <a name="deployment-scenarios"></a>デプロイメント シナリオ
次のセクションでは、一般的なデプロイ シナリオに沿って重要な考慮事項を概説します。 シナリオごとの決定事項と考慮すべき要素について詳しい情報へのリンクを紹介しています。

1. [AD DS: 企業ネットワークへの接続が不要な AD DS 対応のアプリケーションをデプロイする](#BKMK_CloudOnly)
   
    たとえば、インターネットに接続された SharePoint サービスを Azure 仮想マシンにデプロイします。 アプリケーションは、企業ネットワークのリソースには一切依存しません。 また、Windows Server AD DS は必要ですが、それがオンプレミスにある必要はありません。
2. [AD FS: 要求対応のオンプレミス フロントエンド アプリケーションをインターネットに拡張する](#BKMK_CloudOnlyFed)
   
    たとえば社内ユーザーによって使用されている、オンプレミスにデプロイされた要求対応のアプリケーションにインターネットからアクセスできるようにします。 既存の社内ユーザーだけでなく、自社の ID を使用するビジネス パートナーも、インターネット経由でアプリケーションに直接アクセスする必要があります。
3. [AD DS: 企業ネットワークへの接続が必要な Windows Server AD DS 対応のアプリケーションをデプロイする](#BKMK_HybridExt)
   
    たとえば、構成データとユーザー プロファイル データのリポジトリとして Windows Server AD DS を使用する、Windows 統合認証をサポートする LDAP 対応のアプリケーションを Azure 仮想マシンにデプロイします。 アプリケーションは会社の既存の Windows Server AD DS を利用し、かつシングル サインオンに対応していることが理想です。 アプリケーションは要求対応ではありません。

### <a name="BKMK_CloudOnly"></a>1.AD DS: 企業ネットワークへの接続が不要な AD DS 対応のアプリケーションをデプロイする
![Cloud-only AD DS deployment](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**図 1**

#### <a name="description"></a>説明
SharePoint は Azure 仮想マシンにデプロイされ、アプリケーションは会社のネットワーク リソースに依存していません。 また、Windows Server AD DS は必要ですが、それがオンプレミスにある必要は "*ありません*"。 Kerberos またはフェデレーションによる信頼関係は不要です。Windows Server AD DS ドメインはクラウド内の Azure 仮想マシンでもホストされており、ユーザーはそのドメインに対し、アプリケーションを介して自己プロビジョニングすることになります。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>シナリオの考慮事項と適用する技術領域
* [ネットワーク トポロジ](#BKMK_NetworkTopology): クロスプレミス接続 (サイト間接続) を使用しない Azure 仮想ネットワークを作成します。
* [DC のデプロイ構成](#BKMK_DeploymentConfig): 新しいドメイン コントローラーを単一ドメインの新しい Windows Server Active Directory フォレストにデプロイします。 Windows DNS サーバーと共にデプロイする必要があります。
* [Windows Server Active Directory のサイト トポロジ](#BKMK_ADSiteTopology): 既定の Windows Server Active Directory サイトを使用します (すべてのコンピューターは Default-First-Site-Name サイトに配置されます)。
* [IP アドレス指定と DNS](#BKMK_IPAddressDNS):
  
  * Azure PowerShell コマンドレットである Set-AzureStaticVNetIP を使用して DC に静的 IP アドレスを設定します。
  * Azure 上のドメイン コントローラーに Windows Server DNS をインストールして構成します。
  * DC ロールと DNS サーバー ロールのホストとなる VM の名前と IP アドレスを使用して、仮想ネットワークのプロパティを構成します。
* [グローバル カタログ](#BKMK_GC): フォレスト内の最初の DC はグローバル カタログ サーバーとして構成する必要があります。 2 台目以降の DC も GC として構成することをお勧めします。単一ドメイン フォレストでは、グローバル カタログが DC に余分な負荷をかけることはありません。
* [Windows Server AD DS データベースと SYSVOL の配置](#BKMK_PlaceDB): Windows Server Active Directory データベース、ログ、SYSVOL を格納するために、Azure VM として実行されている DC にデータ ディスクを追加します。
* [バックアップと復元](#BKMK_BUR): システムの状態のバックアップをどこに保存するかを決めます。 必要に応じて、バックアップを保存するためのデータ ディスクを別途 DC VM に追加します。

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: 要求対応のオンプレミス フロントエンド アプリケーションをインターネットに拡張する
![Federation with cross-premises connectivity](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**図 2**

#### <a name="description"></a>Description
社内ユーザーによって使用されている、オンプレミスにデプロイされた要求対応のアプリケーションにインターネットから直接アクセスできるようにします。 アプリケーションは、データの保存先となる SQL データベースへの Web フロントエンドとして機能しています。 アプリケーションによって使用される SQL Server も会社のネットワークに配置されています。 社内ユーザーのアクセス用に、2 つの Windows Server AD FS STS と&1; つのロード バランサーがオンプレミスにデプロイされています。 今後は、既存の社内ユーザーだけでなく、自社の ID を使用するビジネス パートナーも、インターネット経由でアプリケーションに直接アクセスする必要があります。

この新しい要件に伴い、デプロイと構成のニーズを効率よく満たすために決定したのは、別途&2; つの Web フロントエンドと&2; つの Windows Server AD FS プロキシ サーバーを Azure 仮想マシンにインストールすることです。 4 つの VM はすべてインターネットに直接公開され、Azure 仮想ネットワークのサイト間 VPN 機能を使用してオンプレミス ネットワークに接続されます。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>シナリオの考慮事項と適用する技術領域
* [ネットワーク トポロジ](#BKMK_NetworkTopology): Azure 仮想ネットワークを作成し、[クロスプレミス接続を構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)します。
  
  > [!NOTE]
  > Windows Server AD FS の各証明書について、証明書テンプレート内で定義されている URL とそのリンク先の証明書に、Azure で実行されている Windows Server AD FS インスタンスからアクセスできることを確認します。 そのために PKI インフラストラクチャ コンポーネントへのクロスプレミス接続が必要になる場合があります。 たとえば、CRL のエンドポイントが LDAP ベースであり、オンプレミスでのみホストされている場合は、クロスプレミス接続が必要になります。 それが難しい場合、使用する証明書は、CRL にインターネットでアクセスできる CA によって発行されたものである必要があります。
  > 
  > 
* [クラウド サービスの構成](#BKMK_CloudSvcConfig): 負荷分散された&2; つの仮想 IP アドレスを提供するために&2; つのクラウド サービスを用意する必要があります。 1 つ目のクラウド サービスの仮想 IP アドレスは、ポート 80 と 443 を使用する 2 つの Windows Server AD FS プロキシ VM に割り振られます。 Windows Server AD FS プロキシ VM は、Windows Server AD FS STS に接続されたオンプレミス ロード バランサーの IP アドレスを参照するように構成します。 2 つ目のクラウド サービスの仮想 IP アドレスは、同様にポート 80 と 443 を使用する Web フロントエンドを実行する 2 つの VM に割り振られます。 正常に稼働している Windows Server AD FS プロキシと Web フロントエンドの VM にのみロード バランサーがトラフィックを確実に割り振るようにカスタム プローブを構成します。
* [フェデレーション サーバーの構成](#BKMK_FedSrvConfig): クラウド内に作成した Windows Server Active Directory フォレストのセキュリティ トークンを生成するように、Windows Server AD FS をフェデレーション サーバー (STS) として構成します。 フェデレーション要求プロバイダーと、受け付ける ID の各種パートナーとの信頼関係を設定します。また、証明書利用者とトークン生成先のさまざまなアプリケーションとの信頼関係を構成します。
  
    ほとんどのシナリオでは、インターネットに接続可能な領域にはセキュリティ上、Windows Server AD FS プロキシ サーバーをデプロイし、対応する Windows Server AD FS フェデレーション サーバーはインターネットに直接接続できないよう分離します。 デプロイのシナリオに関係なく、クラウド サービスは、仮想 IP アドレスを使って構成する必要があります。インターネットに公開される IP アドレスとポートは仮想 IP アドレスによって得られ、2 つの Windows Server AD FS STS インスタンス間やプロキシ インスタンス間の負荷分散も仮想 IP アドレスによって実現されます。
* [Windows Server AD FS の高可用性構成](#BKMK_ADFSHighAvail): Windows Server AD FS ファームのデプロイでは、フェールオーバーと負荷分散用に、少なくとも&2; 台のサーバーをデプロイすることをお勧めします。 Windows Server AD FS の構成データには Windows Internal Database (WID) の使用を検討してください。また、Azure の内部負荷分散機能を使用してファーム内のサーバー間で着信要求を分散させることをお勧めします。

詳細については、「 [AD DS Deployment Guide (AD DS デプロイ ガイド)](https://technet.microsoft.com/library/cc753963)」を参照してください。

### <a name="BKMK_HybridExt"></a>3.AD DS: 企業ネットワークへの接続が必要な Windows Server AD DS 対応のアプリケーションをデプロイする
![Cross-premises AD DS deployment](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**図 3**

#### <a name="description"></a>説明
LDAP 対応アプリケーションを Azure 仮想マシンにデプロイします。 このアプリケーションは Windows 統合認証をサポートし、構成データとユーザー プロファイル データのリポジトリとして Windows Server AD DS を使用します。 このシナリオの目的は、会社の既存の Windows Server AD DS を利用し、かつシングル サインオンにアプリケーションを対応させることです。 アプリケーションは要求対応ではありません。 また、ユーザーはインターネットから直接アプリケーションにアクセスする必要があります。 パフォーマンスとコストを考慮し、会社のドメインに属している&2; つのドメイン コントローラーを別途、アプリケーションと共に Azure にデプロイすることになりました。

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>シナリオの考慮事項と適用する技術領域
* [ネットワーク トポロジ](#BKMK_NetworkTopology): [クロスプレミス接続](../vpn-gateway/vpn-gateway-site-to-site-create.md)を使用する Azure 仮想ネットワークを作成します。
* [インストール方法](#BKMK_InstallMethod): 会社の Windows Server Active Directory ドメインからレプリカ DC をデプロイします。 レプリカ DC に使用する Windows Server AD DS を VM にインストールします。また必要に応じて、"メディアからのインストール" (IFM) 機能を使用すると、インストール時に新しい DC にレプリケートしなければならないデータの量を減らすことができます。 チュートリアルについては、[Azure でのレプリカ Active Directory ドメイン コントローラーのインストール](active-directory-install-replica-active-directory-domain-controller.md)に関するページをご覧ください。 IFM を使用する場合でも、仮想 DC をオンプレミスに構築して仮想ハード ディスク (VHD) 全体をクラウドに移動する方が、インストール時に Windows Server AD DS をレプリケートするよりも効率がよいこともあります。 セキュリティ上の理由から VHD は、Azure へのコピー後にオンプレミス ネットワークから削除することをお勧めします。
* [Windows Server Active Directory のサイト トポロジ](#BKMK_ADSiteTopology): Active Directory サイトとサービスで新しい Azure サイトを作成します。 Azure 仮想ネットワークを表す Windows Server Active Directory サブネット オブジェクトを作成し、そのサブネットを新しい Azure サイトに追加します。 新しいサイト リンクを作成して、そのリンクに新しい Azure サイトを追加し、さらに、Azure 仮想ネットワークの VPN エンドポイントがあるサイトを追加します。その目的は、Azure との間でやり取りされる Windows Server Active Directory トラフィックを制御し、最適化することにあります。
* [IP アドレス指定と DNS](#BKMK_IPAddressDNS):
  
  * Azure PowerShell コマンドレットである Set-AzureStaticVNetIP を使用して DC に静的 IP アドレスを設定します。
  * Azure 上のドメイン コントローラーに Windows Server DNS をインストールして構成します。
  * DC ロールと DNS サーバー ロールのホストとなる VM の名前と IP アドレスを使用して、仮想ネットワークのプロパティを構成します。
* [地理的に分散された DC](#BKMK_DistributedDCs): 必要に応じて追加の仮想ネットワークを構成します。 Active Directory サイトのトポロジ上、Azure リージョンの異なる複数の地域に DC が必要な場合は、適宜 Active Directory サイトを作成してください。
* [読み取り専用 DC](#BKMK_RODC): DC に対する書き込み操作を実行するための要件や、サイト内のアプリケーションやサービスと RODC との互換性に応じて Azure サイトに RODC をデプロイします。 アプリケーションの互換性の詳細については、「 [アプリケーションと読み取り専用ドメイン コントローラーの互換性](https://technet.microsoft.com/library/cc755190)」を参照してください。
* [グローバル カタログ](#BKMK_GC): GC はマルチドメイン フォレストでログオン要求を処理するために必要です。 Azure サイトに GC をデプロイしない場合は、認証要求で他のサイトの GC が照会されるため、送信トラフィックのコストが発生します。 そのようなトラフィックを最小限に抑えるには、Active Directory サイトとサービスで、Azure サイトに対してユニバーサル グループ メンバーシップのキャッシュを有効にしてください。
  
    GC をデプロイする場合は、Azure サイトの GC が、同じドメイン パーティションの一部をレプリケートする必要がある他の GC によってソース DC として優先されないように、サイト リンクとサイト リンク コストを構成します。
* [Windows Server AD DS データベースと SYSVOL の配置](#BKMK_PlaceDB): Windows Server Active Directory データベース、ログ、SYSVOL を格納するために、Azure VM で実行されている DC にデータ ディスクを追加します。
* [バックアップと復元](#BKMK_BUR): システムの状態のバックアップをどこに保存するかを決めます。 必要に応じて、バックアップを保存するためのデータ ディスクを別途 DC VM に追加します。

## <a name="deployment-decisions-and-factors"></a>デプロイの決定事項と考慮すべき要素
この表では、前に示したシナリオで適用した Windows Server Active Directory の技術領域、対応する決定事項、考慮すべき要素をまとめています。 デプロイ シナリオによっては適用できない技術領域もあれば、特に重要度の高い技術領域もあります。

たとえば仮想ネットワークにレプリカ DC をデプロイするとき、フォレストにドメインが&1; つしかなければ、グローバル カタログ サーバーをデプロイするという選択は、そのデプロイ シナリオにとって重要ではありません。それ以上のレプリケーション要件は発生しないためです。 一方、フォレストに複数のドメインがある場合は、仮想ネットワークにグローバル カタログをデプロイするという判断は、使用可能な帯域幅、パフォーマンス、認証、ディレクトリ検索などに影響する可能性があります。

| Windows Server Active Directory の技術領域 | 決定事項 | 考慮すべき要素 |
| --- | --- | --- |
| [ネットワーク トポロジ](#BKMK_NetworkTopology) |仮想ネットワークを作成するかどうか。 |<li>会社のリソースにアクセスするための要件</li> <li>認証</li> <li>[Account Management]</li> |
| [DC のデプロイ構成](#BKMK_DeploymentConfig) |<li>信頼関係のない独立したフォレストをデプロイするか。</li> <li>フェデレーションを使用する新しいフォレストをデプロイするか。</li> <li>Windows Server Active Directory フォレストの信頼関係 (つまり Kerberos) を使用する新しいフォレストをデプロイするか。</li> <li>レプリカ DC をデプロイすることで会社のフォレストを拡張するか。</li> <li>新しい子ドメインやドメイン ツリーをデプロイすることで会社のフォレストを拡張するか。</li> |<li>セキュリティ</li> <li>コンプライアンス</li> <li>コスト</li> <li>回復性とフォールト トレランス</li> <li>アプリケーションの互換性</li> |
| [Windows Server Active Directory のサイト トポロジ](#BKMK_ADSiteTopology) |トラフィックを最適化してコストを最小限に抑えるために、サブネットとサイト、さらに Azure Virtual Network とのサイト リンクをどのように構成するか。 |<li>サブネットとサイトの定義</li> <li>サイト リンクのプロパティと変更通知</li> <li>レプリケーション圧縮</li> |
| [IP アドレス指定と DNS](#BKMK_IPAddressDNS) |IP アドレスと名前解決をどのように構成するか。 |<li>Set-AzureStaticVNetIP コマンドレットを使用して静的 IP アドレスを割り当てます</li> <li>Windows Server DNS サーバーをインストールし、DC および DNS サーバー ロールのホストとなる VM の名前と IP アドレスを使用して、仮想ネットワークのプロパティを構成します</li> |
| [地理的に分散された DC](#BKMK_DistributedDCs) |離れた仮想ネットワーク上の DC にどのようにレプリケートするか。 |Active Directory サイト トポロジ上、Azure リージョンの異なる複数の地域に DC が必要な場合は、適宜 Active Directory サイトを作成します。 [仮想ネットワーク間の接続を構成](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) します。 |
| [読み取り専用 DC](#BKMK_RODC) |読み取り専用 DC を使用するか書き込み可能 DC を使用するか。 |<li>HBI/PII 属性のフィルター処理</li> <li>シークレットのフィルター処理</li> <li>送信トラフィックの制限</li> |
| [グローバル カタログ](#BKMK_GC) |グローバル カタログをインストールするか。 |<li>単一ドメイン フォレストの場合、すべての DC を GC として構成します</li> <li>マルチドメイン フォレストの場合、GC は認証に必要となります</li> |
| [インストール方法](#BKMK_InstallMethod) |Azure に DC をどのようにインストールするか。 |次のどちらかの操作を行います。 <li>Windows PowerShell または Dcpromo を使用して AD DS をインストールします</li> <li>オンプレミスの仮想 DC の VHD を移動します</li> |
| [Windows Server AD DS データベースと SYSVOL の配置](#BKMK_PlaceDB) |Windows Server AD DS のデータベース、ログ、SYSVOL をどこに保存するか。 |Dcpromo.exe の既定値を変更します。 これらの重要な Active Directory ファイルは *必ず* 、書き込みキャッシュを実装するオペレーティング システム ディスクではなく、Azure データ ディスクに配置してください。 |
| [バックアップと復元](#BKMK_BUR) |データをどのように保護し、復元するか。 |システムの状態のバックアップを作成します。 |
| [フェデレーション サーバーの構成](#BKMK_FedSrvConfig) |<li>フェデレーションを使用する新しいフォレストをクラウドにデプロイするか。</li> <li>AD FS をオンプレミスにデプロイし、プロキシをクラウドで公開するか。</li> |<li>セキュリティ</li> <li>コンプライアンス</li> <li>コスト</li> <li>ビジネス パートナーによるアプリケーションへのアクセス</li> |
| [クラウド サービスの構成](#BKMK_CloudSvcConfig) |仮想マシンを初めて作成すると、クラウド サービスは暗黙にデプロイされるが、 それ以外にクラウド サービスをデプロイする必要があるか。 |<li>VM をインターネットに直接公開する必要はあるか。</li> <li> サービスに負荷分散は必要か。</li> |
| [パブリック IP とプライベート IP のアドレス指定に関するフェデレーション サーバーの要件 (動的 IP と仮想 IP)](#BKMK_FedReqVIPDIP) |<li>Windows Server AD FS インスタンスにインターネットから直接アクセスする必要があるか。</li> <li>クラウドにデプロイするアプリケーションにインターネット接続専用の IP アドレスとポートが必要か。</li> |デプロイする環境に必要な仮想 IP アドレスごとに&1; つのクラウド サービスを作成します。 |
| [Windows Server AD FS の高可用性構成](#BKMK_ADFSHighAvail) |<li>Windows Server AD FS サーバー ファーム内のノードの数。</li> <li>Windows Server AD FS プロキシ ファームにデプロイするノードの数。</li> |回復性とフォールト トレランス |

### <a name="BKMK_NetworkTopology"></a>ネットワーク トポロジ
Windows Server AD DS の IP アドレスの整合性と DNS の要件を満たすには、[Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)をまず作成し、仮想マシンをその仮想ネットワークに接続する必要があります。 仮想ネットワークの作成時、必要に応じてオンプレミスの企業ネットワークにも接続可能にするかどうかを決める必要があります。接続可能にすると、Azure 仮想マシンがオンプレミスのコンピューターに透過的に接続するようになります。この接続性は、従来の VPN テクノロジを使用して実現され、企業ネットワークの境界に VPN エンドポイントを公開することが必要です。 つまり、VPN 接続は Azure 側から企業ネットワーク側に開始されます。その逆に開始されることはありません。

仮想ネットワークをオンプレミス ネットワークに拡張すると、各 VM に適用される標準の料金に加算して追加の料金が適用されることに注意してください。 具体的には、Azure Virtual Network ゲートウェイの CPU 時間に対する課金と、オンプレミス コンピューターと VPN 経由で通信する各 VM によって生成される送信トラフィックに対する課金です。 ネットワーク トラフィックの料金の詳細については、「 [Azure の価格](http://azure.microsoft.com/pricing/)」を参照してください。

### <a name="BKMK_DeploymentConfig"></a>DC のデプロイ構成
DC の構成方法は、Azure で実行するサービスの要件によって異なります。 たとえば、自社のフォレストから分離した新しいフォレストをデプロイして、概念実証や新しいアプリケーション、その他の短期プロジェクトのテストに使用することが考えられます。このようなテストで必要なのはディレクトリ サービスであって、企業の内部リソースへのアクセスは必要ありません。

分離したフォレストの DC がオンプレミスの DC との間でレプリケートされないため、システム自体によって生成される送信ネットワーク トラフィックが減って、コストの削減に直結することが利点として挙げられます。 ネットワーク トラフィックの料金の詳細については、「 [Azure の価格](http://azure.microsoft.com/pricing/)」を参照してください。

もう&1; つ例を挙げます。社内の Windows Server Active Directory へのアクセスを必要とするサービスにプライバシー要件が課せられているとします。 サービスのデータをクラウドでホストすることが認められていれば、社内フォレストに使用する新しい子ドメインを Azure にデプロイしてもかまいません。 この場合、新しい子ドメインの DC をデプロイできます (プライバシーの問題に対処するため、グローバル カタログは使用しません)。 このシナリオでは、レプリカ DC のデプロイも必要になるため、オンプレミス DC との接続用に仮想ネットワークが必要です。

新しいフォレストを作成する場合は、[Active Directory による信頼関係](https://technet.microsoft.com/library/cc771397)を使用するか[フェデレーションによる信頼関係](https://technet.microsoft.com/library/dd807036)を使用するかを選択します。 互換性、セキュリティ、コンプライアンス、コスト、回復性に関して規定された各要件のバランスを考慮してください。 たとえば [選択的認証](https://technet.microsoft.com/library/cc755844) を利用するには、Azure に新しいフォレストをデプロイし、オンプレミスのフォレストとクラウドのフォレストとの間に Windows Server Active Directory による信頼関係を作成することが一つの選択肢となります。 一方、アプリケーションが要求対応である場合は、Active Directory フォレストによる信頼関係ではなく、フェデレーションによる信頼関係をデプロイすることが考えられますまた、コストも考慮しなければなりません。 オンプレミスの Windows Server Active Directory をクラウドに拡張することで、レプリケートするデータが増えたり、認証と照会の処理により生成される送信トラフィックが増えたりするので、そのために必要なコストが問題になります。

どのようなデプロイ構成を採用するかの判断には、可用性とフォールト トレランスの要件も関係してきます。 たとえばリンクが中断された場合、必要なインフラストラクチャが Azure にデプロイされていない限り、Kerberos による信頼関係またはフェデレーションによる信頼関係を利用するアプリケーションは、高い確率で機能不全となります。 レプリカ DC (書き込み可能 DC または読み取り専用 DC) など代替のデプロイ構成を用意すれば、リンクの停止に対する耐性を高めることができます。

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory のサイト トポロジ
トラフィックを最適化してコストを最小限に抑えるには、サイトとサイト リンクを正しく定義する必要があります。 サイト、サイト リンク、サブネットは DC 間のレプリケーション トポロジと認証トラフィックのフローに影響を与えます。 トラフィックへの課金について次の点を考慮したうえで、デプロイ シナリオの要件に応じて DC をデプロイ、構成します。

* ゲートウェイ自体に対して時間単位でわずかな料金が発生する。
  
  * 必要に応じてゲートウェイを起動/停止できます。
  * 停止した場合、Azure VM は企業ネットワークから分離されます。
* 受信トラフィックは無料である。
* 送信トラフィックに対して「 [Azure の価格](http://azure.microsoft.com/pricing/)」に従って課金される。 オンプレミスのサイトとクラウドのサイトとの間のサイト リンクのプロパティは次のように最適化できます。
  
  * 複数の仮想ネットワークを使用している場合は、サイト リンクとそれらのコストを適切に構成することで、Windows Server AD DS によって Azure サイトが、同レベルのサービスを無料で提供できるサイトより優先されないようにします。 [サイト リンクをすべてブリッジ] を無効にすることも検討してください (既定では有効)。 この場合レプリケーションは、直接接続されているサイトどうしでのみ実行されます。 推移的に接続されたサイトの DC どうしで直接レプリケートすることはできなくなり、共通のサイトを介してレプリケートすることが必要になります。 その仲介サイトが何らかの理由で使用できなくなると、推移的に接続されたサイト上にある DC 間のレプリケーションは、サイト間の接続が使用可能であっても実行されません。 推移的なレプリケーションの動作を必要とする部分については、該当するサイト リンクやサイト (オンプレミス サイトや企業ネットワーク サイトなど) を含むサイト リンク ブリッジを作成します。
  * [サイト リンクのコスト](https://technet.microsoft.com/library/cc794882) を構成します。 たとえば **[次に最も近いサイトを試す]** 設定が有効になっている場合、Azure のサイトと企業ネットワークとを接続するサイト リンク オブジェクトのコストを増やすことによって、"次に最も近いサイト" に仮想ネットワークのサイトが決して該当しないように設定します。
  * サイト リンク オブジェクトの整合性の要件と変更の頻度に応じて、サイト リンクの[間隔](https://technet.microsoft.com/library/cc794878)と[スケジュール](https://technet.microsoft.com/library/cc816906)を構成します。 許容される遅延時間に合わせてレプリケーションのスケジュールを調整します。 DC は値の最後の状態のみをレプリケートするため、レプリケーション間隔を短くすると、オブジェクトの変更頻度が最小限に抑えられていれば、コストを節約できます。
* コストを抑えることが最優先課題である場合は、レプリケーションがスケジュールされていること、変更通知が無効になっていることを確認します。 これがサイト間のレプリケート時の既定の構成です。 仮想ネットワークに RODC をデプロイする場合、送信方向に変更がレプリケートされることはないので、この構成は重要ではありません。 しかし、書き込み可能 DC をデプロイする場合は、必要以上に高い頻度で変更をレプリケートするようにサイト リンクが構成されていないか必ず確認してください。 グローバル カタログ サーバー (GC) をデプロイする場合は、GC が置かれている他のすべてのサイトについて、ドメイン パーティションのレプリケート元となるソース DC のあるサイトが、Azure サイト内の GC よりも低コストのサイト リンクに接続されていることを確認してください。
* さらに、レプリケーションの圧縮アルゴリズムを変更することで、サイト間のレプリケーションによって生成されるネットワーク トラフィックを減らすこともできます。 圧縮アルゴリズムは、REG_DWORD レジストリ エントリである HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator compression algorithm によって制御されます。 既定値は 3 で、Xpress 圧縮アルゴリズムを指定しています。 値を 2 に変更して、アルゴリズムを MSZIP に変更できます。 ほとんどの場合、これにより圧縮率は高くなりますが、その分 CPU 使用率も上昇します。 詳細については、「 [How Active Directory replication topology works (Active Directory のレプリケーション トポロジのしくみ)](https://technet.microsoft.com/library/cc755994)」を参照してください。

### <a name="BKMK_IPAddressDNS"></a>IP アドレス指定と DNS
Azure 仮想マシンには、"DHCP によってリースされたアドレス" が既定で割り当てられます。 Azure 仮想ネットワークの動的アドレスは、仮想マシンの有効期間にわたって仮想マシンに保持されるため、Windows Server AD DS の要件は満たされます。

結果的に、Azure で動的アドレスを使用すると、実際には静的 IP アドレスを使用していることになります。アドレスはリース期間にわたって割り振り可能であり、リース期間はクラウド サービスの有効期間と等しくなるためです。

ただし、VM がシャットダウンされると、動的アドレスの割り当ては解除されます。 IP アドレスの割り当てが解除されないようにするには、 [Set-AzureStaticVNetIP を使用して、静的 IP アドレスを割り当て](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx)てください。

名前解決用には、独自の DNS サーバー インフラストラクチャをデプロイします (既存のものを活用してもかまいません)。Azure が提供する DNS は Windows Server AD DS の高度な名前解決のニーズを満たしません。 たとえば、Azure の DNS は動的 SRV レコードをサポートしていません。 名前解決は DC にも、ドメインに参加するクライアントにも重要な構成項目です。 DC はリソース レコードを登録し、他の DC のリソース レコードを解決できる必要があります。
フォールト トレランスとパフォーマンス上の理由から、Azure で動作する DC に Windows Server DNS サービスをインストールするのが最善の方法となります。 さらに、DNS サーバーの名前と IP アドレスを使用して、Azure 仮想ネットワークのプロパティを構成します。 仮想ネットワーク上の他の VM が起動したときに、動的 IP アドレスの割り当ての一環として、DNS クライアント リゾルバー設定が DNS サーバーと共に構成されます。

> [!NOTE]
> Azure でホストされている Windows Server AD DS Active Directory ドメインにインターネット経由で直接、オンプレミス コンピューターを参加させることはできません。 Active Directory とドメイン参加操作に関するポートの要件により、必要なポート (実際は DC 全体) をインターネットに直接公開することは実際にはできません。
> 
> 

VM は VM 自体の DNS 名を起動時または名前変更時に自動的に登録します。

この例の詳細と、最初の VM をプロビジョニングしてその VM に AD DS をインストールする別の例については、「 [Azure Virtual Network での新しい Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)」を参照してください。 Windows PowerShell の使用の詳細については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」および[Azure の管理コマンドレット](https://msdn.microsoft.com/library/azure/jj152841)に関するページをご覧ください。

### <a name="BKMK_DistributedDCs"></a>地理的に分散された DC
複数の DC をそれぞれ異なる仮想ネットワークでホストする利点は次のとおりです。

* マルチサイトのフォールト トレランス
* ブランチ オフィスとの物理的近接 (低遅延)

仮想ネットワーク間での直接通信の構成の詳細については、「 [Azure クラシック ポータルでの VNet 間接続の構成](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)」を参照してください。

### <a name="BKMK_RODC"></a>読み取り専用 DC
読み取り専用 DC をデプロイするか書き込み可能 DC をデプロイするかを選択する必要があります。 ドメイン コントローラーには物理的な管理が行き届かないために、RODC をデプロイしようと考える方が多いかもしれません。しかし RODC は、物理的なセキュリティ リスクがある場所 (ブランチ オフィスなど) へのデプロイを想定して設計されています。

ブランチ オフィスにおける物理的なセキュリティ リスクは、Azure にはありません。しかしそれでも、RODC の方が、コスト効果の面で有利であると考えられます。本来の意義とは大きく異なるものの、RODC の機能はこうした環境によく適合しているためです。 たとえば、RODC では送信方向のレプリケーションがなく、またシークレット (パスワード) の取り込みも選択的に行うことができます。 シークレットが不足しているために、ユーザーまたはコンピューターが本人性を証明する際、それらを検証するための送信トラフィックがオンデマンドで発生するのが欠点となります。 しかしシークレットは、あらかじめ取り込んでキャッシュしておくこともできます。

RODC は、HBI (高ビジネス インパクト) や PII (個人を特定できる情報) の保護にも寄与し、RODC で除外される属性セット (FAS) に、機密データが入った属性を追加することができます。 RODC へのレプリケート対象外となる属性の集合として、FAS はカスタマイズすることができます。 Azure に PII や HBI を保存することが好ましくない、または禁止されている場合、FAS を保護対策として使用できます。 詳細については、「[RODC で除外される属性セット[(https://technet.microsoft.com/library/cc753459)]」を参照してください。

使用予定の RODC がアプリケーションの動作に支障をきたさないことを確認してください。 Windows Server Active Directory 対応のアプリケーションはその多くが RODC と正しく連動しますが、書き込み可能 DC にアクセスできない場合、効率的または正常に実行できないアプリケーションもあります。 詳細については、「 [アプリケーションと読み取り専用ドメイン コントローラーの互換性](https://technet.microsoft.com/library/cc755190)」を参照してください。

### <a name="BKMK_GC"></a>グローバル カタログ
グローバル カタログ (GC) をインストールするかどうかを選択する必要があります。 単一ドメイン フォレストでは、すべての DC をグローバル カタログ サーバーとして構成することをお勧めします。 レプリケーション トラフィックの増大を伴わないので、それによってコストが増えることはありません。

マルチドメイン フォレストでは、認証プロセス中にユニバーサル グループのメンバーシップを取得するために GC が必要となります。 GC をデプロイしない場合、Azure 上の DC に対して認証を行う仮想ネットワークのワークロードにより、送信方向の認証トラフィックが間接的に発生します。認証が試みられるたびにオンプレミスの GC が照会されるためです。

GC には (部分的とはいえ) すべてのドメインがホストされるので、GC に関連するコストは予測が困難です。 インターネットに公開されたサービスをホストし、Windows Server AD DS に対してユーザーを認証するワークロードにいたっては、コストをまったく予測できないこともあります。 認証時にクラウド サイトの範囲外への GC の照会を減らすには、 [ユニバーサル グループ メンバーシップ キャッシュを有効](https://technet.microsoft.com/library/cc816928)にするのが効果的です。

### <a name="BKMK_InstallMethod"></a>インストール方法
仮想ネットワークに DC をインストールする方法を選択する必要があります。

* 新しい DC を昇格させる。 詳細については、「 [Azure Virtual Network での新しい Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)」を参照してください。
* オンプレミスに置かれている仮想 DC の VHD をクラウドに移動する。 この場合、オンプレミスの仮想 DC は、"コピー" や "複製" ではなく "移動" する必要があります。

DC には、(Azure の Web ロールや worker ロールの VM ではなく) 必ず Azure 仮想マシンを使用します。 Azure 仮想マシンには持続性があります。DC にとって状態の持続性は必須です。 Azure 仮想マシンは DC などのワークロードを意図して設計されています。

SYSPREP を使用して DC をデプロイしたり複製したりしないでください。 DC の複製機能は Windows Server 2012 以降でのみ使用できます。 DC の複製機能を使用するには、土台となるハイパーバイザーが VMGenerationID に対応している必要があります。 Windows Server 2012 の Hyper-V と Azure 仮想ネットワークは両方とも、サード パーティ製の仮想化ソフトウェア ベンダーと同様に、VMGenerationID をサポートしています。

### <a name="BKMK_PlaceDB"></a>Windows Server AD DS データベースと SYSVOL の配置
Windows Server AD DS のデータベース、ログ、SYSVOL を検索する場所を選択します。 これらのデータは Azure データ ディスクにデプロイする必要があります。

> [!NOTE]
> Azure データ ディスクは 1 TB に制限されます。
> 
> 

データ ディスク ドライブは既定で書き込みデータをキャッシュしません。 VM に接続されたデータ ディスク ドライブにはライト スルー キャッシュが使用されています。 ライト スルー キャッシュでは、VM のオペレーティング システムの観点からトランザクションが完了する前に、持続性の Azure Storage に書き込みデータがコミットされます。 書き込み速度はやや遅くなりますが、持続性は得られます。

Windows Server AD DS にとって、この動作は重要です。後書きディスク キャッシュでは DC による推測データが無効になるためです。 Windows Server AD DS は書き込みキャッシュを無効にしようとしますが、その無効化を許可するかどうかはディスク IO システムによります。 書き込みキャッシュを無効にできない場合は、特定の状況下で USN ロールバックが行われて、結果的に残留オブジェクトやその他の問題につながります。

仮想 DC に関するベスト プラクティスとして、以下のことを行ってください。

* Azure データ ディスクの [ホスト キャッシュ設定] を [なし] に設定します。 そうすれば、AD DS の動作で書き込みキャッシュの問題は発生しません。
* データベース、ログ、SYSVOL をすべて同じデータ ディスクに保存するか、別々のデータ ディスクに保存します。 データ ディスクは通常、オペレーティング システム自体に使用されるディスクとは分かれています。 重要なのは、Windows Server AD DS のデータベースと SYSVOL を Azure オペレーティング システム ディスクに保存しないということです。 既定では、AD DS のインストール プロセスによりこれらのコンポーネントは %systemroot% フォルダーにインストールされますが、Azure にはお勧めしません。

### <a name="BKMK_BUR"></a>バックアップと復元
DC のバックアップと復元、特に VM で実行されている DC のバックアップと復元に関して、何がサポートされ、何がサポートされていないかについて注意が必要です。 「 [仮想化ドメイン コントローラーのバックアップと復元に関する考慮事項](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers)」を参照してください。

Windows Server AD DS に関するバックアップの要件を厳密に満たしているバックアップ ソフトウェア (Windows Server バックアップなど) のみを使用して、システムの状態のバックアップを作成します。

DC の VHD ファイルをコピーしたり複製したりするのではなく、通常のバックアップを行ってください。 万一復元が必要になった場合、サポートされているハイパーバイザーと Windows Server 2012 を介さずに、VHD の複製またはコピーによって復元すると USN バブルが発生します。

### <a name="BKMK_FedSrvConfig"></a>フェデレーション サーバーの構成
Windows Server AD FS フェデレーション サーバー (STS) の構成は、Azure にデプロイするアプリケーションがオンプレミス ネットワーク上のリソースにアクセスする必要があるかどうかによってやや異なります。

アプリケーションが次の条件を満たしている場合に、オンプレミス ネットワークから切り離してアプリケーションをデプロイすることができます。

* SAML セキュリティ トークンを受け付ける。
* インターネットに公開できる。
* オンプレミスのリソースを利用しない。

この場合、Windows Server AD FS STS を次のように構成します。

1. 分離した単一ドメイン フォレストを Azure 上に構成します。
2. Windows Server AD FS フェデレーション サーバー ファームを構成することで、フォレストへのフェデレーション アクセスを可能にします。
3. オンプレミス フォレストで Windows Server AD FS (フェデレーション サーバー ファームとフェデレーション サーバー プロキシ ファーム) を構成します。
4. オンプレミスの Windows Server AD FS インスタンスと Azure の Windows Server AD FS インスタンスとの間でフェデレーションによる信頼関係を確立します。

一方、オンプレミス リソースへのアクセスをアプリケーションが必要とする場合は、Azure 上の Windows Server AD FS とアプリケーションを次のように構成できます。

1. オンプレミス ネットワークと Azure との間の接続を構成します。
2. オンプレミス フォレストで Windows Server AD FS フェデレーション サーバー ファームを構成します。
3. Azure で Windows Server AD FS フェデレーション サーバー プロキシ ファームを構成します。

この構成はオンプレミス リソースの漏えいリスクを軽減できる利点があります。境界ネットワーク内で Windows Server AD FS とアプリケーションを構成することに似ています。

どちらのシナリオでも、企業間のグループ作業が必要な場合は、ID プロバイダーを増やしてそれらのプロバイダーとの信頼関係を確立できることに注意してください。

### <a name="BKMK_CloudSvcConfig"></a>クラウド サービスの構成
インターネットに VM を直接公開したり、インターネットに接続された負荷分散アプリケーションを公開したりする場合は、クラウド サービスが必要です。 クラウド サービスにはそれぞれ、設定によって変更できる仮想 IP アドレスが&1; つ用意されており、それによってインターネット接続が実現されます。

### <a name="BKMK_FedReqVIPDIP"></a>パブリック IP とプライベート IP のアドレス指定に関するフェデレーション サーバーの要件 (動的 IP と仮想 IP)
個々の Azure 仮想マシンには動的 IP アドレスが割り当てられます。 動的 IP アドレスは、Azure 内でのみアクセス可能なプライベート アドレスです。 一方、Windows Server AD FS のデプロイには、ほとんどの場合、仮想 IP アドレスの構成が伴います。 Windows Server AD FS のエンドポイントをインターネットに公開するためには仮想 IP アドレスが必要であり、フェデレーション パートナーやクライアントは、仮想 IP アドレスを使って認証や継続的な管理を行います。 仮想 IP アドレスは、1 つまたは複数の Azure 仮想マシンのホストとなるクラウド サービスのプロパティです。 Azure にデプロイされた要求対応のアプリケーションと Windows Server AD FS の両方がインターネットに接続し、同じポートを共有する場合は、それぞれに専用の仮想 IP アドレスが必要になります。つまり、アプリケーション用と Windows Server AD FS 用にそれぞれ&1; つのクラウド サービスを作成する必要があります。

仮想 IP アドレスおよび動的 IP アドレスという用語の定義については、「 [用語と定義](#BKMK_Glossary)」を参照してください。

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS の高可用性構成
Windows Server AD FS フェデレーション サービスをスタンドアロンでデプロイすることはできますが、運用環境の AD FS STS とプロキシ用に、2 つ以上のノードを備えたファームをデプロイすることをお勧めします。

特定のニーズに最適なデプロイ構成オプションを選択するには、[AD FS 2.0 の設計ガイド](https://technet.microsoft.com/library/dd807036)で [AD FS 2.0 のデプロイ トポロジに関する考慮事項](https://technet.microsoft.com/library/gg982489)を参照してください。

> [!NOTE]
> Azure で Windows Server AD FS のエンドポイントの負荷分散を行うには、同じクラウド サービス内の Windows Server AD FS ファームのすべてのメンバーが HTTP ポート (既定では 80) と HTTPS ポート (既定では 443) に Azure の負荷分散機能を使用するように構成します。 詳細については、Azure サイトで「 [LoadBalancerProbe スキーマ](https://msdn.microsoft.com/library/azure/jj151530)」を参照してください。
> Windows Server のネットワーク負荷分散 (NLB) は Azure でサポートされていません。
> 
> 


