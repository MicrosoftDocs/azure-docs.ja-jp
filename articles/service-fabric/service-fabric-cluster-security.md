---
title: "Service Fabric クラスターをセキュリティで保護する | Microsoft Docs"
description: "Service Fabric クラスターのためのセキュリティ シナリオと、これらのシナリオの実装に使用されるテクノロジについて説明します。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 628df1df8f5de99a5c18d0df5b7ee41e2fb747df
ms.openlocfilehash: c3ff370b105a1f9bdacd1bdb4b32d6209e150be2
ms.lasthandoff: 12/08/2016


---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric クラスターのセキュリティに関するシナリオ
Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 クラスターは、特に運用ワークロードが実行されている場合に、未承認ユーザーがクラスターに接続するのを防ぐためにセキュリティで保護する必要があります。 セキュリティで保護されていないクラスターを作成することはできますが、これを行うと、パブリック インターネットに管理エンドポイントを公開している場合に、匿名ユーザーがそのクラスターに接続できるようになります。 

この記事では、Azure またはスタンドアロンで実行されるクラスターのセキュリティに関するシナリオと、そのようなシナリオを実装するために使用するさまざまなテクノロジの概要を示します。 クラスターのセキュリティに関するシナリオは次のとおりです。

* ノード間のセキュリティ
* クライアントとノードの間のセキュリティ
* ロール ベースのアクセス制御 (RBAC)

## <a name="node-to-node-security"></a>ノード間のセキュリティ
クラスター内の VM やマシンの間の通信をセキュリティで保護します。 これにより、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

![ノード間通信の図][Node-to-Node]

Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx) (Windows Server マシンの場合) を利用できます。

### <a name="node-to-node-certificate-security"></a>ノード間の証明書セキュリティ
Service Fabric では、クラスターを作成するときにノード タイプの構成で指定した X.509 サーバー証明書を使用します。 これらの証明書の概要とその入手または作成方法はこの記事の最後に記載されています。

