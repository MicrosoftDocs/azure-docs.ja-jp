---
title: "Azure Service Fabric クラスターの保護 | Microsoft Docs"
description: "Azure Service Fabric クラスターのセキュリティ シナリオと、その実装に使用できる多様なテクノロジについて説明します。"
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
ms.date: 06/28/2017
ms.author: chackdan
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric クラスターのセキュリティに関するシナリオ
Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 承認されていないユーザーが接続できないようにクラスターをセキュリティで保護する必要があります。 クラスターで実稼働ワークロードを実行している場合、セキュリティで保護されたクラスターは特に重要です。 セキュリティで保護されていないクラスターを作成することはできますが、パブリック インターネットに管理エンドポイントを公開している場合に、匿名ユーザーがそのクラスターに接続できるようになります。 

この記事では、Azure クラスターとスタンドアロン クラスターのセキュリティ シナリオの概要と、その実装に使用できる多様なテクノロジについて説明します。

* ノード間のセキュリティ
* クライアントとノードの間のセキュリティ
* ロール ベースのアクセス制御 (RBAC)

## <a name="node-to-node-security"></a>ノード間のセキュリティ
ノード間のセキュリティでは、クラスター内の VM やコンピューターの間の通信をセキュリティで保護できます。 このセキュリティ シナリオでは、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

![ノード間通信の図][Node-to-Node]

Azure で実行するクラスターおよび Windows で実行するスタンドアロン クラスターのいずれにも、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx) (Windows Server コンピューターの場合) を利用できます。

### <a name="node-to-node-certificate-security"></a>ノード間の証明書セキュリティ
Service Fabric では、クラスターを作成するときにノード タイプの構成で指定した X.509 サーバー証明書を使用します。 これらの証明書の概要とその入手または作成方法はこの記事の最後に記載されています。

証明書セキュリティは、Azure Portal、Azure Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用して、クラスターを作成する際に設定します。 プライマリ証明書と、証明書のロールオーバーに使用されるオプションのセカンダリ証明書を設定できます。 設定するプライマリ証明書とセカンダリ証明書は、[クライアントとノードの間のセキュリティ](#client-to-node-security)に設定する管理用クライアント証明書と読み取り専用クライアント証明書とは異なる必要があります。

Azure 用にクラスターで証明書セキュリティを設定する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

スタンドアロン Windows Server クラスター用にクラスターで証明書セキュリティを設定する方法については、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### <a name="node-to-node-windows-security"></a>ノード間の Windows セキュリティ
スタンドアロン Windows Server クラスター用に Windows セキュリティを設定する方法については、「[Windows セキュリティを使用して Windows 上のスタンドアロン クラスターを保護する](service-fabric-windows-cluster-windows-security.md)」を参照してください。

## <a name="client-to-node-security"></a>クライアントとノードの間のセキュリティ
クライアントとノードの間のセキュリティでは、クライアントの認証を行い、クラスター内のクライアントと個々のノードの間の通信をセキュリティで保護できます。 この種類のセキュリティでは、権限のあるユーザーのみが、クラスターと、クラスターにデプロイされたアプリケーションにアクセスできます。 クライアントは、Windows セキュリティ資格情報または証明書のセキュリティ資格情報を通じて一意に識別されます。

![クライアントとノードの間の通信の図][Client-to-Node]

Azure で実行するクラスターおよび Windows で実行するスタンドアロン クラスターのいずれにも、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx)を利用できます。

