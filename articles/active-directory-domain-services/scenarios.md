---
title: Azure AD Domain Services の一般的なデプロイ シナリオ | Microsoft Docs
description: Azure Active Directory Domain Services が価値を提供し、ビジネス ニーズを満たすいくつかの一般的なシナリオとユースケースについて説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: iainfou
ms.openlocfilehash: 6f81bc2ccf11cbcc3621dc1149879864c88cf0cf
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980513"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services の一般的なユースケースとシナリオ

Azure Active Directory Domain Services (Azure AD DS) では、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証) が提供されます。 Azure AD DS は既存の Azure AD テナントと統合されるので、ユーザーは既存の資格情報を使用してサインインできるようになります。 これらのドメイン サービスを使用すれば、クラウド内でドメイン コントローラーのデプロイ、管理、および修正プログラムの適用を行う必要がなく、オンプレミスのリソースから Azure への円滑なリフトアンドシフトを実現できます。

この記事では、Azure AD DS が価値を提供し、ビジネス ニーズを満たすいくつかの一般的なビジネス シナリオについて概説します。

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 仮想マシンのセキュリティで保護された管理

お客様が単一の AD 資格情報セットを使用できるようにするには、Azure 仮想マシン (VM) を Azure AD DS マネージド ドメインに参加させます。 この方法を使用すると、各 VM 上のローカル管理者アカウントの管理や、環境間での個別のアカウントおよびパスワードの管理など、資格情報の管理に関する問題が軽減されます。

Azure AD DS マネージド ドメインに参加している VM は、グループ ポリシーを使用して管理およびセキュリティ保護することもできます。 必要なセキュリティ基準を VM に適用すれば、企業のセキュリティ ガイドラインに従って VM をロックダウンすることができます。 たとえば、グループ ポリシー管理機能を使用することで、VM 上で起動できるアプリケーションの種類を制限することができます。

