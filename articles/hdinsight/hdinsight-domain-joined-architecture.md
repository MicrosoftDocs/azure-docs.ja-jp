---
title: "ドメイン参加済み Azure HDInsight のアーキテクチャ | Microsoft Docs"
description: "ドメイン参加済み HDInsight を計画する方法について説明します。"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 69378496fb7e4176243d36950e7270809248d2bb
ms.lasthandoff: 03/25/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>HDInsight で Azure のドメイン参加済み Hadoop クラスターを計画する

従来の Hadoop は単一ユーザーのクラスターです。 これは、大規模なデータ ワークロードを構築する小規模なアプリケーション チームを抱えているほとんどの企業に適しています。 Hadoop が人気を得るにつれて、多くの企業では、IT チームが管理するクラスターを複数のアプリケーション チームが共有するモデルに移行しつつあります。 このため、マルチユーザーのクラスターに関わる機能は、Azure HDInsight で最も需要が高い機能となっています。

HDInsight では、独自のマルチユーザー認証と承認が構築される代わりに、最も人気のある ID プロバイダーである Azure Active Directory (Azure AD) が利用されます。 Azure AD の強力なセキュリティ機能を使用して、HDInsight でマルチユーザー承認を管理できます。 HDInsight と Azure AD を統合することで、Azure AD の資格情報を使ってクラスターと通信できます。 HDInsight は Azure AD ユーザーをローカルの Hadoop ユーザーにマップするため、HDInsight で実行されているすべてのサービス (Ambari、Hive サーバー、Ranger、Spark Thrift サーバーなど) は認証済みユーザーに対してシームレスに動作します。

## <a name="integrate-hdinsight-with-azure-ad"></a>HDInsight と Azure AD の統合

HDInsight を Azure AD と統合すると、HDInsight クラスター ノードは Azure AD ドメインに参加することになります。 HDInsight は、クラスターで実行されている Hadoop サービスのサービス プリンシパルを作成し、Azure AD で指定された組織単位 (OU) 内に配置します。 また、ドメインに参加しているノードの IP アドレスについて、Azure AD ドメインに逆引き DNS マッピングも作成します。

この設定は、複数のアーキテクチャを使用して実現できます。 以下のアーキテクチャから好きなものを選んでください。

**Azure IaaS で実行されている Azure AD と統合された HDInsight**

これは、HDInsight を Azure AD と統合する最も簡単なアーキテクチャです。 Azure AD ドメイン コントローラーは、Azure 内の 1 つ (または複数) の仮想マシン (VM) で動作します。 これらの VM は通常、仮想ネットワーク内に存在します。 HDInsight クラスター用に仮想ネットワークをもう 1 つ設定します。 HDInsight に Azure AD を認識させるには、[VNet 間のピアリング](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)を使用してこれらの仮想ネットワークをピアリングする必要があります。