### <a name="client-to-node-certificate-security"></a>クライアントとノードの間の証明書セキュリティ
クライアントとノードの間の証明書セキュリティは、Azure Portal、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用して、クラスターを作成する際に設定します。 証明書を作成するには、管理用クライアント証明書またはユーザー クライアント証明書を指定する必要があります。 指定する管理用クライアント証明書とユーザー クライアント証明書は、ベスト プラクティスとして[ノード間のセキュリティ](#node-to-node-security)に指定するプライマリ証明書とセカンダリ証明書とは異なります。 既定では、ノード間のセキュリティのクラスター証明書は許可されているクライアント管理証明書の一覧に追加されます。

管理用証明書を使用してクラスターに接続するクライアントには、管理機能へのフル アクセス権があります。 読み取り専用ユーザー クライアント証明書を使用してクラスターに接続するクライアントには、管理機能に対する読み取りアクセス権しかありません。 これらの証明書は、この記事で後述する RBAC に使用されます。

Azure 用にクラスターで証明書セキュリティを設定する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

スタンドアロン Windows Server クラスター用にクラスターで証明書セキュリティを設定する方法については、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure でのクライアントとノードの間の Azure Active Directory セキュリティ
Azure で実行されているクラスターの場合、Azure Active Directory (Azure AD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することもできます。 必要な Azure AD アーティファクトを作成する方法、クラスターの作成時にそれらを設定する方法、その後クラスターに接続する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。

## <a name="security-recommendations"></a>セキュリティに関する推奨事項
Azure クラスターについては、クライアントの認証に Azure AD セキュリティ、ノード間のセキュリティに証明書を使用することをお勧めします。

スタンドアロン Windows Server クラスターについては、Windows Server 2012 R2 と Windows Active Directory を使用している場合、グループ管理サービス アカウントで Windows セキュリティを使用することをお勧めします。 それ以外の場合は、Windows アカウントで Windows セキュリティを使用してください。

## <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)
アクセス制御を使用して、ユーザーの各グループについて特定のクラスター操作へのアクセスを制限することができます。 その結果、クラスターのセキュリティが強化されます。 クラスターに接続するクライアント用に、2 種類のアクセス制御 (管理者ロールとユーザー ロール) がサポートされています。

管理者ロールが割り当てられたユーザーには、管理機能へのフル アクセス権 (読み取り機能、書き込み機能など) があります。 ユーザー ロールが割り当てられたユーザーには、既定で管理機能 (クエリ機能など) への読み取りアクセス権のみがあります。 また、アプリケーションとサービスを解決することもできます。

クラスターを作成するときに、管理者およびユーザー クライアント ロールを設定します。 ロールを割り当てるには、ロールの種類ごとに別の ID を指定します (たとえば、証明書または Azure AD を使用します)。 既定のアクセス制御の設定と、既定の設定を変更する方法の詳細については、「[ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。

## <a name="x509-certificates-and-service-fabric"></a>X.509 証明書と Service Fabric
一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証に使用されています。 また、メッセージの暗号化やデジタル署名にも使用されます。 X.509 デジタル証明書の詳細については、「[証明書の使用](http://msdn.microsoft.com/library/ms731899.aspx)」を参照してください。

次のような、考慮すべき重要な点がいくつかあります。

* 運用環境のワークロードを実行しているクラスター用に証明書を作成するには、正しく構成された Windows Server 証明書サービスを使用するか、認定済みの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) の証明書を使用する必要があります。
* MakeCert.exe などのツールを使用して作成した一時証明書やテスト証明書は実稼働環境では使用しないでください。
* 自己署名証明書は、テスト クラスターでのみで使用できます。 自己署名証明書は実稼働環境では使用しないでください。

### <a name="server-x509-certificates"></a>サーバー X.509 証明書
サーバー証明書の主な作業は、クライアントに対するサーバー (ノード) の認証とサーバー (ノード) に対するサーバー (ノード) の認証です。 クライアントまたはノードがノードを認証するときに行う初期チェックの 1 つは、**サブジェクト** フィールドの共通名の値を確認することです。 この共通名、または証明書のサブジェクト代替名 (SAN) の 1 つが、使用可能な共通名の一覧に存在する必要があります。

SAN を含む証明書を生成する方法については、「[セキュリティで保護された LDAP 証明書にサブジェクトの別名を追加する方法](http://support.microsoft.com/kb/931351)」を参照してください。

**サブジェクト** フィールドには、複数の値を指定することができます。 各値には、値の型を示す初期設定が先頭に付いています。 通常、初期設定は、*共通名*を示す **CN** です。たとえば、**CN = www.contoso.com** です。**サブジェクト** フィールドは空白にすることができます。 省略可能な**サブジェクト代替名**フィールドに入力する場合は、証明書の共通名と、1 つの SAN につき 1 つのエントリを指定する必要があります。 これらの値は **DNS 名**値として入力します。

証明書の**目的**フィールド値には、**サーバー認証** や **クライアント認証** などの適切な値を含めるようにしてください。

### <a name="client-x509-certificates"></a>クライアント X.509 証明書
通常、クライアント証明書はサードパーティの CA から発行されません。 通常、現在のユーザーの所在地の個人用ストアには、ルート機関が配置したクライアント証明書が含まれています。この証明書には、**クライアント認証**の**目的**値が指定されています。 相互認証が必要な場合、クライアントはこの証明書を使用できます。

> [!NOTE]
> Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。 クライアント証明書は管理に利用できません。

## <a name="next-steps"></a>次のステップ
* [Resource Manager テンプレートを使用して Azure でクラスターを作成する](service-fabric-cluster-creation-via-arm.md) 
* [Azure Portal を使用してクラスターを作成する](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
