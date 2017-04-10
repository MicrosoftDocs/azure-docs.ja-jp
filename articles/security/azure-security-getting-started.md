---
title: "Microsoft Azure セキュリティの概要 | Microsoft Docs"
description: "この記事では、Microsoft Azure のセキュリティ機能の概要と、クラウド プロバイダーに資産を移行しようとする組織の一般的な考慮事項について説明します。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: eb53ed852b6175fbc7faea44a243e8c7d5ce1753
ms.lasthandoff: 03/25/2017


---
# <a name="getting-started-with-microsoft-azure-security"></a>Microsoft Azure セキュリティの概要
IT 資産を構築し、クラウド プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ ニーズを満たす信頼性の高い基盤となっています。 また、Azure には構成可能な幅広いセキュリティ オプションが用意されており、デプロイの独自の要件を満たすようにセキュリティをカスタマイズできます。

この Azure セキュリティの概要記事では、以下の点について説明します。

* Azure 内でデータとサービスを保護するために使用できる、Azure のサービスと機能
* データやアプリケーションを保護するための Azure インフラストラクチャのセキュリティのしくみ

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理
IT インフラストラクチャ、データ、およびアプリケーションへのアクセスの制御は、非常に重要です。 Microsoft Azure では、そのための機能が、Azure Active Directory (Azure AD)、Azure Storage などのサービスと、さまざまな標準および API のサポートによって提供されています。

