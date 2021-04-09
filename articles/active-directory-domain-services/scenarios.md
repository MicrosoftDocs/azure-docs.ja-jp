---
title: Azure AD Domain Services の一般的なデプロイ シナリオ | Microsoft Docs
description: Azure Active Directory Domain Services が価値を提供し、ビジネス ニーズを満たすいくつかの一般的なシナリオとユースケースについて説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619030"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services の一般的なユースケースとシナリオ

Azure Active Directory Domain Services (Azure AD DS) では、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、ライトウェイト ディレクトリ アクセス プロトコル (LDAP)、Kerberos/NTLM 認証など) が提供されます。 Azure AD DS は既存の Azure AD テナントと統合されるので、ユーザーは既存の資格情報を使用してサインインできるようになります。 これらのドメイン サービスを使用すれば、クラウド内でドメイン コントローラーのデプロイ、管理、および修正プログラムの適用を行う必要がなく、オンプレミスのリソースから Azure への円滑なリフトアンドシフトを実現できます。

この記事では、Azure AD DS が価値を提供し、ビジネス ニーズを満たすいくつかの一般的なビジネス シナリオについて概説します。

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>クラウドで ID ソリューションを提供する一般的な方法

既存のワークロードをクラウドに移行するとき、ディレクトリ対応アプリケーションでは、オンプレミスの AD DS ディレクトリへの読み取りまたは書き込みアクセスに LDAP が使用される場合があります。 Windows Server で実行されるアプリケーションは、通常、グループ ポリシーを使用して安全に管理できるように、ドメインに参加している仮想マシン (VM) にデプロイされます。 さらに、アプリケーションは、エンド ユーザーを認証するために、Windows 統合認証 (Kerberos 認証、NTLM 認証など) に依存する場合があります。

IT 管理者は、多くの場合、次のいずれかのソリューションを使用して、Azure で実行されるアプリケーションに ID サービスを提供します。

* Azure で実行されるワークロードとオンプレミスの AD DS 環境で実行されるワークロードの間でサイト間 VPN 接続を構成する。
    * この場合、オンプレミスのドメイン コントローラーが VPN 接続経由で認証を提供します。
* Azure 仮想マシン (VM) を使用してレプリカ ドメイン コントローラーを作成し、オンプレミスから AD DS ドメインまたはフォレストを拡張する。
    * Azure VM 上で実行されるドメイン コントローラーが認証を提供し、オンプレミス AD DS 環境間でディレクトリ情報をレプリケートします。
* Azure VM で実行されるドメイン コントローラーを使用して、Azure 内にスタンドアロンの AD DS 環境をデプロイする。
    * Azure VM 上で実行されるドメイン コントローラーによって認証は提供されますが、オンプレミス AD DS 環境からレプリケートされるディレクトリ情報はありません。

これらのアプローチでは、オンプレミスのディレクトリに VPN 接続するため、アプリケーションはネットワークの一時的な異常や停止の影響を受けやすくなります。 Azure 内の VM を使用してドメイン コントローラーをデプロイする場合、VM の管理、セキュリティ保護、パッチの適用、監視、バックアップ、トラブルシューティングは IT チームが行う必要があります。

Azure AD DS では、ID サービスを提供するため、オンプレミスの AD DS 環境への VPN 接続を作成したり、Azure で VM を実行および管理したりする必要のない代替方法が提供されます。 マネージド サービスである Azure AD DS により、ハイブリッドとクラウド専用の両方の環境に使用できる統合された ID ソリューションを作成する複雑さが軽減されます。

