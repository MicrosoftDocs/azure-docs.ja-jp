---
title: Enterprise セキュリティ パッケージを使用する Apache Hadoop セキュリティの概要
description: Enterprise セキュリティ パッケージがエンタープライズ セキュリティの 4 本柱をサポートする方法について説明します。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 8a568302b2fd8ed0140c2ad084495d41cc09ed8f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994590"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを使用する Apache Hadoop セキュリティの概要

これまでに、Azure HDInsight では、単一ユーザーのローカル管理者のみをサポートしていました。これは、小規模なアプリケーション チームや部門に適していました。 Apache Hadoop ベースのワークロードがエンタープライズ部門に普及するに伴い、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御などのエンタープライズ レベルの機能の必要性がますます重要になっています。 

Active Directory ドメインに参加する HDInsight クラスターを、Enterprise セキュリティ パッケージ (ESP) を使用して作成できます。 その後、Azure Active Directory を介した認証を通じて HDInsight クラスターにサインインできる企業の従業員の一覧を構成できるようになります。 社外のユーザーは、HDInsight クラスターにサインインすることもアクセスすることもできません。 

エンタープライズ管理者は、[Apache Ranger](https://hortonworks.com/apache/ranger/) を使用して Apache Hive のセキュリティを確保するためのロールベースのアクセス制御 (RBAC) を構成できます。 RBAC を構成すると、必要な内容へのデータ アクセスのみに制限されます。 最後に、管理者は従業員によるデータ アクセスとアクセス制御ポリシーに加えられた変更を監査できます。 その後、管理者は企業リソースにおいて高度なガバナンスを実現できます。

> [!NOTE]  
> Apache Oozie は、ESP クラスターで使用できるようになりました。 Oozie Web UI にアクセスするには、[トンネリング](../hdinsight-linux-ambari-ssh-tunnel.md)を有効にする必要があります。

エンタープライズ セキュリティには、境界セキュリティ、認証、承認、暗号化の 4 つの大きな柱があります。

![エンタープライズ セキュリティの 4 つの柱での Enterprise セキュリティ パッケージ HDInsight クラスターの利点](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png)。

## <a name="perimeter-security"></a>境界セキュリティ
HDInsight の境界セキュリティは、仮想ネットワークと Azure VPN Gateway サービスを使用して実現されます。 エンタープライズ管理者は、仮想ネットワーク内に ESP クラスターを作成し、ネットワーク セキュリティ グループ (ファイアウォール規則) を使用して仮想ネットワークへのアクセスを制限できます。 HDInsight クラスターと通信できるのは、受信ファイアウォール規則で定義されている IP アドレスだけです。 この構成では、境界セキュリティを提供します。

境界セキュリティのもう 1 つの層は、VPN Gateway サービスを使用して実現されます。 ゲートウェイは、HDInsight クラスターへのすべての受信要求に対する防御の最前線として機能します。 ゲートウェイでは要求を受け取り、検証して、クラスター内の他のノードに渡す要求のみを許可します。 この方法では、ゲートウェイは境界セキュリティをクラスター内の他のノードとデータ ノードに提供します。

## <a name="authentication"></a>Authentication
エンタープライズ管理者は、ESP を使用する HDInsight クラスターを[仮想ネットワーク](https://azure.microsoft.com/services/virtual-network/)に作成できます。 HDInsight クラスターのノードはすべて、企業が管理するドメインに参加することになります。 これは、[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) を使用して実現されます。 

この設定により、企業の従業員は、ドメイン資格情報を使用してクラスター ノードにサインインできます。 また、ドメイン資格情報を使用して、クラスターと対話する他の承認済みエンドポイント (Apache Ambari Views、ODBC、JDBC、PowerShell、REST API など) で認証することもできます。 管理者は、これらのエンドポイントを使用してクラスターを操作するユーザーの数の制限を完全に制御できます。

## <a name="authorization"></a>Authorization
ほとんどの企業では、すべての従業員がすべてのエンタープライズ リソースにはアクセスできないようにするベスト プラクティスに従っています。 同様に、管理者はクラスター リソースのロールベースのアクセス制御ポリシーを定義できます。 

たとえば、管理者は [Apache Ranger](https://hortonworks.com/apache/ranger/) を構成して Hive のアクセス制御ポリシーを設定できます。 この機能により、従業員は仕事を順調に進めるために必要な量のデータにのみアクセスできます。 クラスターへの SSH アクセスも管理者だけに制限されます。

## <a name="auditing"></a>監査
リソースにおいて許可されていないアクセスや意図しないアクセスを追跡するには、クラスター リソース、およびデータへのアクセスをすべて監査する必要があります。 HDInsight クラスター リソースを許可されていないユーザーから保護し、データをセキュリティで保護することが重要です。 

管理者は HDInsight クラスター リソースとデータへのすべてのアクセスを表示し、レポートを作成できます。 また、管理者は Apache Ranger のサポートされているエンドポイントで作成されたアクセス制御ポリシーのすべての変更を表示し、レポートを作成することもできます。 

ESP を使用する HDInsight クラスターでは、使い慣れた Apache Ranger UI を使用して監査ログを検索します。 バックエンドでは、Ranger はログの保存と検索に [Apache Solr](https://hortonworks.com/apache/solr/) を使用します。

## <a name="encryption"></a>暗号化
データの保護は、組織のセキュリティとコンプライアンス要件を満たすために重要です。 許可されていない従業員からデータへのアクセスを制限すると共に、暗号化する必要があります。 

HDInsight クラスターのデータ ストア (Azure BLOB ストレージおよび Azure Data Lake Storage Gen1/Gen2) はどちらも、保存データの透過的なサーバー側[暗号化](../../storage/common/storage-service-encryption.md)をサポートしています。 セキュリティで保護された HDInsight クラスターは、この保存データのサーバー側暗号化の機能とシームレスに連携します。

## <a name="next-steps"></a>次の手順

* [ESP を使用する HDInsight クラスターを計画する](apache-domain-joined-architecture.md)
* [ESP を使用する HDInsight クラスターを構成する](apache-domain-joined-configure.md)
* [ESP を使用する HDInsight クラスターを管理する](apache-domain-joined-manage.md)
* [ESP を使用する HDInsight クラスターのために Apache Hive ポリシーを構成する](apache-domain-joined-run-hive.md)
* [HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

