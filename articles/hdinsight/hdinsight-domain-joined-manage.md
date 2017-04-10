---
title: "ドメイン参加済み HDInsight クラスターの管理 | Microsoft Docs"
description: "ドメイン参加済み HDInsight クラスターを管理する方法について説明します"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: e2b2e8d6110062efb99201f4daac1e1295d9b904
ms.lasthandoff: 03/25/2017


---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>ドメイン参加済み HDInsight クラスターの管理 (プレビュー)
ドメイン参加済み HDInsight のユーザーとロールについて説明し、ドメイン参加済み HDInsight クラスターの管理方法についても説明します。

## <a name="users-of-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターのユーザー
ドメインに参加していない HDInsight クラスターには、クラスターの作成中に作成される、次の 2 つのユーザー アカウントがあります。

* **Ambari 管理者**: このアカウントは、"*Hadoop ユーザー*" または "*HTTP ユーザー*" とも呼ばれます。 このアカウントは、Ambari (https://&lt;clustername>.azurehdinsight.net) にログオンするために使用できます。 また、Ambari ビューに対するクエリの実行、外部ツール (PowerShell、Templeton、Visual Studio など) によるジョブの実行、Hive ODBC ドライバーと BI ツール (Excel、PowerBI、Tableau など) による認証にも使用できます。
* **SSH ユーザー**: このアカウントは、SSH と共に使用し、sudo コマンドを実行することができます。 Linux VM に対するルート特権があります。

ドメイン参加済み HDInsight クラスターには、Ambari 管理者と SSH ユーザー以外に、3 つの新しいユーザーの種類があります。

* **Ranger 管理者**: このアカウントは、ローカルの Apache Ranger 管理者アカウントです。 これは、Active Directory ドメイン ユーザーではありません。 このアカウントは、ポリシーのセットアップや、他のユーザー管理者または代理管理者の作成に使用することができます (それらの管理者がポリシーを管理できるようにするため)。 既定では、ユーザー名は *admin* で、パスワードは Ambari 管理者パスワードと同じです。 パスワードは、Ranger の [Settings (設定)] ページで更新することができます。
* **クラスター管理者ドメイン ユーザー**: このアカウントは、Ambari と Ranger を含む Hadoop クラスター管理者として指定された Active Directory ドメイン ユーザーです。 クラスターの作成中に、このユーザーの資格情報を指定する必要があります。 このユーザーは、次の特権を持ちます。

  * コンピューターをドメインに参加させ、クラスターの作成中に指定する OU 内に配置します。
  * クラスターの作成中に指定する OU 内にサービス プリンシパルを作成します。
  * 逆引き DNS エントリを作成します。

    他の AD ユーザーもこれらの特権を持つことに注意してください。

    クラスター内には、Ranger によって管理されないエンドポイント (たとえば Templeton) がいくつかあります。そのため、それらは安全ではありません。 これらのエンドポイントは、クラスター管理者ドメイン ユーザーを除くすべてのユーザーに対してロックダウンされます。
* **レギュラー**: クラスターの作成中に、複数の Active Directory グループを指定することができます。 これらのグループのユーザーは、Ranger と Ambari に同期されます。 これらのユーザーはドメイン ユーザーであり、Ranger によって管理されているエンドポイント (たとえば Hiveserver2) のみにアクセスできます。 すべての RBAC ポリシーと監査は、これらのユーザーに適用できます。

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>ドメイン参加済み HDInsight クラスターのロール
ドメイン参加済み HDInsight には、次のロールがあります。

* クラスター管理者
* クラスター オペレーター
* サービス管理者
* サービス オペレーター
* クラスター ユーザー

**これらのロールのアクセス許可を確認するには**

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Roles (ロール)]** をクリックします。
3. 青い疑問符をクリックして、アクセス許可を表示します。

    ![ドメイン参加済み HDInsight のロールのアクセス許可](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ambari Management UI を開く
1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. ブレードで HDInsight クラスターを開きます。 「[クラスターの一覧と表示](hdinsight-administer-use-management-portal.md#list-and-show-clusters)」を参照してください。
3. 上部のメニューで **[ダッシュボード]** をクリックして、Ambari を開きます。
4. クラスター管理者のドメイン ユーザー名とパスワードを使用して、Ambari にログオンします。
5. 右上隅のドロップダウン メニューで **[Admin (管理者)]** をクリックし、**[Manage Ambari (Ambari の管理)]** をクリックします。

    ![ドメイン参加済み HDInsight での Ambari の管理](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    次のような UI になります。

    ![ドメイン参加済み HDInsight での Ambari Management UI](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン ユーザーの一覧表示
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[ユーザー]**をクリックします。 Active Directory から HDInsight クラスターに同期されているすべてのユーザーが表示されます。

    ![ドメイン参加済み HDInsight での Ambari Management UI によるユーザーの一覧表示](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン グループの一覧表示
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Groups (グループ)]** をクリックします。 Active Directory から HDInsight クラスターに同期されているすべてのグループが表示されます。

    ![ドメイン参加済み HDInsight での Ambari Management UI によるグループの一覧表示](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Hive ビューのアクセス許可の構成
1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Views (ビュー)]** をクリックします。
3. **[HIVE]** をクリックして、詳細を表示します。

    ![ドメイン参加済み HDInsight での Ambari Management UI による Hive ビュー](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. **[Hive View (Hive ビュー)]** リンクをクリックして、Hive ビューを構成します。
5. **[Permissions (アクセス許可)]** セクションまで下へスクロールします。

    ![ドメイン参加済み HDInsight での Ambari Management UI による Hive ビューのアクセス許可の構成](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. **[Add User (ユーザーの追加)]** または **[Add Group (グループの追加)]** をクリックし、Hive ビューを使用するユーザーまたはグループを指定します。

## <a name="configure-users-for-the-roles"></a>ロールのユーザーの構成
 ロールとそのアクセス許可の一覧を表示するには、「[ドメイン参加済み HDInsight クラスターのロール](#roles-of-domain---joined-hdinsight-clusters)」を参照してください。

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューで **[Roles (ロール)]** をクリックします。
3. **[Add User (ユーザーの追加)]** または **[Add Group (グループの追加)]** をクリックし、別のロールにユーザーやグループを割り当てます。

## <a name="next-steps"></a>次のステップ
* ドメイン参加済み HDInsight クラスターの構成については、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関する記事を参照してください。
* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](hdinsight-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行する方法については、[HDInsight で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)に関するページをご覧ください。