> [!div class="nextstepaction"]
> [Azure AD DS を、Azure VM またはオンプレミスにおける Azure AD および自己管理型 AD DS と比較する][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>ハイブリッド組織向けの Azure AD DS

多くの組織は、クラウドとオンプレミスの両方のアプリケーション ワークロードを含むハイブリッド インフラストラクチャを運用しています。 リフト アンド シフト戦略の一環として Azure に移行されたレガシ アプリケーションでも、従来の LDAP 接続を使用して ID 情報を提供することができます。 このハイブリッド インフラストラクチャをサポートするために、オンプレミスの AD DS 環境からの ID 情報を Azure AD テナントに同期させることができます。 Azure AD DS では、Azure 内のこれらのレガシ アプリケーションに ID ソースを提供します。オンプレミスのディレクトリ サービスへのアプリケーション接続を構成して管理する必要はありません。

オンプレミスと Azure の両方のリソースを実行するハイブリッド組織である Litware Corporation の例を見てみましょう。

![オンプレミスの同期を含むハイブリッド組織向けの Azure Active Directory Domain Services](./media/overview/synced-tenant.png)

* ドメイン サービスを必要とするアプリケーションおよびサーバー ワークロードは、Azure の仮想ネットワークにデプロイされています。
    * これには、リフト アンド シフト戦略の一環として Azure に移行されたレガシ アプリケーションが含まれる場合があります。
* ID 情報をオンプレミスのディレクトリから Azure AD テナントに同期するために、Litware Corporation は、[Azure AD Connect][azure-ad-connect]をデプロイしました。
    * 同期される ID 情報には、ユーザー アカウントとグループ メンバーシップが含まれます。
* Litware の IT チームは、この仮想ネットワークまたはピアリングされた仮想ネットワーク内の Azure AD テナントに対して Azure AD DS を有効にします。
* Azure 仮想ネットワーク内にデプロイされたアプリケーションと VM は、ドメイン参加、LDAP 読み取り、LDAP バインド、NTLM 認証、Kerberos 認証、グループ ポリシーなどの Azure AD DS 機能を使用できます。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 マネージド ドメインに Azure AD Connect をインストールして元の Azure AD にオブジェクトを同期することはサポートされていません。

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>クラウド専用組織向けの Azure AD DS

クラウド専用 Azure AD テナントには、オンプレミスの ID ソースはありません。 たとえば、ユーザー アカウントとグループ メンバーシップは、Azure AD 内で直接作成および管理されます。

ここで、ID に Azure AD を使用するクラウド専用組織である Contoso の例を見てみましょう。 ユーザー ID、ユーザーの資格情報、およびグループ メンバーシップはすべて Azure AD で作成および管理されます。 オンプレミスのディレクトリから ID 情報を同期するために Azure AD Connect を追加構成する必要はありません。

![オンプレミスの同期を行わない、クラウド専用組織向けの Azure Active Directory Domain Services](./media/overview/cloud-only-tenant.png)

* ドメイン サービスを必要とするアプリケーションおよびサーバー ワークロードは、Azure の仮想ネットワークにデプロイされています。
* Contoso の IT チームは、この仮想ネットワークまたはピアリングされた仮想ネットワーク内の Azure AD テナントに対して Azure AD DS を有効にします。
* Azure 仮想ネットワーク内にデプロイされたアプリケーションと VM は、ドメイン参加、LDAP 読み取り、LDAP バインド、NTLM 認証、Kerberos 認証、グループ ポリシーなどの Azure AD DS 機能を使用できます。

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 仮想マシンのセキュリティで保護された管理

お客様が単一の AD 資格情報セットを使用できるようにするには、Azure 仮想マシン (VM) を Azure AD DS マネージド ドメインに参加させます。 この方法を使用すると、各 VM 上のローカル管理者アカウントの管理や、環境間での個別のアカウントおよびパスワードの管理など、資格情報の管理に関する問題が軽減されます。

マネージド ドメインに参加している VM は、グループ ポリシーを使用して管理およびセキュリティ保護することもできます。 必要なセキュリティ基準を VM に適用すれば、企業のセキュリティ ガイドラインに従って VM をロックダウンすることができます。 たとえば、グループ ポリシー管理機能を使用することで、VM 上で起動できるアプリケーションの種類を制限することができます。

![Azure 仮想マシンの効率的な管理](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

一般的なシナリオの例を見てみましょう。 サーバーおよびその他のインフラストラクチャが寿命に達するので、Contoso では現在オンプレミスでホストされているアプリケーションをクラウドに移行する必要があります。 Contoso における現在の IT 標準では、企業アプリケーションをホストするサーバーをドメインに参加させ、グループ ポリシーを使用して管理する必要があると定められています。

Contoso の IT 管理者は、Azure にデプロイされた VM をドメインに参加させたいと考えています。これにより、ユーザーが会社の資格情報を使用してサインインできるようになるので、管理はより容易になります。 VM をドメインに参加させたら、グループ ポリシー オブジェクト (GPO) を使用して、必要なセキュリティ基準に準拠するようにその VM を構成することもできます。 Contoso では、Azure 内で独自のドメイン コントローラーをデプロイ、監視、および管理しなくても済むようにしたいと考えています。

Azure AD DS は、このユースケースに適しています。 マネージド ドメインを使用すれば、VM をドメインに参加させ、単一の資格情報セットを使用し、グループ ポリシーを適用することができます。 また、マネージド ドメインであるため、ドメイン コントローラーを自分で構成および保守する必要はありません。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* マネージド ドメインでは、既定で単一のフラットな組織単位 (OU) 構造が使用されます。 ドメインに参加している VM はすべて、単一の OU 内にあります。 必要に応じて、[カスタム OU][custom-ou] を作成することができます。
* Azure AD DS では、ユーザーおよびコンピューターのコンテナーごとに組み込みの GPO が使用されます。 追加の制御を行う場合は、[カスタム GPO を作成][create-gpo]して、カスタム OU をターゲットにすることができます。
* Azure AD DS では、基本の AD コンピューター オブジェクト スキーマがサポートされます。 コンピューター オブジェクトのスキーマを拡張することはできません。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP バインド認証を使用するオンプレミスのアプリケーションをリフトアンドシフトする

サンプル シナリオとして、Contoso には何年も前に ISV から購入したオンプレミスのアプリケーションがあります。 アプリケーションは現在、ISV によってメンテナンス モードにされています。アプリケーションに対する変更を依頼することは非常にコスト高です。 このアプリケーションは Web ベースのフロントエンドを備えています。このフロントエンドでは Web 形式を使用してユーザーの資格情報を収集し、次にオンプレミスの AD DS 環境への LDAP バインドを実行してユーザーを認証します。

![LDAP バインド](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso では、このアプリケーションを Azure に移行したいと考えています。 アプリケーションは、変更を行うことなく、そのまま機能し続ける必要があります。 さらに、ユーザーが既存の企業資格情報を使用し、さらに追加のトレーニングなしで、認証を受けることができるようにする必要があります。 これは、アプリケーションが実行されているエンド ユーザーには透過的である必要があります。

このシナリオでは、Azure AD DS を使用することで、認証プロセスの一環としてアプリケーションに LDAP バインドを実行させることができます。 構成またはユーザー エクスペリエンスに何ら変更を加えることなく、従来のオンプレミス アプリケーションを Azure にリフトアンドシフトし、そのアプリケーションで引き続きユーザーをシームレスに認証することができます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションでディレクトリに対する変更/書き込みを行う必要がないことを確認します。 マネージド ドメインへの LDAP 書き込みアクセスはサポートされていません。
* マネージド ドメインに対してパスワード変更を直接行うことはできません。 エンド ユーザーがパスワードを変更するには、[Azure AD のセルフサービス パスワード変更メカニズム][sspr]を使用するか、オンプレミスのディレクトリに対してパスワード変更を行います。 その後、これらの変更は自動的に同期され、マネージド ドメイン内で使用できるようになります。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>LDAP 読み取りを使用してディレクトリにアクセスするオンプレミスのアプリケーションをリフトアンドシフトする

前のシナリオの例と同様に、Contoso には、約 10 年前に開発されたオンプレミスの基幹業務 (LOB) アプリケーションがあると仮定します。 このアプリケーションはディレクトリ対応であり、LDAP を使用して、ユーザーに関する情報や属性を AD DS から読み取るように設計されています。 アプリケーションでは属性の変更もディレクトリへの書き込みも行われません。

Contoso では、このアプリケーションを Azure に移行し、現在このアプリケーションをホストしている古いオンプレミスのハードウェアを廃止したいと考えています。 REST ベースの Microsoft Graph API など、最新のディレクトリ API を使用するためにアプリケーションを書き直すことはできません。 コードの変更またはアプリケーションの書き直しを行うことなく、クラウドで実行されるようにアプリケーションを移行するには、リフトアンドシフト オプションが必要となります。

このシナリオに対応するために、Azure AD DS はアプリケーションにマネージド ドメインに対する LDAP 読み取りを実行させ、必要な属性情報を取得できます。 アプリケーションを書き直す必要がないため、Azure へのリフトアンドシフトを実施することにより、アプリの実行場所の変更に気づくことなくユーザーが引き続きアプリを使用できるようにすることができます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションでディレクトリに対する変更/書き込みを行う必要がないことを確認します。 マネージド ドメインへの LDAP 書き込みアクセスはサポートされていません。
* アプリケーションがカスタム/拡張型の Active Directory スキーマを必要としないことを確認します。 Azure AD DS では、スキーマの拡張機能はサポートされていません。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>オンプレミスのサービスまたはデーモン アプリケーションを Azure に移行する

一部のアプリケーションには複数の層が含まれていますが、そのうち 1 つの層では、データベースなどのバックエンド層に対して認証された呼び出しを実行する必要があります。 これらのシナリオでは、一般的に AD サービス アカウントが使用されます。 アプリケーションを Azure にリフトアンドシフトする場合、Azure AD DS を利用すれば、サービス アカウントを引き続き同じ方法で使用できます。 オンプレミスのディレクトリから Azure AD に同期される同じサービス アカウントを使用することも、カスタム OU を作成してから、その OU 内で個別のサービス アカウントを作成することもできます。 どちらの方法でも、アプリケーションは引き続き同じ方法で機能し、他の層およびサービスに対して認証された呼び出しを行います。

![WIA を使用したサービス アカウント](./media/active-directory-domain-services-scenarios/wia-service-account.png)

このシナリオの例では、Contoso には、Web フロントエンド、SQL Server、およびバックエンド FTP サーバーを含むカスタム作成のソフトウェア資格情報コンテナー アプリケーションがあります。 サービス アカウントを使用した Windows 統合認証では、FTP サーバーに対する Web フロントエンドが認証されます。 Web フロント エンドは、サービス アカウントとして実行されるように設定されています。 バックエンド サーバーは Web フロント エンドのサービス アカウントからのアクセスを承認するように構成されています。 Contoso では、このアプリケーションを Azure に移行するために、クラウド内に独自のドメイン コントローラー VM をデプロイして管理することを望んでいません。

このシナリオでは、Web フロント エンド、SQL Server、および FTP サーバーをホストするサーバーを Azure VM に移行し、マネージド ドメインに参加させることができます。 その後、VM では、オンプレミスのディレクトリにある同じサービス アカウントを使用してアプリの認証を行うことができます。これは、Azure AD Connect を使用して Azure AD 経由で同期されます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションが認証にユーザー名とパスワードを使用していることを確認します。 証明書またはスマート カード ベースの認証は、Azure AD DS でサポートされていません。
* マネージド ドメインに対してパスワード変更を直接行うことはできません。 エンド ユーザーがパスワードを変更するには、[Azure AD のセルフサービス パスワード変更メカニズム][sspr]を使用するか、オンプレミスのディレクトリに対してパスワード変更を行います。 その後、これらの変更は自動的に同期され、マネージド ドメイン内で使用できるようになります。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure での Windows Server のリモート デスクトップ サービスのデプロイ

Azure AD DS を使用して、Azure にデプロイされているリモート デスクトップ サーバーに対して、マネージド ドメイン サービスを提供できます。

このデプロイ シナリオの詳細については、[Azure AD Domain Services を RDS デプロイに統合する方法][windows-rds]に関するページを参照してください。

## <a name="domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスター

マネージド ドメインに Apache Ranger が有効な状態で参加する Azure HDInsight クラスターを設定できます。 Apache Ranger を使用して Hive ポリシーを作成して適用することができます。さらに、データ サイエンティストなどのユーザーが ODBC ベースのツール (Excel や Tableau など) を使用して Hive に接続できるようにすることができます。 Microsoft では、HBase、Spark、Storm などの他のワークロードもドメイン参加済み HDInsight に追加できるように作業を進めています。

このデプロイ シナリオの詳細については、[ドメイン参加済み HDInsight クラスターを構成する方法][hdinsight]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

作業を開始するには、[Azure Active Directory Domain Services マネージド ドメインの作成と構成][tutorial-create-instance]に関する記事をご覧ください。

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds