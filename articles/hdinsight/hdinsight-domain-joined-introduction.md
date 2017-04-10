---
title: "Hadoop セキュリティ - ドメイン参加済み HDInsight クラスター - Azure | Microsoft Docs"
description: "詳細...."
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
ms.date: 10/31/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21078a82e979af251a08bca70d623c80949d064a
ms.lasthandoff: 03/25/2017


---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters-preview"></a>ドメイン参加済み HDInsight クラスターでの Hadoop セキュリティの概要 (プレビュー)

Azure HDInsight では、これまで単一ユーザーのローカル管理者のみをサポートしていました。 これは、小規模なアプリケーション チームや部門に適していました。 Hadoop ベースのワークロードがエンタープライズ部門に普及するにつれて、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御などのエンタープライズ レベルの機能の必要性がますます重要になってきました。 ドメイン参加済み HDInsight クラスターを使用すると、Active Directory ドメインに参加している HDInsight クラスターを作成し、Azure Active Directory で認証して HDInsight クラスターにログオンできる企業の従業員のリストを構成できます。 社外のユーザーは、HDInsight クラスターにログオンすることもアクセスすることもできません。 エンタープライズ管理者は、[Apache Ranger](http://hortonworks.com/apache/ranger/) を使用して Hive のセキュリティを確保するためのロールベースのアクセス制御を構成できるため、データへのアクセスを必要な分だけに制限できます。 また、管理者は、従業員によるデータ アクセスとアクセス制御ポリシーに対して行われた変更を監査できるため、企業リソースの高度なガバナンスを実現できます。

> [!NOTE]
> このプレビューで説明する新機能は、Hive ワークロードに対応する Linux ベースの HDInsight クラスターでのみ使用できます。 HBase、Spark、Storm、Kafka など、他のワークロードには今後のリリースで対応する予定です。
>
>

## <a name="benefits"></a>メリット
エンタープライズ セキュリティには、境界セキュリティ、認証、承認、暗号化の 4 つの大きな柱があります。

![ドメイン参加済み HDInsight クラスターのメリットの柱](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png)に関するページを参照してください。

### <a name="perimeter-security"></a>境界セキュリティ
HDInsight の境界セキュリティは、仮想ネットワークとゲートウェイ サービスを使用して実現されます。 現在、エンタープライズ管理者は、仮想ネットワーク内に HDInsight クラスターを作成し、ネットワーク セキュリティ グループ (受信または送信ファイアウォール規則) を使用して仮想ネットワークへのアクセスを制限できます。 HDInsight クラスターと通信できるのは、受信ファイアウォール規則で定義されている IP アドレスだけであるため、境界セキュリティが提供されます。 境界セキュリティのもう 1 つの層は、ゲートウェイ サービスを使用して実現されます。 ゲートウェイは、HDInsight クラスターへのすべての受信要求に対する防御の最前線として機能するサービスです。 ゲートウェイは、受け入れた要求の検証が終わるまで、その要求をクラスターの他のノードに渡すことを許可しないため、クラスターの他の名前ノードやデータ ノードの境界セキュリティが実現されます。

### <a name="authentication"></a>認証
このパブリック プレビューでは、エンタープライズ管理者は、ドメイン参加済み HDInsight クラスターを[仮想ネットワーク](https://azure.microsoft.com/services/virtual-network/)にプロビジョニングできます。 HDInsight クラスターの各ノードは、企業が管理するドメインに参加することになります。 これは、[Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) を使用して実現されます。 クラスター内のすべてのノードは、企業が管理するドメインに参加しています。 この設定により、企業の従業員はドメイン資格情報を使用してクラスター ノードにログオンできます。 また、ドメイン資格情報を使用して、クラスターと対話する他の承認済みエンドポイント (Hue、Ambari Views、ODBC、JDBC、PowerShell、REST API など) で認証を行うこともできます。 管理者は、これらのエンドポイントを使用してクラスターを操作するユーザーの数の制限を完全に制御できます。

### <a name="authorization"></a>承認
ほとんどの企業では、すべての従業員がすべてのエンタープライズ リソースにアクセスできるわけではないというベスト プラクティスに従っています。 同様に、このリリースでは、管理者はクラスター リソースのロールベースのアクセス制御ポリシーを定義できます。 たとえば、管理者は [Apache Ranger](http://hortonworks.com/apache/ranger/) を構成して Hive のアクセス制御ポリシーを設定できます。 この機能により、従業員は仕事を順調に進めるために必要な量のデータにのみアクセスできるようになります。 クラスターへの SSH アクセスも管理者だけに制限されます。

### <a name="auditing"></a>監査
HDInsight クラスター リソースを未承認のユーザーから保護し、データをセキュリティで保護すると同時に、リソースへの未承認のアクセスや誤ったアクセスを追跡するために、クラスター リソースとデータへのすべてのアクセスを監査する必要があります。 このプレビューでは、管理者は HDInsight クラスター リソースとデータへのすべてのアクセスを表示し、レポートを作成できます。 また、管理者は Apache Ranger のサポートされているエンドポイントで行われたアクセス制御ポリシーのすべての変更を表示し、レポートを作成することもできます。 ドメイン参加済み HDInsight クラスターでは、使い慣れた Apache Ranger UI を使用して監査ログを検索します。 バックエンドでは、Ranger はログの保存と検索に [Apache Solr](http://hortonworks.com/apache/solr/) を使用します。

### <a name="encryption"></a>Encryption
データの保護は、組織のセキュリティとコンプライアンスの要件を満たすうえで重要です。未承認の従業員によるデータへのアクセスを制限すると同時に、暗号化によってデータを保護する必要があります。 HDInsight クラスター、Azure Storage BLOB、Azure Data Lake Storage のデータ ストアは、保存データの透過的なサーバー側[暗号化](../storage/storage-service-encryption.md)をサポートしています。 セキュリティで保護された HDInsight クラスターは、この保存データのサーバー側暗号化機能とシームレスに連携します。

## <a name="next-steps"></a>次のステップ
* ドメイン参加済み HDInsight クラスターの構成については、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関する記事をご覧ください。
* ドメイン参加済み HDInsight クラスターの管理については、[ドメイン参加済み HDInsight クラスターの管理](hdinsight-domain-joined-manage.md)に関する記事をご覧ください。
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](hdinsight-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行する方法については、[HDInsight で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)に関するページをご覧ください。

