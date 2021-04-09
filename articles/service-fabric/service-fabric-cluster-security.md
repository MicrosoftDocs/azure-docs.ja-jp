---
title: Azure Service Fabric クラスターをセキュリティで保護する
description: Azure Service Fabric クラスターのセキュリティ シナリオと、その実装に使用できる多様なテクノロジについて説明します。
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573070"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric クラスターのセキュリティに関するシナリオ

Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 承認されていないユーザーが接続できないように、クラスターをセキュリティで保護する必要があります。 クラスターで実稼働ワークロードを実行している場合、セキュリティで保護されたクラスターは特に重要です。 セキュリティで保護されていないクラスターを作成することはできますが、パブリック インターネットに管理エンドポイントを公開している場合に、匿名ユーザーがそのクラスターに接続できるようになります。 セキュリティで保護されていないクラスターは、運用ワークロードでサポートされません。 

この記事では、Azure クラスターとスタンドアロン クラスターのセキュリティ シナリオの概要と、その実装に使用できる多様なテクノロジについて説明します。

* ノード間のセキュリティ
* クライアントとノードの間のセキュリティ
* Service Fabric のロールベースのアクセス制御

## <a name="node-to-node-security"></a>ノード間のセキュリティ

ノード間のセキュリティでは、クラスター内の VM やコンピューターの間の通信をセキュリティで保護できます。 このセキュリティ シナリオでは、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

![ノード間通信の図][Node-to-Node]

Azure で実行するクラスターおよび Windows で実行するスタンドアロン クラスターのいずれにも、[証明書セキュリティ](/previous-versions/msp-n-p/ff649801(v=pandp.10))または [Windows セキュリティ](/previous-versions/msp-n-p/ff649396(v=pandp.10)) (Windows Server コンピューターの場合) を利用できます。

### <a name="node-to-node-certificate-security"></a>ノード間の証明書セキュリティ

Service Fabric では、クラスターを作成するときにノード タイプの構成で指定した X.509 サーバー証明書を使用します。 これらの証明書の概要とその入手または作成方法はこの記事の最後に記載されています。

