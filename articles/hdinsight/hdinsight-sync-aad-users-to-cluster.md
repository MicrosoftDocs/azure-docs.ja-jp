---
title: Azure Active Directory ユーザーを HDInsight クラスターに同期する
description: Azure Active Directory の認証されたユーザーを HDInsight クラスターに同期します。
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 2a753a33e9ddf16cc277ab10c1f91049a6382066
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871947"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory ユーザーを HDInsight クラスターに同期する

[Enterprise セキュリティ パッケージ (ESP) を使用する HDInsight クラスター](./domain-joined/hdinsight-security-overview.md)は、Azure Active Directory (Azure AD) ユーザーに強力な認証を使用したり、"*Azure ロールベースのアクセス制御 (Azure RBAC)* " ポリシーを使用したりすることができます。 Azure AD にユーザーやグループを追加するとき、アクセスが必要なユーザーをクラスターに同期できます。

## <a name="prerequisites"></a>前提条件

まだそのようにしていない場合は、[Enterprise セキュリティ パッケージを使用する HDInsight クラスターを作成します](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)。

## <a name="add-new-azure-ad-users"></a>新しい Azure AD ユーザーを追加する

ホストを表示するには、Ambari Web UI を開きます。 各ノードが、新しい無人アップグレードの設定で更新されます。

1. [Azure Portal](https://portal.azure.com) から、ESP クラスターに関連付けられた Azure AD ディレクトリに移動します。

2. 左側のメニューから **[すべてのユーザー]** を選択してから、 **[New user] (新しいユーザー)** を選択します。

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png" alt-text="Azure portal のすべてのユーザーとグループ":::

3. 新しいユーザーのフォームを完了します。 クラスター ベースのアクセス許可を割り当てるために作成したグループを選択します。 この例では、新しいユーザーを割り当てることのできる "HiveUsers" という名前のグループを作成します。 ESP クラスターを作成するための[手順の例](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)には、`HiveUsers` と `AAD DC Administrators` という 2 つのグループの追加が含まれています。

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png" alt-text="Azure portal ユーザー ウィンドウのグループの選択":::

4. **作成** を選択します。

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Apache Ambari REST API を使用してユーザーを同期する

クラスターの作成プロセス中に指定されたユーザー グループは、その時点で同期されます。 ユーザーの同期は、1 時間に 1 回自動的に実行されます。 ユーザーを直ちに同期するには、またはクラスターの作成中に指定されたグループ以外のグループを同期するには、Ambari REST API を使用します。

次のメソッドは、Ambari REST API で POST を使用します。 詳細については、「[Ambari REST API を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari-rest-api.md)」を参照してください。

1. [ssh コマンド](hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して `CLUSTERNAME` をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 認証の後、次のコマンドを入力します。

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    応答は次のようになります。

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. 同期の状態を表示するには、新しい `curl` コマンドを実行します。

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    応答は次のようになります。

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. この結果は、状態が **[完了]** であり、新しいユーザーが 1 人作成され、そのユーザーにメンバーシップが割り当てられたことを示しています。 この例では、ユーザーが Azure AD 内の同じグループに追加されたため、ユーザーは "HiveUsers" 同期済み LDAP グループに割り当てられます。

    > [!NOTE]  
    > 上記の方法では、クラスターの作成時にドメイン設定の **アクセス ユーザー グループ** プロパティに指定されている Azure AD グループのみが同期されます。 詳細については、「[create an HDInsight cluster (HDInsight クラスターを作成する)](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)」を参照してください。

## <a name="verify-the-newly-added-azure-ad-user"></a>新しく追加された Azure AD ユーザーを確認する

新しい Azure AD ユーザーが追加されたことを確認するには、[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) を開きます。 **`https://CLUSTERNAME.azurehdinsight.net`** を参照することによって、Ambari Web UI にアクセスします。 クラスター管理者のユーザー名とパスワードを入力します。

1. Ambari ダッシュボードから、**[管理者]** メニューの下にある **[Ambari の管理]** を選択します。

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png" alt-text="Apache Ambari ダッシュボードの Ambari の管理":::

2. ページの左側の **[User + Group Management] (ユーザーとグループの管理)** メニュー グループの下にある **[ユーザー]** を選択します。

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png" alt-text="HDInsight の [ユーザーとグループ] メニュー":::

3. 新しいユーザーが [ユーザー] テーブル内に表示されます。 [種類] は `Local` ではなく、`LDAP` に設定されています。

    :::image type="content" source="./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png" alt-text="HDInsight aad ユーザー ページの概要":::

## <a name="log-in-to-ambari-as-the-new-user"></a>新しいユーザーとして Ambari にログインする

新しいユーザー (またはその他の任意のドメイン ユーザー) が Ambari にログインしたとき、そのユーザーは自身の完全な Azure AD ユーザー名とドメイン資格情報を使用します。  Ambari は、Azure AD 内のユーザーの表示名であるユーザー エイリアスを表示します。
新しいユーザーの例では、`hiveuser3@contoso.com` というユーザー名を持っています。 Ambari では、この新しいユーザーは `hiveuser3` として表示されますが、このユーザーは `hiveuser3@contoso.com` として Ambari にログインします。

## <a name="see-also"></a>参照

* [ESP HDInsight での Apache Hive ポリシーの構成](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP を使用する HDInsight クラスターを管理する](./domain-joined/apache-domain-joined-manage.md)
* [Apache Ambari に対するユーザーの承認](hdinsight-authorize-users-to-ambari.md)