![Azure 仮想マシンの効率的な管理](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

一般的なシナリオの例を見てみましょう。 サーバーおよびその他のインフラストラクチャが寿命に達するので、Contoso では現在オンプレミスでホストされているアプリケーションをクラウドに移行する必要があります。 Contoso における現在の IT 標準では、企業アプリケーションをホストするサーバーをドメインに参加させ、グループ ポリシーを使用して管理する必要があると定められています。 Contoso の IT 管理者は、Azure にデプロイされた VM をドメインに参加させたいと考えています。これにより、ユーザーが会社の資格情報を使用してサインインできるようになるので、管理はより容易になります。 VM をドメインに参加させたら、グループ ポリシーを使用して、必要なセキュリティ基準に準拠するようにその VM を構成することもできます。 Contoso では、Azure 内で独自のドメイン コントローラーをデプロイ、監視、および管理しなくても済むようにしたいと考えています。

Azure AD DS は、このユースケースに適しています。 Azure AD DS マネージド ドメインを使用すれば、VM をドメインに参加させ、単一の資格情報セットを使用し、グループ ポリシーを適用することができます。 マネージド ドメインとして、ドメイン コントローラーを自分で構成および保守する必要はありません。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* Azure AD DS マネージド ドメインでは、既定で単一のフラットな組織単位 (OU) 構造が使用されます。 ドメインに参加している VM はすべて、単一の OU 内にあります。 必要に応じて、カスタム OU を作成することができます。
* Azure AD DS では、ユーザーおよびコンピューターのコンテナーごとに組み込みの GPO が使用されます。 追加の制御を行う場合は、カスタム GPO を作成して、それらをカスタム OU に対するターゲットにすることができます。
* Azure AD DS では、基本の AD コンピューター オブジェクト スキーマがサポートされます。 コンピューター オブジェクトのスキーマを拡張することはできません。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP バインド認証を使用するオンプレミスのアプリケーションをリフトアンドシフトする

サンプル シナリオとして、Contoso には何年も前に ISV から購入したオンプレミスのアプリケーションがあります。 アプリケーションは現在、ISV によってメンテナンス モードにされています。アプリケーションに対する変更を依頼することは非常にコスト高です。 このアプリケーションは Web ベースのフロントエンドを備えています。このフロントエンドでは Web 形式を使用してユーザーの資格情報を収集し、次にオンプレミスの AD DS 環境への LDAP バインドを実行してユーザーを認証します。

![LDAP バインド](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso では、このアプリケーションを Azure に移行したいと考えています。 アプリケーションは、変更を行うことなく、そのまま機能し続ける必要があります。 さらに、ユーザーが既存の企業資格情報を使用し、さらに追加のトレーニングなしで、認証を受けることができるようにする必要があります。 これは、アプリケーションが実行されているエンド ユーザーには透過的である必要があります。

このシナリオでは、Azure AD DS を使用することで、認証プロセスの一環としてアプリケーションに LDAP バインドを実行させることができます。 構成またはユーザー エクスペリエンスに何ら変更を加えることなく、従来のオンプレミス アプリケーションを Azure にリフトアンドシフトし、そのアプリケーションで引き続きユーザーをシームレスに認証することができます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションでディレクトリに対する変更/書き込みを行う必要がないことを確認します。 Azure AD DS マネージド ドメインへの LDAP 書き込みアクセスはサポートされていません。
* Azure AD DS マネージド ドメインに対してパスワード変更を直接行うことはできません。 エンド ユーザーがパスワードを変更するには、Azure AD のセルフサービス パスワード変更メカニズムを使用するか、オンプレミスのディレクトリに対してパスワード変更を行います。 そうすると、これらの変更は自動的に同期され、Azure AD DS マネージド ドメイン内で使用できるようになります。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>LDAP 読み取りを使用してディレクトリにアクセスするオンプレミスのアプリケーションをリフトアンドシフトする

前のシナリオの例と同様に、Contoso には、約 10 年前に開発されたオンプレミスの基幹業務 (LOB) アプリケーションがあると仮定します。 このアプリケーションはディレクトリ対応のアプリケーションであり、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) を使用して、ユーザーに関する情報や属性を AD DS から読み取るように設計されています。 アプリケーションでは属性の変更もディレクトリへの書き込みも行われません。

Contoso では、このアプリケーションを Azure に移行し、現在このアプリケーションをホストしている古いオンプレミスのハードウェアを廃止したいと考えています。 REST ベースの Azure AD Graph API など、最新のディレクトリ API を使用するためにアプリケーションを書き直すことはできません。 コードの変更またはアプリケーションの書き直しを行うことなく、クラウドで実行されるようにアプリケーションを移行するには、リフトアンドシフト オプションが必要となります。

このシナリオに対応するために、Azure AD DS はアプリケーションにマネージド ドメインに対する LDAP 読み取りを実行させ、必要な属性情報を取得できます。 アプリケーションを書き直す必要がないため、Azure へのリフトアンドシフトを実施することにより、アプリの実行場所の変更に気づくことなくユーザーが引き続きアプリを使用できるようにすることができます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションでディレクトリに対する変更/書き込みを行う必要がないことを確認します。 Azure AD DS マネージド ドメインへの LDAP 書き込みアクセスはサポートされていません。
* アプリケーションがカスタム/拡張型の Active Directory スキーマを必要としないことを確認します。 Azure AD DS では、スキーマの拡張機能はサポートされていません。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>オンプレミスのサービスまたはデーモン アプリケーションを Azure に移行する

一部のアプリケーションには複数の層が含まれていますが、そのうち 1 つの層では、データベースなどのバックエンド層に対して認証された呼び出しを実行する必要があります。 これらのシナリオでは、一般的に AD サービス アカウントが使用されます。 アプリケーションを Azure にリフトアンドシフトする場合、Azure AD DS を利用すれば、サービス アカウントを引き続き同じ方法で使用できます。 オンプレミスのディレクトリから Azure AD に同期される同じサービス アカウントを使用することも、カスタム OU を作成してから、その OU 内で個別のサービス アカウントを作成することもできます。 どちらの方法でも、アプリケーションは引き続き同じ方法で機能し、他の層およびサービスに対して認証された呼び出しを行います。

![WIA を使用したサービス アカウント](./media/active-directory-domain-services-scenarios/wia-service-account.png)

このシナリオの例では、Contoso には、Web フロントエンド、SQL Server、およびバックエンド FTP サーバーを含むカスタム作成のソフトウェア資格情報コンテナー アプリケーションがあります。 サービス アカウントを使用した Windows 統合認証では、FTP サーバーに対する Web フロントエンドが認証されます。 Web フロント エンドは、サービス アカウントとして実行されるように設定されています。 バックエンド サーバーは Web フロント エンドのサービス アカウントからのアクセスを承認するように構成されています。 Contoso では、このアプリケーションを Azure に移行するために、クラウド内に独自のドメイン コントローラー VM をデプロイして管理することを望んでいません。

このシナリオでは、Web フロント エンド、SQL Server、および FTP サーバーをホストするサーバーを Azure VM に移行し、Azure AD DS マネージド ドメインに参加させることができます。 その後、VM では、オンプレミスのディレクトリにある同じサービス アカウントを使用してアプリの認証を行うことができます。これは、Azure AD Connect を使用して Azure AD 経由で同期されます。

### <a name="deployment-notes"></a>デプロイに関する注意事項

このユース ケースの例には、次のデプロイに関する考慮事項が適用されます。

* アプリケーションが認証にユーザー名とパスワードを使用していることを確認します。 証明書またはスマート カード ベースの認証は、Azure AD DS でサポートされていません。
* Azure AD DS マネージド ドメインに対してパスワード変更を直接行うことはできません。 エンド ユーザーがパスワードを変更するには、Azure AD のセルフサービス パスワード変更メカニズムを使用するか、オンプレミスのディレクトリに対してパスワード変更を行います。 そうすると、これらの変更は自動的に同期され、Azure AD DS マネージド ドメイン内で使用できるようになります。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure での Windows Server のリモート デスクトップ サービスのデプロイ

Azure AD DS を使用して、Azure にデプロイされているリモート デスクトップ サーバーに対して、マネージド ドメイン サービスを提供できます。 このデプロイ シナリオの詳細については、[Azure AD Domain Services を RDS デプロイに統合する方法][windows-rds]に関するページを参照してください。

## <a name="domain-joined-hdinsight-clusters-preview"></a>ドメイン参加済み HDInsight クラスター (プレビュー)

Azure AD DS のマネージド ドメインに Apache Ranger が有効な状態で参加する Azure HDInsight クラスターを設定できます。 現在、この機能はプレビュー段階にあります。 Apache Ranger を使用して Hive ポリシーを作成して適用することができます。さらに、データ サイエンティストなどのユーザーが ODBC ベースのツール (Excel や Tableau など) を使用して Hive に接続できるようにすることができます。 Microsoft では、HBase、Spark、Storm などの他のワークロードもドメイン参加済み HDInsight に追加できるように作業を進めています。

このデプロイ シナリオの詳細については、[ドメイン参加済み HDInsight クラスターを構成する方法][hdinsight]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

作業を開始するには、[Azure Active Directory Domain Services インスタンスを作成して構成][tutorial-create-instance]してください。

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
