---
title: Ambari Views のユーザー承認 - Azure HDInsight
description: ESP が有効になっている HDInsight クラスターに対する Ambari ユーザーと Ambari グループのアクセス許可を管理する方法。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 28f30270ab0a6c057ee583ccebc2a8540980c6cc
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442183"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Apache Ambari ビューに対してユーザーを承認する

[Enterprise セキュリティ パッケージ (ESP) が有効になっている HDInsight クラスター](./domain-joined/hdinsight-security-overview.md)には、エンタープライズ グレードの機能が備わっています。Azure Active Directory ベースの認証もその 1 つです。 クラスターへのアクセスが提供されている Azure AD グループに追加された[新しいユーザーを同期](hdinsight-sync-aad-users-to-cluster.md)して、特定のユーザーに特定のアクションの実行を許可できます。 [Apache Ambari](https://ambari.apache.org/) でのユーザー、グループ、およびアクセス許可の操作は、ESP HDInsight クラスターと標準 HDInsight クラスターの両方でサポートされています。

Active Directory ユーザーは、自分のドメイン資格情報を使用してクラスター ノードにサインインできます。 また、クラスターと他の承認済みエンドポイント ([Hue](https://gethue.com/)、Ambari Views、ODBC、JDBC、PowerShell、REST API など) との対話も、ドメイン資格情報で認証することができます。

> [!WARNING]  
> Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ (hdinsightwatchdog) のパスワードは変更しないでください。 パスワードを変更すると、スクリプト アクションを使用したり、クラスターでスケール操作を実行する能力が損なわれます。

新しい ESP クラスターをまだプロビジョニングしていない場合は、[こちらの手順](./domain-joined/apache-domain-joined-configure.md)に従ってプロビジョニングしてください。

## <a name="access-the-ambari-management-page"></a>Ambari 管理ページにアクセスする

[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) の **Ambari 管理ページ**にアクセスするには、ブラウザーで **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** にアクセスします。 クラスターの作成時に定義したクラスタ アドミニストレーターのユーザー名とパスワードを入力します。 次に、Ambari のダッシュボードで **[admin]\(管理\)** メニューの **[Manage Ambari]\(Ambari の管理\)** を選択します。

![Ambari の管理](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Apache Hive ビューへのアクセス許可を付与する

Ambari には、[Apache Hive](https://hive.apache.org/) や [Apache TEZ](https://tez.apache.org/) のビュー インスタンスが備わっています。 Hive ビュー インスタンスへのアクセス権を付与するには、**Ambari 管理ページ**に移動します。

1. 管理ページの左側の **[Views]\(ビュー\)** メニュー ヘッダーにある **[Views]\(ビュー\)** リンクを選択します。

    ![[Views]\(ビュー\) リンク](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. [Views]\(ビュー\) ページの **HIVE** 行を展開します。 Hive サービスをクラスターに追加するときに、既定の Hive ビューが 1 つ作成されます。 必要に応じて Hive ビュー インスタンスをさらに作成することもできます。 Hive ビューを選択します。

    ![[Views]\(ビュー\) - Hive ビュー](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. ビュー ページを下の方へスクロールします。 *[Permissions]\(アクセス許可\)* セクションには、ビューのアクセス許可をドメイン ユーザーに許可するためのオプションが 2 つあります。

**[Grant permission to these users]\(次のユーザーにアクセス許可を付与\)** ![[Grant permission to these users]\(次のユーザーにアクセス許可を付与\)](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**[Grant permission to these groups]\(次のグループにアクセス許可を付与\)** ![[Grant permission to these groups]\(次のグループにアクセス許可を付与\)](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. ユーザーを追加するには、 **[Add User]\(ユーザーの追加\)** ボタンを選択します。

   * ユーザー名を入力し始めると、既に定義されている名前がドロップダウン リストに表示されます。

     ![ユーザーのオートコンプリート](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * ユーザー名を選択するか、最後まで入力します。 このユーザー名を新しいユーザーとして追加するには、 **[New]\(新規\)** ボタンを選択します。

   * 変更を保存するには、**青色のチェック ボックス**をオンにします。

     ![入力されたユーザー](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. グループを追加するには、 **[Add Group]\(グループの追加\)** ボタンを選択します。

   * グループ名の入力を開始します。 既存のグループ名を選択 (または新しいグループを追加) するプロセスは、ユーザーを追加するときと同じです。
   * 変更を保存するには、**青色のチェック ボックス**をオンにします。

     ![入力されたグループ](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

ビューのアクセス許可をユーザーに割り当てるとき、余分なアクセス許可があるグループのメンバーにすることを望まない場合、ビューに直接ユーザーを追加する方法が便利です。 管理オーバーヘッドを減らすには、おそらくグループにアクセス許可を割り当てる方が簡単です。

## <a name="grant-permissions-to-apache-tez-views"></a>Apache TEZ ビューへのアクセス許可を付与する

[Apache Hive](https://hive.apache.org/) クエリや [Apache Pig](https://pig.apache.org/) スクリプトによって送信されたすべての Tez ジョブは、[Apache TEZ](https://tez.apache.org/) ビューのインスタンスを使用して監視したりデバッグしたりすることができます。 クラスターのプロビジョニング時に、既定の Tez ビュー インスタンスが 1 つ作成されます。

Tez ビュー インスタンスにユーザーとグループを割り当てるには、前述したように、[Views]\(ビュー\) ページの **TEZ** 行を展開します。

![[Views]\(ビュー\) - Tez ビュー](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

ユーザーまたはグループを追加するには、前のセクションの手順 3. ～ 5. を繰り返します。

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる

ユーザーとグループには 5 つのセキュリティ ロールがあります。以下、それらをアクセス権の高い順に示します。

* クラスター管理者
* クラスター オペレーター
* サービス管理者
* サービス オペレーター
* クラスター ユーザー

ロールを管理するには、**Ambari 管理ページ**に移動し、左側の *[Clusters]\(クラスター\)* メニュー グループにある **[Roles]\(ロール\)** リンクを選択します。

![[Roles]\(ロール\) メニュー リンク](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

それぞれのロールに付与されているアクセス許可の一覧については、[Roles]\(ロール\) ページの **[Roles]\(ロール\)** テーブル ヘッダーの横にある青色の疑問符をクリックしてください。

![[Roles]\(ロール\) メニュー リンク](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

このページには、ユーザーとグループのロールを管理するための 2 種類のビュー (ブロック ビューとリスト ビュー) が用意されています。

### <a name="block-view"></a>ブロック ビュー

ブロック ビューには、各ロールが行ごとに表示されるほか、前述したような **[Assign roles to these users]\(次のユーザーにロールを割り当て\)** オプションと **[Assign roles to these groups]\(次のグループにロールを割り当て\)** オプションが表示されます。

![ロールのブロック ビュー](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>リスト ビュー

リスト ビューには、ユーザーとグループという 2 つのカテゴリの簡単な編集機能が用意されています。

* リスト ビューの [Users]\(ユーザー\) カテゴリには、すべてのユーザーが一覧表示され、各ユーザーのロールをドロップダウン リストで選択することができます。

    ![ロールのリスト ビュー - ユーザー](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  リスト ビューの [Groups]\(グループ\) カテゴリには、すべてのグループと、各グループに割り当てられているロールが表示されます。 この例に示したグループのリストは、クラスターのドメイン設定の **[Access user group]\(アクセス ユーザー グループ\)** プロパティに指定された Azure AD グループから同期されています。 [ESP が有効になっている HDInsight クラスターの作成](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)に関するページを参照してください。

    ![ロールのリスト ビュー - グループ](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    上の画像では、"hiveusers" グループに "*クラスター ユーザー*" ロールが割り当てられています。 これは読み取り専用のロールで、このグループのユーザーは、サービスの構成とクラスターのメトリックを表示することはできますが、変更することはできません。

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Ambari に読み取り専用ユーザーとしてログインする

Azure AD ドメイン ユーザー "hiveuser1" には、Hive ビューと Tez ビューに対するアクセス許可を割り当ててあります。 Ambari Web UI を起動してこのユーザーのドメイン資格情報 (電子メール形式の Azure AD ユーザー名とパスワード) を入力すると、Ambari Views ページにユーザーがリダイレクトされます。 そこから、アクセス可能な任意のビューを選択することができます。 このユーザーが、このサイトの他の領域 (ダッシュボード、サービス、ホスト、アラート、管理ページなど) にアクセスすることはできません。

![読み取り専用ユーザー](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Ambari にクラスター ユーザーとしてログインする

"*クラスター ユーザー*" ロールには、Azure AD ドメイン ユーザー "hiveuser2" を割り当ててあります。 このロールは、ダッシュボードとすべてのメニュー項目にアクセスすることができます。 クラスター ユーザーは、選択できるオプションが管理者と比べて少なくなります。 たとえば hiveuser2 は、各サービスの構成を表示することはできますが、編集することはできません。

![クラスター ユーザー ロールを付与されたユーザー](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>次の手順

* [ESP HDInsight での Apache Hive ポリシーの構成](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight クラスターの管理](./domain-joined/apache-domain-joined-manage.md)
* [HDInsight 上の Apache Hadoop で Apache Hive ビューを使用する](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD ユーザーをクラスターに同期する](hdinsight-sync-aad-users-to-cluster.md)