[Azure AD](../active-directory/active-directory-whatis.md) は、組織のユーザー、グループ、およびオブジェクトの認証、承認、およびアクセス制御を行う ID リポジトリとエンジンです。 Azure AD を使用すると、開発者はアプリケーションで ID 管理を効果的に統合できます。 [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0)、[WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx)、[OpenID Connect](http://openid.net/connect/) など、業界標準のプロトコルがサポートされているため、.NET、Java、Node.js、PHP などのプラットフォームでのサインインを実現できます。

REST ベースの Graph API により、開発者は任意のプラットフォームからディレクトリに対して読み取りと書き込みを行うことができます。 [OAuth 2.0](http://oauth.net/2/) がサポートされることで、開発者は、マイクロソフトやサードパーティの Web API と連携するモバイル アプリケーションや Web アプリケーションを作成できるほか、セキュリティに優れた独自の Web API を作成することができます。 .NET、Windows ストア、iOS、Android 向けにオープンソースのクライアント ライブラリが用意されており、その他のライブラリも開発が進められています。

### <a name="how-azure-enables-identity-and-access-management"></a>Azure の ID 管理とアクセス管理のしくみ
Azure AD は、組織のスタンドアロン クラウド ディレクトリとして使用することも、既存のオンプレミス Active Directory との統合ソリューションとして使用することもできます。 一部の統合機能には、ディレクトリ同期とシングル サインオン (SSO) が含まれています。 これらの機能により、既存のオンプレミス ID の有効範囲がクラウドまで拡張され、管理者とユーザーのエクスペリエンスが向上します。

ID およびアクセス管理に関しては、他にも次のような機能があります。

* Azure AD では、どこでホストされているかにかかわらず、SaaS アプリケーションへの [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) が可能です。 一部のアプリケーションは Azure AD とフェデレーションされ、他のアプリケーションはパスワード SSO を使用します。 フェデレーション アプリケーションでは、ユーザー プロビジョニングとパスワード保管もサポートできます。
* [Azure Storage](https://azure.microsoft.com/services/storage/) のデータへのアクセスは、認証によって制御されます。 各ストレージ アカウントには、プライマリ キー ([ストレージ アカウント キー](https://msdn.microsoft.com/library/azure/ee460785.aspx) (SAK)) とセカンダリ秘密キー (Shared Access Signature (SAS)) があります。
* Azure AD は、オンプレミス ディレクトリとの [Active Directory フェデレーション サービス (AD FS)](../active-directory/fundamentals-identity.md)、同期、およびレプリケーションを使用し、フェデレーションを通じて ID をサービスとして提供します。
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) は、モバイル アプリケーション、電話、またはテキスト メッセージを使用したサインインの検証をユーザーに要求する多要素認証サービスです。 Azure AD と併用して Azure Multi-Factor Authentication サーバーでオンプレミス リソースをセキュリティで保護したり、SDK を使用するカスタム アプリケーションおよびディレクトリに使用したりすることができます。
* [Azure AD ドメイン サービス](https://azure.microsoft.com/services/active-directory-ds/)を使用すると、ドメイン コントローラーをデプロイしなくても、Azure 仮想マシンをドメインに参加させることができます。 ユーザーは会社の Active Directory 資格情報を使用してこれらの仮想マシンにサインインし、グループ ポリシーによってすべての Azure 仮想マシンにセキュリティ基準を適用することで、ドメインに参加している仮想マシンを管理できます。
* [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) は、数億個の ID を扱うコンシューマー向けアプリケーション用の高可用性グローバル ID 管理サービスを提供します。 モバイルと Web の両方のプラットフォームにわたる統合を実現できます。 コンシューマーは、既に持っているソーシャル アカウントを使用するか、新たな資格情報を作成して、すべてのアプリケーションにサインインできます。その場合のエクスペリエンスは、カスタマイズすることができます。

## <a name="data-access-control-and-encryption"></a>データ アクセス制御と暗号化
Microsoft は、Azure の運営全般にわたって、職務の分離と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)の原則を採用しています。 Azure のサポート担当者がデータにアクセスするときには明示的な許可が必要であり、アクセス許可は "ジャストインタイム" ベースで付与され、ログ記録と監査が行われて、契約完了後は取り消されます。

Azure には、さらに、転送中および保存中のデータを保護するための機能がいくつも備わっています。 具体的には、データ、ファイル、アプリケーション、サービス、通信、ドライブの暗号化などです。 Azure に配置する前に情報を暗号化したり、キーをオンプレミスのデータセンターに格納したりすることもできます。

![Azure での Microsoft マルウェア対策](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Azure の暗号化テクノロジ
[Azure AD レポート](../active-directory/active-directory-reporting-audit-events.md)を使用すると、サブスクリプション環境への管理アクセスについての詳細情報を収集できます。 また、Azure 内の機密情報が含まれている VHD に対して [BitLocker ドライブ暗号化](https://technet.microsoft.com/library/cc732774.aspx)を構成することもできます。

Azure には、その他に、データのセキュリティを保持するための次のような機能もあります。

* アプリケーション開発者は、Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) と .NET Framework を使用して、Azure にデプロイするアプリケーションに暗号化を組み込むことができます。
* Azure Blob Storage のクライアント側暗号化では、キーを完全に制御できます。 ストレージ サービスがキーを参照することはなく、データを復号化することもできません。
* [Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) を ([RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx) と併せて) 使用すると、ファイルおよびデータレベルの暗号化と、ポリシーベースのアクセス管理によるデータリークの防止が可能になります。
* Azure では SQL Server 仮想マシンでの[テーブル レベルおよび列レベルの暗号化 (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) がサポートされており、データセンターでのサードパーティのオンプレミス キー管理サーバーがサポートされています。
* ストレージ アカウント キー、共有アクセス署名、管理証明書、およびその他のキーは、Azure テナントごとに固有です。
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) ハイブリッド ストレージは、データを Azure Storage にアップロードする前に、128 ビット パブリック/プライベート キー ペアによってデータを暗号化します。
* Azure では、データ型、コンテナー、およびトランスポートに応じて、SSL/TLS、IPsec、AES などのさまざまな暗号化メカニズムをサポートし、使用します。

## <a name="virtualization"></a>仮想化
Azure プラットフォームでは、仮想化された環境を使用します。 ユーザー インスタンスは、物理ホスト サーバーへのアクセス権を持たないスタンドアロン仮想マシンとして動作し、この分離は物理[プロセッサ (リング 0/リング 3) 特権レベル](https://en.wikipedia.org/wiki/Protection_ring)によって強制されます。

リング 0 は最も高い特権で、3 は最も低い特権です。 ゲスト OS はより低い特権であるリング 1 で実行し、アプリケーションは最低の特権であるリング 3 で実行します。 この物理リソースの仮想化によって、ゲスト OS とハイパーバイザーが明確に分離され、この 2 つの間に追加のセキュリティ分離が生じます。

Azure のハイパーバイザーは、マイクロ カーネルのように動作し、ゲスト仮想マシンからのすべてのハードウェア アクセス要求を、VMBus と呼ばれる共有メモリ インターフェイスを使用して処理するために、ホストに渡します。 そうすることで、ユーザーがシステムへの生の読み取り/書き込み/実行アクセスを取得できないようにし、共有システム リソースのリスクを軽減します。

![Azure での Microsoft マルウェア対策](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Azure での仮想化の実装方法
Azure では、ハイパーバイザー ファイアウォール (パケット フィルター) を使用します。このファイアウォールはハイパーバイザー内で実装され、ファブリック コントローラー エージェントによって構成されます。 これにより、テナントを不正アクセスから保護できます。 既定では、仮想マシンが作成されるとすべてのトラフィックがブロックされ、ファブリック コントローラー エージェントがパケット フィルターを構成して、承認されたトラフィックを許可するように "*規則と例外*" を追加します。

ここでプログラムされるルールには、次の 2 つのカテゴリがあります。

* **マシン構成またはインフラストラクチャの規則**: 既定では、すべての通信がブロックされています。 仮想マシンに DHCP および DNS トラフィックの送受信を許可する例外があります。 仮想マシンは、トラフィックを "パブリック" インターネットに送信したり、トラフィックをクラスター内の他の仮想マシンや OS ライセンス認証サーバーに送信したりすることもできます。 仮想マシンの発信先の許可リストには、Azure ルーター サブネット、Azure 管理バック エンド、およびその他の Microsoft サービスは含まれていません。
* **ロール構成ファイル**: テナントのサービス モデルに基づいて受信アクセス制御リスト (ACL) を定義します。 たとえば、テナントが特定の仮想マシンのポート 80 に Web フロント エンドを持っている場合、[Azure クラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)内にエンドポイントを構成していると、Azure はすべての IP に対して TCP ポート 80 を開きます。 仮想マシンでバックエンド ロールまたは worker ロールが実行されている場合には、同一のテナントにある仮想マシンのみに対して、worker ロールが開かれます。

## <a name="isolation"></a>分離:
もう 1 つの重要なクラウド セキュリティ要件として、共有マルチテナント アーキテクチャ内のデプロイの間で意図せず許可されてしない情報の転送が行われないようにするための分離が挙げられます。

Azure は、VLAN 分離、ACL、ロード バランサー、および IP フィルターによる[ネットワーク アクセス制御](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)と分離を実装しています。 このため、仮想マシンが受信する外部トラフィックは、定義したポートとプロトコルだけに制限されます。 さらに、なりすましトラフィックを防止し、送受信トラフィックを信頼できるプラットフォーム コンポーネントだけに限定するため、ネットワーク フィルター処理が実装されていています。 トラフィック フローのポリシーは、既定ではトラフィックを拒否する境界保護デバイス上で実装されます。

![Azure での Microsoft マルウェア対策](./media/azure-security-getting-started/sec-azgsfig3.PNG)

ネットワーク アドレス変換 (NAT) は、内部ネットワーク トラフィックを外部トラフィックから分離するために使用されます。 内部トラフィックは、外部にはルーティングできません。 外部にルーティングできる[仮想 IP アドレス](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx)は、Azure 内だけでルーティングできる[内部の動的 IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) アドレスに変換されます。

Azure 仮想マシンへの外部トラフィックは、ルーター、ロード バランサー、およびレイヤー 3 スイッチで、ACL によってファイアウォールの対象となります。 特定の既知のプロトコルのみが許可されます。 ACL は、管理に使用されるその他の VLAN にゲスト仮想マシンから送信されたトラフィックを制限する役割を果たします。 また、ホスト OS で IP フィルターによってフィルター処理されたトラフィックは、データ リンクとネットワーク レイヤーの両方でも制限されます。

### <a name="how-azure-implements-isolation"></a>Azure での分離の実装方法
Azure ファブリック コントローラーは、テナントのワークロードにインフラストラクチャのリソースを割り当て、ホストから仮想マシンへの一方向の通信を管理します。 Azure ハイパーバイザーは、仮想マシン間のメモリおよびプロセスの分離を強制し、ネットワーク トラフィックをゲスト OS テナントに安全にルーティングします。 Azure では、次のようにテナント、ストレージ、および仮想ネットワークの分離も実装されています。

* 各 Azure AD テナントは、セキュリティ境界を使用して、論理的に分離されています。
* Azure ストレージ アカウントはサブスクリプションごとに一意であり、アクセスの認証は、ストレージ アカウント キーを使用して行う必要があります。
* 仮想ネットワークは、一意のプライベート IP アドレス、ファイアウォール、および IP ACL の組み合わせによって、論理的に分離されています。 ロード バランサーは、エンドポイント定義に基づいて、トラフィックを適切なテナントにルーティングします。

## <a name="virtual-networks-and-firewalls"></a>仮想ネットワークとファイアウォール
Azure の[分散仮想ネットワーク](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx)は、プライベート ネットワーク トラフィックを他の Azure 仮想ネットワーク上のトラフィックから論理的に分離するために役立ちます。

![Azure での Microsoft マルウェア対策](./media/azure-security-getting-started/sec-azgsfig4.PNG)

サブスクリプションには、分離された複数のプライベート ネットワーク (およびファイアウォール、負荷分散、ネットワーク アドレス変換) を含めることができます。

Azure では、各 Azure クラスター内に、トラフィックを論理的に分離するための、ネットワーク分離の 3 つのプライマリ レベルがあります。 [仮想ローカル エリア ネットワーク](https://azure.microsoft.com/services/virtual-network/) (VLAN) は、顧客のトラフィックを Azure ネットワークの残りの部分から分離するために使用されます。 クラスターの外部からの Azure ネットワークへのアクセスは、ロード バランサーによって制限されます。

仮想マシン間のネットワーク トラフィックは、ハイパーバイザーの仮想スイッチを通じて渡される必要があります。 ルート OS の IP フィルター コンポーネントは、ゲスト仮想マシンからルート仮想マシンを分離し、各ゲスト仮想マシンを分離します。 このコンポーネントは、テナントのノードとパブリック インターネット間の通信を制限するために、顧客のサービスの構成に基づいてトラフィックのフィルター処理を実行し、他のテナントから分離します。

IP フィルターは、ゲスト仮想マシンによる以下の動作を防ぐために役立ちます。

* なりすましトラフィックの生成
* 自分以外を宛先とするトラフィックの受信
* 保護されたインフラストラクチャ エンドポイントへのトラフィックの転送
* 不適切なブロードキャスト トラフィックの送受信

仮想マシンは、[Azure 仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)に配置できます。 これらの仮想ネットワークは、オンプレミス環境で構成するネットワークに似ています。この環境では、通常、ネットワークが仮想スイッチに関連付けられています。 同じ仮想ネットワークに接続されている仮想マシンどうしは、追加の構成なしで通信できます。 また、仮想ネットワーク内に別のサブネットを構成することもできます。

仮想ネットワークで安全な通信を行うために、以下の Azure Virtual Network テクノロジを使用できます。

* [**ネットワーク セキュリティ グループ (NSG)**](../virtual-network/virtual-networks-nsg.md)。 NSG を使用して、仮想ネットワーク内で 1 つまたは複数の仮想マシンのインスタンスに対するトラフィックを制御できます。 NSG には、トラフィックの方向、プロトコル、ソース アドレスとポート、および送信先アドレスとポートに基づいて、トラフィックを許可または拒否するアクセス制御ルールが含まれています。
* [**ユーザー定義のルーティング**](../virtual-network/virtual-networks-udr-overview.md)。 仮想ネットワーク セキュリティ アプライアンスに到達するように特定のサブネットに送信されるパケットの次のホップを指定するユーザー定義のルートを作成することによって、仮想アプライアンスを通じてパケットのルーティングを制御できます。
* [**IP 転送**](../virtual-network/virtual-networks-udr-overview.md)。 仮想ネットワーク セキュリティ アプライアンスは、自分宛てではない着信トラフィックを受信できることが必要です。 仮想マシンが自分以外の宛先へのトラフィックを受信できるようにするには、仮想マシンの IP 転送を有効にします。
* [**強制トンネリング**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md)。 強制トンネリングでは、検査と監査のために、仮想ネットワーク内の仮想マシンによって生成されたインターネットへのすべてのトラフィックを、サイト間 VPN トンネルを介してオンプレミスの場所にリダイレクトまたは "強制的に" 戻すことができます。
* [**エンドポイント ACL**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 エンドポイント ACL を定義することで、どのコンピューターがインターネットから仮想ネットワーク上の仮想マシンへの着信接続を許可されるかを制御できます。
* [**パートナー ネットワーク セキュリティ ソリューション**](https://azure.microsoft.com/marketplace/)。 Azure Marketplace から、多くのパートナー ネットワーク セキュリティ ソリューションにアクセスできます。

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Azure での仮想ネットワークとファイアウォールの実装方法
Azure は、既定ではすべてのホスト仮想マシンとゲスト仮想マシン上で、パケット フィルター ファイアウォールを実装しています。 Azure Marketplace の Windows OS イメージでも、Windows ファイアウォールが既定で有効になっています。 Azure のパブリックに公開されたネットワークの境界にあるロード バランサーは、顧客管理者によって管理されている IP ACL に基づいて通信を制御します。

Azure で通常業務の一環として、または障害発生時に、顧客のデータを移動する場合は、暗号化されたプライベート通信チャネル経由で行います。 仮想ネットワークおよびファイアウォールで Azure が採用しているその他の機能は、次のとおりです。

* **ネイティブ ホスト ファイアウォール**: Azure Service Fabric と Azure Storage は、ハイパーバイザーのないネイティブ OS で実行されます。 そのため、Windows ファイアウォールには前に説明した 2 セットの規則が構成されます。 ストレージは、パフォーマンスを最適化するために、ネイティブで実行されます。
* **ホスト ファイアウォール**: ホスト ファイアウォールは、ハイパーバイザーを実行しているホスト オペレーティング システムを保護します。 規則は、Service Fabric コントローラーとジャンプ ボックスだけに特定のポートでのホスト OS との対話を許可するようにプログラミングされています。 その他の例外として、DHCP 応答と DNS 返信の許可があります。 Azure では、ホスト OS 用のファイアウォール規則のテンプレートがあるマシン構成ファイルを使用します。 ホスト自体は、既知の認証済みのソースからの通信だけを許可するように構成されている Windows ファイアウォールによって、外部からの攻撃から保護されています。
* **ゲスト ファイアウォール**: 仮想マシンのスイッチ パケット フィルターの規則をレプリケートしますが、別のソフトウェア (ゲスト OS の Windows ファイアウォールなど) によってプログラミングされています。 ゲスト仮想マシン ファイアウォールは、ゲスト仮想マシンとの間の通信を、ホスト IP フィルターの構成では許可されている場合でも制限するように構成できます。 たとえば、ゲスト仮想マシン ファイアウォールを使用して、相互に接続するように構成されている 2 つの VNet 間の通信を制限することもできます。
* **ストレージ ファイアウォール (FW)**: ストレージ フロントエンドのファイアウォールは、ポート 80/443 とその他の必要なユーティリティ ポート上のトラフィックだけになるようにフィルタリングします。 ストレージ バックエンドのファイアウォールは、通信がストレージ フロントエンド サーバーのみから来るように制限します。
* **仮想ネットワーク ゲートウェイ**: [Azure Virtual Network ゲートウェイ](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)は、Azure Virtual Network のワークロードをオンプレミス サイトに接続するクロスプレミス ゲートウェイとして機能します。 [IPsec サイト間 VPN トンネル](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)または [ExpressRoute](../expressroute/expressroute-introduction.md) 回路を介して、オンプレミス サイトに接続する必要があります。 IPsec/IKE VPN トンネルの場合、ゲートウェイは IKE ハンドシェイクを実行し、仮想ネットワークとオンプレミス サイト間の IPsec S2S VPN トンネルを確立します。 仮想ネットワーク ゲートウェイは、[ポイント対サイト VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) も終了させます。

## <a name="secure-remote-access"></a>セキュリティで保護されたリモート アクセス
クラウドに格納されたデータは、悪用されず、転送中の機密性と整合性が維持されるように、十分に保護されている必要があります。 たとえば、組織のポリシー ベースの監査可能な ID およびアクセス管理メカニズムと結び付いたネットワーク制御などです。

組み込みの暗号化テクノロジを使用すると、デプロイ内やデプロイ間の通信、Azure リージョン間の通信、および Azure からオンプレミス データセンターへの通信を暗号化できます。 [リモート デスクトップ セッション](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)、[リモート Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx)、および Azure Portal を介した仮想マシンへの管理者アクセスは、常に暗号化されます。

オンプレミス データセンターをクラウドに安全に拡張するために、Azure には[サイト間 VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) と[ポイント対サイト VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) のほか、[ExpressRoute](../expressroute/expressroute-introduction.md) との専用リンクが用意されています (VPN 経由での Azure Virtual Networks への接続は暗号化されます)。

### <a name="how-azure-implements-secure-remote-access"></a>Azure での安全なリモート アクセスの実装方法
Azure Portal への接続は、常に認証される必要があり、SSL や TLS を必要とします。 セキュリティで保護された管理ができるように、管理証明書を構成することができます。 [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx)、[IPsec](https://en.wikipedia.org/wiki/IPsec) など、セキュリティで保護された業界標準プロトコルが完全にサポートされています。

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) は、Azure のデータセンターとオンプレミスや共用環境にあるインフラストラクチャの間でプライベート接続を作成するサービスです。 ExpressRoute 接続では、公共のインターネットを利用できません。 一般的なインターネット ベースのリンクよりも信頼性が高く、高速で、待機時間が少なく、セキュリティの高い接続が提供されます。 オンプレミスの場所と Azure の間のデータ転送に ExpressRoute 接続を使用することで、コスト面で大きなメリットが得られる場合もあります。

## <a name="logging-and-monitoring"></a>ログ記録と監視
Azure には、監査証跡を生成するセキュリティ関連イベントの認証済みログ記録機能があり、改ざんができないように設計されています。 記録されるのは、Azure インフラストラクチャ 仮想マシンと Azure AD のセキュリティ イベント ログのようなシステム情報などです。 セキュリティ イベントの監視には、さまざまなイベントの収集が含まれます。具体的には、DHCP または DNS サーバーの IP アドレスの変更、仕様によってブロックされているポート、プロトコル、または IP アドレスへのアクセス試行、セキュリティ ポリシーまたはファイアウォール設定の変更、アカウントまたはグループの作成、予期しないプロセスまたはドライバーのインストールなどです。

![Azure での Microsoft マルウェア対策](./media/azure-security-getting-started/sec-azgsfig5.PNG)

特権を持つユーザーのアクセスとアクティビティ、承認されているアクセス試行と承認されていないアクセス試行、システム例外、および情報セキュリティ イベントを記録する監査ログは、所定の期間、保持されます。 ログの収集と保有は独自の要件に合わせてユーザーが構成するため、ログの保有期間はユーザーの判断で決まります。

### <a name="how-azure-implements-logging-and-monitoring"></a>Azure でのログ記録と監視の実装方法
Azure では、ノードがネイティブか仮想かにかかわらず、管理下の各コンピューティング ノード、ストレージ ノード、またはファブリック ノードに管理エージェント (MA) と Azure セキュリティ モニター (ASM) エージェントをデプロイします。 各管理エージェントは、Azure 証明書ストアから取得された証明書でサービス チーム ストレージ アカウントを認証し、事前に構成された診断とイベント データをストレージ アカウントに転送するように構成されています。 これらのエージェントは、顧客の仮想マシンにはデプロイされません。

Azure 管理者は、ログへのアクセスを認証および制御される必要があるため、Web ポータルを通じてログにアクセスします。 管理者は、ログの解析、フィルター処理、関連付け、および分析を行うことができます。 ログ用の Azure サービス チーム ストレージ アカウントは、ログの改ざんを防止するために、直接の管理者アクセスから保護されています。

Microsoft は、Syslog プロトコルを使用してネットワーク デバイスからログを収集し、Microsoft 監査コレクション サービス (ACS) を使用してホスト サーバーからログを収集しています。 これらのログはログ データベースに格納され、不審なイベントがあると、このデータベースからアラートが生成されます。 管理者は、これらのログにアクセスして分析することができます。

[Azure 診断](https://msdn.microsoft.com/library/azure/gg433048.aspx)は、Azure で実行されているアプリケーションから診断データを収集できるようにするための Azure の機能です。 診断データは、デバッグとトラブルシューティング、パフォーマンスの測定、リソースの使用状況の監視、トラフィック分析、容量計画、監査などに使用できます。 収集された診断データは、保存用に Azure ストレージ アカウントに転送できます。 転送は、スケジュールして実行することも、オンデマンドで実行することもできます。

## <a name="threat-mitigation"></a>脅威の緩和
Azure では、インフラストラクチャとサービスを保護するために、分離、暗号化、フィルター処理の他にも、脅威を緩和するためのさまざまなメカニズムとプロセスを採用しています。 たとえば、DDoS、特権昇格、[OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) などの高度な脅威を検出および修復するための内部制御やテクノロジです。

クラウド インフラストラクチャをセキュリティで保護するために Microsoft が採用しているセキュリティ コントロールおよびリスク管理プロセスによって、セキュリティ インシデントのリスクが軽減されます。 それでも問題が発生した場合には、Microsoft Online Security Services and Compliance (OSSC) チームの Security Incident Management (SIM) チームがいつでも対応します。

### <a name="how-azure-implements-threat-mitigation"></a>Azure での脅威の緩和の実装方法
Azure には、脅威の緩和を実装し、お客様の環境での潜在的な脅威の緩和を支援するためのセキュリティ コントロールがあります。 次の一覧は、Azure が提供する脅威緩和機能をまとめたものです。

* [Azure のマルウェア対策](azure-security-antimalware.md)は、すべてのインフラストラクチャ サーバーで、既定で有効になっています。 必要に応じて、独自の仮想マシン内でも有効にできます。
* Microsoft は、脅威を検出し、攻撃を防ぐために、サーバー、ネットワーク、およびアプリケーションで、継続的な監視を行っています。 異常な動作は自動アラートによって管理者に通知されるため、内部および外部の脅威に対して修正措置を取ることができます。
* [Barracuda](https://techlib.barracuda.com/ng54/deployonazure) の Web アプリケーション ファイアウォールなど、サードパーティ製セキュリティ ソリューションをサブスクリプションにデプロイすることもできます。
* Microsoft の侵入テストには、"[レッド チーム](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)" も含まれています。このテストでは、Microsoft のセキュリティ専門家が Azure の実際の運用システム (お客様のシステム以外) を攻撃して、現実の高度な継続的脅威に対する防御を検証します。
* Azure プラットフォーム全体にわたるセキュリティ更新プログラムの配布とインストールは、統合されたデプロイ システムが管理しています。

## <a name="next-steps"></a>次のステップ
[Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)

[Azure セキュリティ チームのブログ](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory のブログ](http://blogs.technet.com/b/ad/)