証明書セキュリティは、Azure ポータル、Azure Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用してクラスターを作成する際に構成されます。 プライマリ証明書と、証明書のロールオーバーに使用されるオプションのセカンダリ証明書を指定できます。 指定するプライマリ証明書とセカンダリ証明書は、 [クライアントとノードの間のセキュリティ](#client-to-node-security)に指定する管理用クライアント証明書と読み取り専用クライアント証明書とは異なります。

Azure のクラスターで証明書セキュリティを構成する方法については、 [Azure Resource Manager テンプレートを使用したクラスターの設定](service-fabric-cluster-creation-via-arm.md) に関する記事を参照してください。

スタンドアロン Windows Server の場合は、「 [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護 ](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>ノード間の Windows セキュリティ
スタンドアロン Windows Server の場合は、「 [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>クライアントとノードの間のセキュリティ
クライアントの認証を行い、クラスター内のクライアントと個々のノードの間の通信をセキュリティで保護します。 この種類のセキュリティでは、クライアント側の通信を認証して保護するため、権限のあるユーザーのみがクラスターにデプロイされたクラスターとアプリケーションにアクセスできます。 クライアントは、Windows セキュリティ資格情報または証明書のセキュリティ資格情報を通じて一意に識別されます。

![クライアントとノードの間の通信の図][Client-to-Node]

Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx)を利用できます。

### <a name="client-to-node-certificate-security"></a>クライアントとノードの間の証明書セキュリティ
 クライアントとノードの間の証明書セキュリティを構成するには、Azure ポータル、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートでクラスターを作成するときに、管理用クライアント証明書やユーザー クライアント証明書を指定します。  指定する管理用クライアント証明書とユーザー クライアント証明書は、 [ノード間のセキュリティ](#node-to-node-security)に指定するプライマリ証明書とセカンダリ証明書とは異なります。

管理用証明書を使用してクラスターに接続するクライアントには、管理機能へのフル アクセス権があります。  読み取り専用ユーザー クライアント証明書を使用してクラスターに接続するクライアントには、管理機能に対する読み取りアクセス権しかありません。 つまり、これらの証明書は、この記事の中で後で説明するロールベースのアクセス制御 (RBAC) に使用されます。

Azure のクラスターで証明書セキュリティを構成する方法については、 [Azure Resource Manager テンプレートを使用したクラスターの設定](service-fabric-cluster-creation-via-arm.md) に関する記事を参照してください。

スタンドアロン Windows Server の場合は、「 [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護 ](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Azure でのクライアントとノードの間の Azure Active Directory (AAD) セキュリティ
Azure で実行されているクラスターは、Azure Active Directory (AAD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することもできます。 必要な AAD アーティファクトを作成する方法、クラスターの作成中にそれらを設定する方法、その後これらのクラスターに接続する方法については、 [Azure Resource Manager テンプレートを使用したクラスターの設定](service-fabric-cluster-creation-via-arm.md) に関する記事を参照してください。

## <a name="security-recommendations"></a>セキュリティに関する推奨事項
Azure クラスターについては、クライアントの認証に AAD セキュリティ、ノード間のセキュリティに証明書を使用することをお勧めします。

スタンドアロン Windows Server クラスターについては、Windows Server 2012 R2 と Active Directory を使用している場合、グループ管理アカウント (GMA) で Windows セキュリティを使用することをお勧めします。 それ以外の場合は、引き続き Windows アカウントで Windows セキュリティを使用してください。

## <a name="role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC)
アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。 クラスターに接続するクライアント用に、2 種類の異なるアクセス制御 (管理者ロールとユーザー ロール) がサポートされています。

管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。

クラスターの作成時に、管理者ロールとユーザー クライアント ロールを指定して、それぞれに個別の ID (証明書、AAD など) を設定します。 既定のアクセス制御の設定と、既定の設定を変更する方法の詳細については、「[ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。

## <a name="x509-certificates-and-service-fabric"></a>X.509 証明書と Service Fabric
一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証、暗号化、メッセージのデジタル署名に使用されています。 これらの証明書の詳細については、「 [証明書の使用](http://msdn.microsoft.com/library/ms731899.aspx)」を参照してください。

次のような、考慮すべき重要な点がいくつかあります。

* 運用環境のワークロードを実行しているクラスターに使用する証明書は、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの [証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)から取得する必要があります。
* 運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください。
* 自己署名入りの証明書は使用できますが、運用環境のクラスターではなく、テスト環境のクラスターにのみ使用してください。

### <a name="server-x509-certificates"></a>サーバー X.509 証明書
サーバー証明書の主な作業は、クライアントに対するサーバー (ノード) の認証とサーバー (ノード) に対するサーバー (ノード) の認証です。 クライアントまたはノードがノードを認証するときに行う初期チェックの 1 つは、サブジェクト フィールドの共通名の値を確認することです。 この共通名、または証明書のサブジェクト代替名の 1 つが、使用可能な共通名の一覧に存在する必要があります。

「 [セキュリティで保護された LDAP 証明書にサブジェクトの別名を追加する方法](http://support.microsoft.com/kb/931351)」という記事では、サブジェクト代替名 (SAN) で証明書を生成する方法について説明しています。

サブジェクト フィールドには複数の値を指定できます。各値には、その値を示す初期設定のプレフィックスを付けます。 よく使われる初期設定は、共通名を示す "CN" です。たとえば、"CN = www.contoso.com" です。 サブジェクト フィールドは空白にすることもできます。 省略可能なサブジェクト代替名フィールドに入力する場合は、証明書の共通名と、1 つのサブジェクト代替名につき 1 つのエントリを指定する必要があります。 これらの値は DNS 名値として入力します。

証明書の目的フィールド値には、"サーバー認証" や "クライアント認証" などの適切な値を含めるようにしてください。

### <a name="client-x509-certificates"></a>クライアント X.509 証明書
通常、クライアント証明書はサードパーティの証明機関から発行されません。 通常、現在のユーザーの所在地の個人用ストアには、ルート機関が配置した "クライアント認証" 用のクライアント証明書が含まれています。 相互認証が必要な場合、クライアントはこのような証明書を使用できます。

> [!NOTE]
> Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。 クライアント証明書は管理に利用できません。
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>次のステップ
この記事では、クラスターのセキュリティに関する概念的な情報について説明します。 次に、


1.  [Resource Manager テンプレートを使用して Azure でクラスターを作成する](service-fabric-cluster-creation-via-arm.md) 
2.  [Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