証明書セキュリティは、Azure Portal、Azure Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用して、クラスターを作成する際に設定します。 Service Fabric SDK の既定の動作では、有効期限が最も先の証明書がデプロイおよびインストールされます。従来の動作では、手動で開始されるロールオーバーを許可するために、プライマリおよびセカンダリの証明書の定義が認められていました。新しい機能でなく、従来の機能を使用することは推奨されません。 使用されるプライマリ証明書は、有効期限が最も先であり、[クライアントとノードの間のセキュリティ](#client-to-node-security)に設定した管理用クライアント証明書および読み取り専用クライアント証明書とは異なるものである必要があります。

Azure 用にクラスターで証明書セキュリティを設定する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

スタンドアロン Windows Server クラスター用にクラスターで証明書セキュリティを設定する方法については、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### <a name="node-to-node-windows-security"></a>ノード間の Windows セキュリティ

> [!NOTE]
> Windows 認証は Kerberos に基づいています。 認証の種類として NTLM はサポートされていません。
>
> Service Fabric クラスターでは、可能な限り x.509 証明書認証を使用してください。

スタンドアロン Windows Server クラスター用に Windows セキュリティを設定する方法については、「[Windows セキュリティを使用して Windows 上のスタンドアロン クラスターを保護する](service-fabric-windows-cluster-windows-security.md)」を参照してください。

## <a name="client-to-node-security"></a>クライアントとノードの間のセキュリティ

クライアントとノードの間のセキュリティでは、クライアントの認証を行い、クラスター内のクライアントと個々のノードの間の通信をセキュリティで保護できます。 この種類のセキュリティでは、権限のあるユーザーのみが、クラスターと、クラスターにデプロイされたアプリケーションにアクセスできます。 クライアントは、Windows セキュリティ資格情報または証明書のセキュリティ資格情報を通じて一意に識別されます。

![クライアントとノードの間の通信の図][Client-to-Node]

Azure で実行するクラスターと Windows で実行するスタンドアロン クラスターでは、両方とも[証明書セキュリティ](/previous-versions/msp-n-p/ff649801(v=pandp.10))または [Windows セキュリティ](/previous-versions/msp-n-p/ff649396(v=pandp.10))を使用できますが、推奨されるのは、可能な場合は常に X.509 証明書認証を使用することです。

### <a name="client-to-node-certificate-security"></a>クライアントとノードの間の証明書セキュリティ

クライアントとノードの間の証明書セキュリティは、Azure Portal、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用して、クラスターを作成する際に設定します。 証明書を作成するには、管理用クライアント証明書またはユーザー クライアント証明書を指定する必要があります。 指定する管理用クライアント証明書とユーザー クライアント証明書は、ベスト プラクティスとして[ノード間のセキュリティ](#node-to-node-security)に指定するプライマリ証明書とセカンダリ証明書とは異なります。 クラスター証明書には、クライアント管理用証明書と同じ権限が与えられます。 ただし、セキュリティのベスト プラクティスとしては、この証明書はクラスターのみが使用し、管理ユーザーは使用しないことをお勧めします。

管理用証明書を使用してクラスターに接続するクライアントには、管理機能へのフル アクセス権があります。 読み取り専用ユーザー クライアント証明書を使用してクラスターに接続するクライアントには、管理機能に対する読み取りアクセス権しかありません。 これらの証明書は、この記事で後述する Service Fabric RBAC に使用されます。

Azure 用にクラスターで証明書セキュリティを設定する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

スタンドアロン Windows Server クラスター用にクラスターで証明書セキュリティを設定する方法については、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure でのクライアントとノードの間の Azure Active Directory セキュリティ

Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 まだテナントを作成していない場合は、まず [Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]に関するページをお読みください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

Azure で実行されているクラスターの場合、Azure Active Directory (Azure AD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することもできます。 必要な Azure AD アーティファクトを作成する方法と、クラスターを作成するときにそれらに値を設定する方法については、[クライアントを認証するための Azure AD の設定](service-fabric-cluster-creation-setup-aad.md)に関するページを参照してください。

## <a name="security-recommendations"></a>セキュリティに関する推奨事項

Azure でホストされているパブリック ネットワークにデプロイされた Service Fabric クラスターの場合、クライアントとノードの間の相互認証は次のようにすることを推奨します。

* クライアント ID には Azure Active Directory を使用する
* サーバー ID および http 通信の TLS 暗号化の証明書

Azure でホストされているパブリック ネットワークにデプロイされた Service Fabric クラスターの場合、ノード間のセキュリティではクラスター証明書を使用してノードを認証することをお勧めします。

スタンドアロン Windows Server クラスターについては、Windows Server 2012 R2 と Windows Active Directory を使用している場合、グループ管理サービス アカウントで Windows セキュリティを使用することをお勧めします。 それ以外の場合は、Windows アカウントで Windows セキュリティを使用してください。

## <a name="service-fabric-role-based-access-control"></a>Service Fabric のロールベースのアクセス制御

アクセス制御を使用して、ユーザーの各グループについて特定のクラスター操作へのアクセスを制限することができます。 その結果、クラスターのセキュリティが強化されます。 クラスターに接続するクライアント用に、2 種類のアクセス制御 (管理者ロールとユーザー ロール) がサポートされています。

管理者ロールが割り当てられたユーザーには、管理機能へのフル アクセス権 (読み取り機能、書き込み機能など) があります。 ユーザー ロールが割り当てられたユーザーには、既定で管理機能 (クエリ機能など) への読み取りアクセス権のみがあります。 また、アプリケーションとサービスを解決することもできます。

クラスターを作成するときに、管理者およびユーザー クライアント ロールを設定します。 ロールを割り当てるには、ロールの種類ごとに別の ID を指定します (たとえば、証明書または Azure AD を使用します)。 既定のアクセス制御の設定と、既定の設定を変更する方法の詳細については、「[Service Fabric のロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」をご覧ください。

## <a name="x509-certificates-and-service-fabric"></a>X.509 証明書と Service Fabric

一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証に使用されています。 また、メッセージの暗号化やデジタル署名にも使用されます。 Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。 X.509 デジタル証明書の詳細については、「[証明書の使用](/dotnet/framework/wcf/feature-details/working-with-certificates)」を参照してください。 [Key Vault](../key-vault/general/overview.md) を使用して、Azure の Service Fabric クラスターの証明書を管理します。

次のような、考慮すべき重要な点がいくつかあります。

* 運用環境のワークロードを実行しているクラスター用に証明書を作成するには、正しく構成された Windows Server 証明書サービスを使用するか、認定済みの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) の証明書を使用する必要があります。
* MakeCert.exe などのツールを使用して作成した一時証明書やテスト証明書は実稼働環境では使用しないでください。
* 自己署名証明書は、テスト クラスターでのみで使用できます。 自己署名証明書は実稼働環境では使用しないでください。
* 証明書の拇印を生成するとき、必ず SHA1 拇印を生成してください。 SHA1 は、クライアントとクラスターの証明書拇印を構成するときに使用される拇印です。

### <a name="cluster-and-server-certificate-required"></a>クラスターとサーバーの証明書 (必須)

これらの証明書 (1 つのプライマリと省略可能なセカンダリ) は、クラスターをセキュリティで保護し、クラスターに対する不正なアクセスを防ぐために必要です。 これらの証明書は、クラスターとサーバーの認証を提供します。

クラスター認証は、クラスター フェデレーション用のノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。 サーバー認証は、管理クライアントに対するクラスター管理エンドポイントを認証します。これで、管理クライアントは、"中間者" ではなく実際のクラスターと通信していることを認識するようになります。 また、この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対する TLS も提供します。 クライアントまたはノードがノードを認証するときに行う初期チェックの 1 つは、**サブジェクト** フィールドの共通名の値を確認することです。 この共通名、または証明書のサブジェクト代替名 (SAN) の 1 つが、使用可能な共通名の一覧に存在する必要があります。

証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。 通常、これらの証明書の拡張子は .pfx または .pem です。  
* 証明書はキー交換のために作成される必要があり、Personal Information Exchange (.pfx) ファイルにエクスポートできます。
* **証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります**。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の TLS を提供するために必要です。 証明機関 (CA) から *.cloudapp.azure.com ドメインの TLS/SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

他に以下の点を考慮してください。

* **サブジェクト** フィールドには、複数の値を指定することができます。 各値には、値の型を示す初期設定が先頭に付いています。 通常、初期設定は、"*共通名*" を示す **CN** です。たとえば、**CN = www\.contoso.com** です。
* **サブジェクト** フィールドは空白にすることができます。
* 省略可能な **サブジェクト代替名** フィールドに入力する場合は、証明書の共通名と、1 つの SAN につき 1 つのエントリを指定する必要があります。 これらの値は **DNS 名** 値として入力します。 SAN を含む証明書を生成する方法については、「[セキュリティで保護された LDAP 証明書にサブジェクトの別名を追加する方法](https://support.microsoft.com/kb/931351)」を参照してください。
* 証明書の **目的** フィールド値には、**サーバー認証** や **クライアント認証** などの適切な値を含めるようにしてください。

### <a name="application-certificates-optional"></a>アプリケーション証明書 (省略可能)

アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

* アプリケーション構成値の暗号化と復号化。
* レプリケーション中のノード間のデータの暗号化。

セキュリティで保護されたクラスターを作成する概念は、Linux のクラスターでも Windows のクラスターでも同じです。

### <a name="client-authentication-certificates-optional"></a>クライアント認証証明書 (省略可能)

管理者またはユーザーのクライアント操作用に、任意の数の証明書を追加で指定できます。 相互認証が必要な場合、クライアントはこれらの証明書を使用できます。 通常、クライアント証明書はサードパーティの CA から発行されません。 代わりに、通常は、現在のユーザーの所在地の個人用ストアには、ルート機関が配置したクライアント証明書が含まれています。 証明書には、**クライアント認証** の **目的** の値が必要です。  

既定では、クラスター証明書には管理者クライアント特権があります。 これらの追加のクライアント証明書は、クラスターにインストールしてはいけませんが、クラスター構成で許可されるものとして指定されます。  ただし、クラスターに接続して操作を行うには、クライアント マシンにクライアント証明書をインストールする必要があります。

> [!NOTE]
> Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。 クライアント証明書は管理に利用できません。

## <a name="next-steps"></a>次のステップ

* [Resource Manager テンプレートを使用して Azure でクラスターを作成する](service-fabric-cluster-creation-via-arm.md)
* [Azure Portal を使用してクラスターを作成する](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
