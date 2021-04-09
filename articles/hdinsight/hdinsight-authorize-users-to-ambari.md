---
title: Ambari Views のユーザー承認 - Azure HDInsight
description: ESP が有効になっている HDInsight クラスターに対する Ambari ユーザーと Ambari グループのアクセス許可を管理する方法。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 44f98692131070940514498b6bf648936bea0806
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872236"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Apache Ambari ビューに対してユーザーを承認する

[Enterprise セキュリティ パッケージ (ESP) が有効になっている HDInsight クラスター](./domain-joined/hdinsight-security-overview.md)には、エンタープライズ グレードの機能が備わっています。Azure Active Directory ベースの認証もその 1 つです。 クラスターへのアクセスが提供されている Azure AD グループに追加された[新しいユーザーを同期](hdinsight-sync-aad-users-to-cluster.md)して、特定のユーザーに特定のアクションの実行を許可できます。 [Apache Ambari](https://ambari.apache.org/) でのユーザー、グループ、およびアクセス許可の操作は、ESP HDInsight クラスターと標準 HDInsight クラスターの両方でサポートされています。

Active Directory ユーザーは、自分のドメイン資格情報を使用してクラスター ノードにサインインできます。 また、クラスターと他の承認済みエンドポイント ([Hue](https://gethue.com/)、Ambari Views、ODBC、JDBC、PowerShell、REST API など) との対話も、ドメイン資格情報で認証することができます。

> [!WARNING]  
> Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ (hdinsightwatchdog) のパスワードは変更しないでください。 パスワードを変更すると、スクリプト アクションを使用したり、クラスターでスケール操作を実行する能力が損なわれます。

新しい ESP クラスターをまだプロビジョニングしていない場合は、[こちらの手順](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)に従ってプロビジョニングしてください。

## <a name="access-the-ambari-management-page"></a>Ambari 管理ページにアクセスする

[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) の **Ambari 管理ページ** にアクセスするには、ブラウザーで `https://CLUSTERNAME.azurehdinsight.net` にアクセスします。 クラスターの作成時に定義したクラスタ アドミニストレーターのユーザー名とパスワードを入力します。 次に、Ambari のダッシュボードで **[admin]\(管理\)** メニューの **[Manage Ambari]\(Ambari の管理\)** を選択します。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png" alt-text="Apache Ambari のダッシュボードの管理":::

## <a name="add-users"></a>ユーザーの追加

### <a name="add-users-through-the-portal"></a>ポータルを使用してユーザーを追加する

1. 管理ページで、**[ユーザー]** を選択します。

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png" alt-text="Apache Ambari 管理ページのユーザー":::

1. **[+ Create Local User]\(+ ローカル ユーザーの作成\)** を選択します。

1. **ユーザー名** と **パスワード** を指定します。 **[保存]** を選択します。

### <a name="add-users-through-powershell"></a>PowerShell を使用してユーザーを追加する

以下の変数を編集して、`CLUSTERNAME`、`NEWUSER`、`PASSWORD` を適切な値に置き換えます。

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Curl を使用してユーザーを追加する

以下の変数を編集して、`CLUSTERNAME`、`ADMINPASSWORD`、`NEWUSER`、`USERPASSWORD` を適切な値に置き換えます。 このスクリプトは、bash を使用して実行するように設計されています。 Windows コマンド プロンプトの場合は、若干の変更が必要になります。

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Apache Hive ビューへのアクセス許可を付与する

Ambari には、[Apache Hive](https://hive.apache.org/) や [Apache TEZ](https://tez.apache.org/) のビュー インスタンスが備わっています。 Hive ビュー インスタンスへのアクセス権を付与するには、**Ambari 管理ページ** に移動します。

1. 管理ページの左側の **[Views]\(ビュー\)** メニュー ヘッダーにある **[Views]\(ビュー\)** リンクを選択します。

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png" alt-text="Apache Ambari のビューのリンク表示":::

2. [Views]\(ビュー\) ページの **HIVE** 行を展開します。 Hive サービスをクラスターに追加するときに、既定の Hive ビューが 1 つ作成されます。 必要に応じて Hive ビュー インスタンスをさらに作成することもできます。 Hive ビューを選択します。

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png" alt-text="HDInsight ビュー - Apache Hive ビュー":::

3. ビュー ページを下の方へスクロールします。 *[Permissions]\(アクセス許可\)* セクションには、ビューのアクセス許可をドメイン ユーザーに許可するためのオプションが 2 つあります。

**[Grant permission to these users]\(次のユーザーにアクセス許可を付与\)** :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png" alt-text="[Grant permission to these users]\(次のユーザーにアクセス許可を付与\)":::

**[Grant permission to these groups]\(次のグループにアクセス許可を付与\)** :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png" alt-text="[Grant permission to these groups]\(次のグループにアクセス許可を付与\)":::

1. ユーザーを追加するには、**[Add User]\(ユーザーの追加\)** ボタンを選択します。

   * ユーザー名を入力し始めると、既に定義されている名前がドロップダウン リストに表示されます。

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png" alt-text="Apache Ambari のユーザー オート コンプリート":::

   * ユーザー名を選択するか、最後まで入力します。 このユーザー名を新しいユーザーとして追加するには、**[New]\(新規\)** ボタンを選択します。

   * 変更を保存するには、**青色のチェック ボックス** をオンにします。

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png" alt-text="Apache Ambari のユーザーへのアクセス許可の付与":::

1. グループを追加するには、**[Add Group]\(グループの追加\)** ボタンを選択します。

   * グループ名の入力を開始します。 既存のグループ名を選択 (または新しいグループを追加) するプロセスは、ユーザーを追加するときと同じです。
   * 変更を保存するには、**青色のチェック ボックス** をオンにします。

     :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png" alt-text="Apache Ambari のアクセス許可の付与":::

ビューのアクセス許可をユーザーに割り当てるとき、余分なアクセス許可があるグループのメンバーにすることを望まない場合、ビューに直接ユーザーを追加する方法が便利です。 管理オーバーヘッドを減らすには、おそらくグループにアクセス許可を割り当てる方が簡単です。

## <a name="grant-permissions-to-apache-tez-views"></a>Apache TEZ ビューへのアクセス許可を付与する

[Apache Hive](https://hive.apache.org/) クエリや [Apache Pig](https://pig.apache.org/) スクリプトによって送信されたすべての Tez ジョブは、[Apache TEZ](https://tez.apache.org/) ビューのインスタンスを使用して監視したりデバッグしたりすることができます。 クラスターのプロビジョニング時に、既定の Tez ビュー インスタンスが 1 つ作成されます。

Tez ビュー インスタンスにユーザーとグループを割り当てるには、前述したように、[Views]\(ビュー\) ページの **TEZ** 行を展開します。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png" alt-text="HDInsight ビュー - Apache Tez ビュー":::

ユーザーまたはグループを追加するには、前のセクションの手順 3. ～ 5. を繰り返します。

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる

ユーザーとグループには 5 つのセキュリティ ロールがあります。以下、それらをアクセス権の高い順に示します。

* クラスター管理者
* クラスター オペレーター
* サービス管理者
* サービス オペレーター
* クラスター ユーザー

ロールを管理するには、**Ambari 管理ページ** に移動し、左側の *[Clusters]\(クラスター\)* メニュー グループにある **[Roles]\(ロール\)** リンクを選択します。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png" alt-text="Apache Ambari のロールのメニュー リンク":::

それぞれのロールに付与されているアクセス許可の一覧については、[Roles]\(ロール\) ページの **[Roles]\(ロール\)** テーブル ヘッダーの横にある青色の疑問符をクリックしてください。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png" alt-text="Apache Ambari のロールのメニュー リンクのアクセス許可":::

このページには、ユーザーとグループのロールを管理するための 2 種類のビュー (ブロック ビューとリスト ビュー) が用意されています。

### <a name="block-view"></a>ブロック ビュー

ブロック ビューには、各ロールが行ごとに表示されるほか、前述したような **[Assign roles to these users]\(次のユーザーにロールを割り当て\)** オプションと **[Assign roles to these groups]\(次のグループにロールを割り当て\)** オプションが表示されます。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png" alt-text="Apache Ambari のロールのブロック ビュー":::

### <a name="list-view"></a>リスト ビュー

リスト ビューには、ユーザーとグループという 2 つのカテゴリの簡単な編集機能が用意されています。

* リスト ビューの [Users]\(ユーザー\) カテゴリには、すべてのユーザーが一覧表示され、各ユーザーのロールをドロップダウン リストで選択することができます。

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png" alt-text="Apache Ambari のロールのリスト ビュー - ユーザー":::

* リスト ビューの [Groups]\(グループ\) カテゴリには、すべてのグループと、各グループに割り当てられているロールが表示されます。 この例に示したグループのリストは、クラスターのドメイン設定の **[Access user group]\(アクセス ユーザー グループ\)** プロパティに指定された Azure AD グループから同期されています。 [ESP が有効になっている HDInsight クラスターの作成](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)に関するページを参照してください。

    :::image type="content" source="./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png" alt-text="Apache Ambari のロールのリスト ビュー - グループ":::

    上の画像では、"hiveusers" グループに "*クラスター ユーザー*" ロールが割り当てられています。 これは読み取り専用のロールで、このグループのユーザーは、サービスの構成とクラスターのメトリックを表示することはできますが、変更することはできません。

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Ambari に読み取り専用ユーザーとしてログインする

Azure AD ドメイン ユーザー "hiveuser1" には、Hive ビューと Tez ビューに対するアクセス許可を割り当ててあります。 Ambari Web UI を起動してこのユーザーのドメイン資格情報 (電子メール形式の Azure AD ユーザー名とパスワード) を入力すると、Ambari Views ページにユーザーがリダイレクトされます。 そこから、アクセス可能な任意のビューを選択することができます。 このユーザーが、このサイトの他の領域 (ダッシュボード、サービス、ホスト、アラート、管理ページなど) にアクセスすることはできません。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png" alt-text="Apache Ambari の読み取り専用ユーザー":::

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Ambari にクラスター ユーザーとしてログインする

"*クラスター ユーザー*" ロールには、Azure AD ドメイン ユーザー "hiveuser2" を割り当ててあります。 このロールは、ダッシュボードとすべてのメニュー項目にアクセスすることができます。 クラスター ユーザーは、選択できるオプションが管理者と比べて少なくなります。 たとえば hiveuser2 は、各サービスの構成を表示することはできますが、編集することはできません。

:::image type="content" source="./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png" alt-text="Apache Ambari のダッシュボードの表示":::

## <a name="next-steps"></a>次のステップ

* [ESP HDInsight での Apache Hive ポリシーの構成](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight クラスターの管理](./domain-joined/apache-domain-joined-manage.md)
* [HDInsight 上の Apache Hadoop で Apache Hive ビューを使用する](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD ユーザーをクラスターに同期する](hdinsight-sync-aad-users-to-cluster.md)
* [Apache Ambari REST API を使用した HDInsight クラスターの管理](./hdinsight-hadoop-manage-ambari-rest-api.md)