![ドメイン参加の HDInsight クラスター トポロジ](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> このアーキテクチャでは、HDInsight クラスターで Azure Data Lake Store を使用できません。


Azure AD の前提条件:

* [組織単位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)を作成する必要があります。その中には、HDInsight クラスターの VM と、クラスターによって使用されるサービス プリンシパルを配置します。
* Azure AD と通信するために、[ライトウェイト ディレクトリ アクセス プロトコル](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) を設定する必要があります。 LDAPS を設定するために使用する証明書は、(自己署名証明書ではなく) 実際の証明書にする必要があります。
* HDInsight サブネットの IP アドレス範囲 (たとえば、上の図では 10.2.0.0/24) について、ドメインに逆引き DNS ゾーンを作成する必要があります。
* サービス アカウントかユーザー アカウントが必要です。 このアカウントを使用して HDInsight クラスターを作成します。 このアカウントには、次のアクセス許可が必要です。

    - サービス プリンシパル オブジェクトとマシン オブジェクトを組織単位内に作成するためのアクセス許可。
    - 逆引き DNS プロキシ規則を作成するためのアクセス許可。
    - マシンを Active Directory ドメインに参加させるためのアクセス許可。

**クラウド専用の Azure AD と統合された HDInsight**

クラウド専用の Azure AD の場合、ドメイン コントローラーを構成して HDInsight と Azure AD を統合できるようにします。 これは、[Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) を使用して実現されます。 Azure AD DS によってクラウド内にドメイン コントローラー マシンが作成され、そのマシン用の IP アドレスが提供されます。 高可用性を確保するために 2 つのドメイン コントローラーが作成されます。

現時点では、Azure AD DS はクラシック仮想ネットワークにしか存在せず、 アクセスする手段は Azure クラシック ポータルのみになります。 HDInsight 仮想ネットワークは Azure Portal に存在します。これは、VNet 間のピアリングを使用してクラシック仮想ネットワークとピアリングする必要があります。

> [!NOTE]
> クラシック仮想ネットワークと Azure Resource Manager 仮想ネットワークの間でピアリングするには、両方の仮想ネットワークが同じリージョンに存在しており、なおかつ同じ Azure サブスクリプションに属していることが必要になります。

![ドメイン参加の HDInsight クラスター トポロジ](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Azure AD の前提条件:

* [組織単位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)を作成する必要があります。その中には、HDInsight クラスターの VM と、クラスターによって使用されるサービス プリンシパルを配置します。
* Azure AD DS の構成時に [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) を設定する必要があります。 LDAPS を設定するために使用する証明書は、(自己署名証明書ではなく) 実際の証明書にする必要があります。
* HDInsight サブネットの IP アドレス範囲 (たとえば、上の図では 10.2.0.0/24) について、ドメインに逆引き DNS ゾーンを作成する必要があります。
* [パスワード ハッシュ](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)は、Azure AD から Azure AD DS に同期する必要があります。
* サービス アカウントかユーザー アカウントが必要です。 このアカウントを使用して HDInsight クラスターを作成します。 このアカウントには、次のアクセス許可が必要です。

    - サービス プリンシパル オブジェクトとマシン オブジェクトを組織単位内に作成するためのアクセス許可。
    - 逆引き DNS プロキシ規則を作成するためのアクセス許可。
    - マシンを Azure AD ドメインに参加させるためのアクセス許可。

**VPN 経由でオンプレミスの Active Directory に統合された HDInsight**

このアーキテクチャは、Azure IaaS で実行されている Azure AD と統合された HDInsight に似ています。 唯一の違いは Azure AD がオンプレミスであり、HDInsight の Azure AD に対する認識が [Azure からオンプレミス ネットワークへの VPN 接続](../expressroute/expressroute-introduction.md)経由であることです。

![ドメイン参加の HDInsight クラスター トポロジ](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> このアーキテクチャでは、HDInsight クラスターで Azure Data Lake Store を使用できません。

Azure AD の前提条件:

* [組織単位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)を作成する必要があります。その中には、HDInsight クラスターの VM と、クラスターによって使用されるサービス プリンシパルを配置します。
* Azure AD と通信するために、[LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) を設定する必要があります。 LDAPS を設定するために使用する証明書は、(自己署名証明書ではなく) 実際の証明書にする必要があります。
* HDInsight サブネットの IP アドレス範囲 (たとえば、上の図では 10.2.0.0/24) について、ドメインに逆引き DNS ゾーンを作成する必要があります。
* サービス アカウントかユーザー アカウントが必要です。 このアカウントを使用して HDInsight クラスターを作成します。 このアカウントには、次のアクセス許可が必要です。

    - サービス プリンシパル オブジェクトとマシン オブジェクトを組織単位内に作成するためのアクセス許可。
    - 逆引き DNS プロキシ規則を作成するためのアクセス許可。
    - マシンを Azure AD ドメインに参加させるためのアクセス許可。

**Azure AD と同期されたオンプレミスの Active Directory に統合された HDInsight**

このアーキテクチャは、クラウド専用の Azure AD と統合された HDInsight に似ています。 唯一の違いは、オンプレミスの Active Directory が Azure AD と同期されることです。 クラウドでドメイン コントローラーを構成して、HDInsight と Azure AD を統合できるようにします。 これは、[Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) を使用して実現されます。 Azure AD DS によってクラウド内にドメイン コントローラー マシンが作成され、そのマシン用の IP アドレスが提供されます。 高可用性を確保するために 2 つのドメイン コントローラーが作成されます。

現時点では、Azure AD DS はクラシック仮想ネットワークにしか存在せず、 アクセスする手段は Azure クラシック ポータルのみになります。 HDInsight 仮想ネットワークは Azure Portal に存在します。これは、VNet 間のピアリングを使用してクラシック仮想ネットワークとピアリングする必要があります。

> [!NOTE]
> クラシック仮想ネットワークと Azure Resource Manager 仮想ネットワークの間でピアリングするには、両方の仮想ネットワークが同じリージョンに存在しており、なおかつ同じ Azure サブスクリプションに属していることが必要になります。

![ドメイン参加の HDInsight クラスター トポロジ](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Azure AD の前提条件:

* [組織単位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)を作成する必要があります。その中には、HDInsight クラスターの VM と、クラスターによって使用されるサービス プリンシパルを配置します。
* Azure AD DS の構成時に [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) を設定する必要があります。 LDAPS を設定するために使用する証明書は、(自己署名証明書ではなく) 実際の証明書にする必要があります。
* HDInsight サブネットの IP アドレス範囲 (たとえば、上の図では 10.2.0.0/24) について、ドメインに逆引き DNS ゾーンを作成する必要があります。
* [パスワード ハッシュ](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)は、Azure AD から Azure AD DS に同期する必要があります。
* サービス アカウントかユーザー アカウントが必要です。 このアカウントを使用して HDInsight クラスターを作成します。 このアカウントには、次のアクセス許可が必要です。

    - サービス プリンシパル オブジェクトとマシン オブジェクトを組織単位内に作成するためのアクセス許可。
    - 逆引き DNS プロキシ規則を作成するためのアクセス許可。
    - マシンを Active Directory ドメインに参加させるためのアクセス許可。

**既定以外の Azure AD と統合された HDInsight (テストと開発のみに推奨)**

このアーキテクチャは、クラウド専用の Azure AD と統合された HDInsight に似ています。 ほとんどの企業では、Azure AD に対する管理アクセスは特定の個人に限定されています。 したがって、概念実証を行う際やドメイン参加済みクラスターの作成を試してみる際は、管理者が Azure AD で前提条件を構成するのを待つのではなく、サブスクリプション内で Azure AD インスタンスを作成したほうが便利な場合があります。 これは自分で作成した Azure AD インスタンスであるため、Azure AD DS を構成するうえで必要な、すべての Azure AD のアクセス許可があります。

Azure AD DS によってクラウド内にドメイン コントローラー マシンが作成され、そのマシン用の IP アドレスが提供されます。 高可用性を確保するために 2 つのドメイン コントローラーが作成されます。

Azure AD DS はクラシック仮想ネットワークにのみ存在するため、Azure クラシック ポータルにアクセスする必要があるほか、Azure AD DS を構成するためのクラシック仮想ネットワークを作成する必要があります。 HDInsight 仮想ネットワークは Azure Portal に存在します。これは、VNet 間のピアリングを使用してクラシック仮想ネットワークとピアリングする必要があります。

> [!NOTE]
> クラシック仮想ネットワークと Azure Resource Manager 仮想ネットワークの間でピアリングするには、両方の仮想ネットワークが同じリージョンに存在しており、なおかつ同じ Azure サブスクリプションに属していることが必要になります。

![ドメイン参加の HDInsight クラスター トポロジ](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Azure AD の前提条件:

* [組織単位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)を作成する必要があります。その中には、HDInsight クラスターの VM と、クラスターによって使用されるサービス プリンシパルを配置します。
* Azure AD DS の構成時に [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) を設定する必要があります。 LDAPS を構成するために[自己署名証明書](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)を作成できます。 ただし、自己署名証明書を使用するには、<a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a> に例外を要求する必要があります。
* HDInsight サブネットの IP アドレス範囲 (たとえば、上の図では 10.2.0.0/24) について、ドメインに逆引き DNS ゾーンを作成する必要があります。
* [パスワード ハッシュ](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)は、Azure AD から Azure AD DS に同期する必要があります。
* サービス アカウントかユーザー アカウントが必要です。 このアカウントを使用して HDInsight クラスターを作成します。 このアカウントには、次のアクセス許可が必要です。

    - サービス プリンシパル オブジェクトとマシン オブジェクトを組織単位内に作成するためのアクセス許可。
    - 逆引き DNS プロキシ規則を作成するためのアクセス許可。
    - マシンを Azure Active Directory ドメインに参加させるためのアクセス許可。

## <a name="next-steps"></a>次のステップ
* ドメイン参加済み HDInsight クラスターを構成するには、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関する記事をご覧ください。
* ドメイン参加済み HDInsight クラスターを管理するには、[ドメイン参加済み HDInsight クラスターの管理](hdinsight-domain-joined-manage.md)に関する記事をご覧ください。
* Hive ポリシーを構成して Hive クエリを実行するには、[ドメイン参加済み HDInsight クラスターでの Hive ポリシーの構成](hdinsight-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行する方法については、[HDInsight で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

