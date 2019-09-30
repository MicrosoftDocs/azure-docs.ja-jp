---
title: Azure Active Directory ユーザーをクラスターに同期する - Azure HDInsight
description: Azure Active Directory の認証されたユーザーを HDInsight クラスターに同期します。
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9767bccc754d5ccf8c0e7cbb66c8e9c3d5eaf7d0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105364"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory ユーザーを HDInsight クラスターに同期する

[Enterprise セキュリティ パッケージ (ESP) を使用する HDInsight クラスター](hdinsight-domain-joined-introduction.md)は、Azure Active Directory (Azure AD) ユーザーに強力な認証を使用したり、*ロールベースのアクセス制御* (RBAC) ポリシーを使用したりできます。 Azure AD にユーザーやグループを追加するとき、アクセスが必要なユーザーをクラスターに同期できます。

## <a name="prerequisites"></a>前提条件

まだそのようにしていない場合は、[Enterprise セキュリティ パッケージを使用する HDInsight クラスターを作成します](hdinsight-domain-joined-configure.md)。

## <a name="add-new-azure-ad-users"></a>新しい Azure AD ユーザーを追加する

ホストを表示するには、Ambari Web UI を開きます。 各ノードが、新しい無人アップグレードの設定で更新されます。

1. [Azure Portal](https://portal.azure.com) で、ESP クラスターに関連付けられた Azure AD ディレクトリに移動します。

2. 左側のメニューから **[すべてのユーザー]** を選択してから、 **[New user] (新しいユーザー)** を選択します。

    ![Azure portal のすべてのユーザーとグループ](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. 新しいユーザーのフォームを完了します。 クラスター ベースのアクセス許可を割り当てるために作成したグループを選択します。 この例では、新しいユーザーを割り当てることのできる "HiveUsers" という名前のグループを作成します。 ESP クラスターを作成するための[手順の例](hdinsight-domain-joined-configure.md)には、`HiveUsers` と `AAD DC Administrators` という 2 つのグループの追加が含まれています。

    ![Azure portal ユーザー ウィンドウのグループの選択](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. **作成** を選択します。

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Apache Ambari REST API を使用してユーザーを同期する

クラスターの作成プロセス中に指定されたユーザー グループは、その時点で同期されます。 ユーザーの同期は、1 時間に 1 回自動的に実行されます。 ユーザーを直ちに同期するには、またはクラスターの作成中に指定されたグループ以外のグループを同期するには、Ambari REST API を使用します。

次のメソッドは、Ambari REST API で POST を使用します。 詳細については、「[Ambari REST API を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari-rest-api.md)」を参照してください。

1. [SSH でクラスターに接続します](hdinsight-hadoop-linux-use-ssh-unix.md)。 Azure Portal のクラスターの概要ペインから、 **[Secure Shell (SSH)]** ボタンを選択します。

    ![HDInsight Secure Shell (SSH) アイコン](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. 表示されている `ssh` コマンドをコピーし、それを SSH クライアントに貼り付けます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

3. 認証の後、次のコマンドを入力します。

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    応答は次のようになります。

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. 同期の状態を表示するには、新しい `curl` コマンドを実行します。

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    応答は次のようになります。
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. この結果は、状態が **[完了]** であり、新しいユーザーが 1 人作成され、そのユーザーにメンバシップが割り当てられたことを示しています。 この例では、ユーザーが Azure AD 内の同じグループに追加されたため、ユーザーは "HiveUsers" 同期済み LDAP グループに割り当てられます。

> [!NOTE]  
> 前のメソッドは、クラスターの作成中にドメイン設定の **[Access user group] (アクセス ユーザー グループ)** プロパティで指定された Azure AD グループのみを同期します。 詳細については、「[create an HDInsight cluster (HDInsight クラスターを作成する)](domain-joined/apache-domain-joined-configure.md)」を参照してください。

## <a name="verify-the-newly-added-azure-ad-user"></a>新しく追加された Azure AD ユーザーを確認する

新しい Azure AD ユーザーが追加されたことを確認するには、[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) を開きます。 **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** を参照することによって、Ambari Web UI にアクセスします。 クラスター管理者のユーザー名とパスワードを入力します。

1. Ambari ダッシュボードから、 **[管理者]** メニューの下にある **[Ambari の管理]** を選択します。

    ![Apache Ambari ダッシュボードの Ambari の管理](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. ページの左側の **[User + Group Management] (ユーザーとグループの管理)** メニュー グループの下にある **[ユーザー]** を選択します。

    ![HDInsight の [ユーザーとグループ] メニュー](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. 新しいユーザーが [ユーザー] テーブル内に表示されます。 [種類] は `Local` ではなく、`LDAP` に設定されています。

    ![HDInsight aad ユーザー ページの概要](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>新しいユーザーとして Ambari にログインする

新しいユーザー (またはその他の任意のドメイン ユーザー) が Ambari にログインしたとき、そのユーザーは自身の完全な Azure AD ユーザー名とドメイン資格情報を使用します。  Ambari は、Azure AD 内のユーザーの表示名であるユーザー エイリアスを表示します。 新しいユーザーの例では、`hiveuser3@contoso.com` というユーザー名を持っています。 Ambari では、この新しいユーザーは `hiveuser3` として表示されますが、このユーザーは `hiveuser3@contoso.com` として Ambari にログインします。

## <a name="see-also"></a>関連項目

* [ESP HDInsight での Apache Hive ポリシーの構成](hdinsight-domain-joined-run-hive.md)
* [ESP を使用する HDInsight クラスターを管理する](hdinsight-domain-joined-manage.md)
* [Apache Ambari に対するユーザーの承認](hdinsight-authorize-users-to-ambari.md)
