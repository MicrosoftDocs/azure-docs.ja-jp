---
title: Enterprise セキュリティ パッケージ クラスターを管理する - Azure HDInsight
description: Enterprise セキュリティ パッケージを使用して Azure HDInsight クラスターを管理する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: bc31c3d71590a6b8c0b324ffcb8c10129a9f8699
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863243"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを使用して HDInsight クラスターを管理する

HDInsight Enterprise セキュリティ パッケージ (ESP) でのユーザーとロール、および ESP クラスターを管理する方法について説明します。

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>VSCode を使用してドメイン参加済みクラスターにリンクする

Apache Ambari マネージド ユーザー名を使用して、通常のクラスターをリンクできます。また、ドメイン ユーザー名 (`user1@contoso.com` など) を使用して、セキュリティ保護された Apache Hadoop クラスターをリンクすることもできます。

1. [Visual Studio Code](https://code.visualstudio.com/) を開きます。 [Spark & Hive Tools](../hdinsight-for-vscode.md) 拡張機能がインストールされていることを確認します。

1. Visual Studio Code の「[クラスターのリンク](../hdinsight-for-vscode.md#link-a-cluster)」の手順に従ってください。

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>IntelliJ を使用してドメイン参加済みクラスターにリンクする

Ambari 管理対象ユーザー名を使用することで、ノーマル クラスターをリンクできます。また、ドメイン ユーザー名 (`user1@contoso.com` など) を使用することで、セキュリティ Hadoop クラスターをリンクすることもできます。

1. IntelliJ IDEA を開きます。 すべての[前提条件](../spark/apache-spark-intellij-tool-plugin.md#prerequisites)が満たされていることを確認します。

1. IntelliJ の「[クラスターのリンク](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster)」の手順に従ってください。

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Eclipse を使用してドメイン参加済みクラスターにリンクする

Ambari 管理対象ユーザー名を使用することで、ノーマル クラスターをリンクできます。また、ドメイン ユーザー名 (`user1@contoso.com` など) を使用することで、セキュリティ Hadoop クラスターをリンクすることもできます。

1. Eclipse を開きます。 すべての[前提条件](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites)が満たされていることを確認します。

1. Eclipse の「[クラスターのリンク](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster)」の手順に従ってください。

## <a name="access-the-clusters-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを含むクラスターへのアクセス

Enterprise セキュリティ パッケージ (旧称 HDInsight Premium) は、クラスターに対する複数ユーザーのアクセスを提供します。アクセスの際の認証は Active Directory によって行なわれ、承認は Apache Ranger と Storage ACL (ADLS ACL) によって行なわれます。 承認は、複数ユーザー間のセキュリティで保護された境界を提供し、承認ポリシーに基づく特権を持つユーザーのみがデータにアクセスすることを許可します。

セキュリティとユーザーの分離は、Enterprise セキュリティ パッケージがある HDInsight クラスターでは重要です。 これらの要件を満たすために、クラスターの作成時に選択されたローカル ユーザーとAAD DS (つまり Kerberos) で使用可能なユーザーに対しては、Enterprise セキュリティ パッケージがあるクラスターへの SSH アクセスがサポートされます。 次の表は、各クラスターの種類に対して推奨されるアクセス方法を示しています。

|ワークロード|シナリオ|アクセス方法|
|--------|--------|-------------|
|Apache Hadoop|Hive – 対話型ジョブ/クエリ    |<ul><li>[Beeline](#beeline)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|対話型ジョブ/クエリ、PySpark 対話型|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin + Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|バッチ シナリオ – spark-submit、PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|カスタム アプリケーションのインストール|<ul><li>[スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter は、Enterprise セキュリティ パッケージではインストールもサポートも行われません。

標準 API の使用は、セキュリティの観点から有用です。 また、次の利点も得られます。

- **管理** – 標準 API を使用して、コードの管理とジョブの自動化を実行できます (Livy や HS2 など)。
- **監査** – SSH では、どのユーザーがクラスターに SSH で接続したかを監査する方法はありません。 これは、ジョブがユーザーのコンテキストで実行されるため、標準エンドポイント経由で構築される場合には該当しません。

### <a name="use-beeline"></a><a name="beeline"></a>Beeline を使用する

Beeline をローカルにインストールし、次のパラメーターを使用してパブリック インターネット経由で接続します。

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Beeline をローカルにインストールしている場合に Azure 仮想ネットワーク経由で接続するときは、次のパラメーターを使用します。

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

ヘッドノードの完全修飾ドメイン名を検索するには、Ambari REST API を使用した HDInsight の管理に関するドキュメントの情報を使用してください。

## <a name="users-of-hdinsight-clusters-with-esp"></a>ESP を使用する HDInsight クラスターのユーザー

ESP を使用していない HDInsight クラスターには、クラスターの作成中に作成される、次の 2 つのユーザー アカウントがあります。

- **Ambari 管理者**: このアカウントは、"*Hadoop ユーザー*" または "*HTTP ユーザー*" とも呼ばれます。 このアカウントを使用すると、`https://CLUSTERNAME.azurehdinsight.net` で Ambari にサインインできます。 また、Ambari ビューに対するクエリの実行、外部ツール (PowerShell、Templeton、Visual Studio など) によるジョブの実行、Hive ODBC ドライバーと BI ツール (Excel、Power BI、Tableau など) による認証にも使用できます。

ESP を使用している HDInsight クラスターには、Ambari 管理者以外に、3 つの新しいユーザーの種類があります。

- **Ranger 管理者**: このアカウントは、ローカルの Apache Ranger 管理者アカウントです。 これは Active Directory ドメイン ユーザーではありません。 このアカウントは、ポリシーのセットアップや、他のユーザー管理者または代理管理者の作成に使用することができます (それらの管理者がポリシーを管理できるようにするため)。 既定では、ユーザー名は *admin* で、パスワードは Ambari 管理者パスワードと同じです。 パスワードは、Ranger の [Settings (設定)] ページで更新することができます。

- **クラスター管理者ドメイン ユーザー**: このアカウントは、Ambari と Ranger を含む Hadoop クラスター管理者として指定された Active Directory ドメイン ユーザーです。 クラスターの作成中に、このユーザーの資格情報を指定する必要があります。 このユーザーは、次の特権を持ちます。
    - コンピューターをドメインに参加させ、クラスターの作成中に指定する OU 内に配置します。
    - クラスターの作成中に指定する OU 内にサービス プリンシパルを作成します。
    - 逆引き DNS エントリを作成します。

    他の AD ユーザーもこれらの特権を持つことに注意してください。

    クラスター内には、Ranger によって管理されていないために安全ではないエンドポイント (Templeton など) がいくつか存在します。 これらのエンドポイントは、クラスター管理者ドメイン ユーザーを除くすべてのユーザーに対してロックダウンされます。

- **レギュラー**: クラスターの作成中に、複数の Active Directory グループを指定することができます。 これらのグループのユーザーは、Ranger と Ambari に同期されます。 これらのユーザーはドメイン ユーザーであり、Ranger によって管理されているエンドポイント (たとえば Hiveserver2) のみにアクセスできます。 すべての RBAC ポリシーと監査は、これらのユーザーに適用できます。

## <a name="roles-of-hdinsight-clusters-with-esp"></a>ESP を使用する HDInsight クラスターのロール

HDInsight Enterprise セキュリティ パッケージには次のロールがあります。

- クラスター管理者
- クラスター オペレーター
- サービス管理者
- サービス オペレーター
- クラスター ユーザー

**これらのロールのアクセス許可を確認するには**

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューから、 **[ロール]** を選択します。
3. 青い疑問符を選択して、アクセス許可を表示します。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png" alt-text="ESP HDInsight のロールのアクセス許可" border="true":::

## <a name="open-the-ambari-management-ui"></a>Ambari Management UI を開く

1. `https://CLUSTERNAME.azurehdinsight.net/` に移動します。ここで、CLUSTERNAME はクラスターの名前です。
1. クラスター管理者のドメイン ユーザー名とパスワードを使用して、Ambari にサインインします。
1. 右上隅から **[管理者]** ドロップダウン メニューを選択し、 **[Ambari の管理]** を選択します。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png" alt-text="ESP HDInsight の Apache Ambari の管理" border="true":::

    次のような UI になります。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png" alt-text="ESP HDInsight Apache Ambari Management UI" border="true":::

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン ユーザーの一覧表示

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューから、 **[ユーザー]** を選択します。 Active Directory から HDInsight クラスターに同期されているすべてのユーザーが表示されます。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png" alt-text="ESP HDInsight での Ambari Management UI によるユーザーの一覧表示" border="true":::

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Active Directory から同期されているドメイン グループの一覧表示

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューから、 **[グループ]** を選択します。 Active Directory から HDInsight クラスターに同期されているすべてのグループが表示されます。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png" alt-text="ESP HDInsight での Ambari Management UI によるグループの一覧表示" border="true":::

## <a name="configure-hive-views-permissions"></a>Hive ビューのアクセス許可の構成

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューから、 **[ビュー]** を選択します。
3. **[HIVE]** を選択して、詳細を表示します。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png" alt-text="ESP HDInsight での Ambari Management UI による Hive ビュー" border="true":::

4. **[Hive ビュー]** リンクを選択して、Hive ビューを構成します。
5. **[Permissions (アクセス許可)]** セクションまで下へスクロールします。

    :::image type="content" source="./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png" alt-text="ESP HDInsight での Ambari Management UI による Hive ビューのアクセス許可の構成" border="true":::

6. **[ユーザーの追加]** または **[グループの追加]** を選択し、Hive ビューを使用できるユーザーまたはグループを指定します。

## <a name="configure-users-for-the-roles"></a>ロールのユーザーの構成

 ロールとそのアクセス許可の一覧を表示するには、「ESP を使用する HDInsight クラスターのロール」を参照してください。

1. Ambari Management UI を開きます。  「[Ambari Management UI を開く](#open-the-ambari-management-ui)」を参照してください。
2. 左側のメニューから、 **[ロール]** を選択します。
3. **[ユーザーの追加]** または **[グループの追加]** を選択して、ユーザーやグループを異なるロールに割り当てます。

## <a name="next-steps"></a>次のステップ

- Enterprise セキュリティ パッケージを使用した HDInsight クラスターの構成については、[ESP での HDInsight クラスターの構成](./apache-domain-joined-configure-using-azure-adds.md)に関するページをご覧ください。
- Hive ポリシーの構成と Hive クエリの実行については、[ESP を使用する HDInsight クラスター用の Apache Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関するページを参照してください。