---
title: Azure HDInsight ID ブローカー (HIB)
description: ドメイン参加済みの Apache Hadoop クラスターの認証を簡略化するための Azure HDInsight ID ブローカーについて説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: c5bc5bc702dbd54bbad43aa4e1c6c8650c18e088
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863192"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID ブローカー (HIB)

この記事では、Azure HDInsight ID ブローカー機能を設定して使用する方法について説明します。 この機能を使用すると、Azure Active Directory Domain Services (Azure AD DS) の従来のパスワード ハッシュを必要とせずに多要素認証を実施しながら、Apache Ambari で最新の OAuth 認証を使用することができます。

## <a name="overview"></a>概要

HDInsight ID ブローカーにより、次のシナリオで複雑な認証設定が簡略化されます。

* 組織では、クラウド リソースにアクセスするユーザーを認証するためにフェデレーションに依存しています。 以前は、HDInsight Enterprise セキュリティ パッケージ クラスターを使用するには、オンプレミス環境から Azure Active Directory (Azure AD) へのパスワード ハッシュ同期を有効にする必要がありました。 この要件は、組織によっては難しい場合や望ましくない場合があります。
* あなたの組織では、Apache Ambari や他のクラスター リソースへの Web ベースまたは HTTP ベースのアクセスに対して多要素認証を実施したいと考えているとします。

HDInsight ID ブローカーにより、パスワード ハッシュを Azure AD DS に同期させることなく、OAuth (最新) から Kerberos (レガシ) へのプロトコル移行を可能にする認証インフラストラクチャが実現します。 このインフラストラクチャは、HDInsight ID ブローカー ノードが有効になっている Windows Server 仮想マシン (VM) 上で動作するコンポーネントと、クラスター ゲートウェイ ノードで構成されています。

次の表を使用して、組織のニーズに基づいた最適な認証オプションを決定します。

|認証オプション |HDInsight 構成 | 検討するべき要素 |
|---|---|---|
| OAuth のみ | Enterprise セキュリティ パッケージ + HDInsight ID ブローカー | 最も安全なオプションです。 (多要素認証がサポートされています)。パス ハッシュ同期は必要では "*ありません*"。 Azure AD DS でのパスワード ハッシュを使用しないオンプレミス アカウントでは ssh/kinit/keytab アクセスはできません。 クラウド専用アカウントでは、引き続き ssh/kinit/keytab 接続することができます。 OAuth による Ambari への Web ベースのアクセス。 OAuth をサポートするために、レガシ アプリ (JDBC/ODBC など) を更新する必要があります。|
| OAuth と基本認証 | Enterprise セキュリティ パッケージ + HDInsight ID ブローカー | OAuth による Ambari への Web ベースのアクセス。 レガシ アプリでは、基本認証が引き続き使用されます。基本認証アクセスでは、多要素認証を無効にする必要があります。 パス ハッシュ同期は必要では "*ありません*"。 Azure AD DS でのパスワード ハッシュを使用しないオンプレミス アカウントでは ssh/kinit/keytab アクセスはできません。 クラウド専用アカウントでは、引き続き ssh/kinit 接続することができます。 |
| 基本認証のみ | Enterprise セキュリティ パッケージ | オンプレミスのセットアップに最も似ています。 Azure AD DS へのパスワード ハッシュ同期が必要です。 オンプレミスのアカウントでは、ssh/kinit 接続することも、keytab を使用することもできます。 バッキング ストレージが Azure Data Lake Storage Gen2 である場合は、多要素認証を無効にする必要があります。 |

次の図は、HDInsight ID ブローカーが有効になった後の、フェデレーション ユーザーを含むすべてのユーザーの、最新の OAuth ベースの認証フローを示しています。

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="HDInsight ID ブローカーによる認証フローを示す図。" border="false":::

この図では、まず最初にクライアント (つまり、ブラウザーまたはアプリ) で OAuth トークンを取得する必要があります。 次に、HTTP 要求でゲートウェイにトークンが提示されます。 Azure portal などの他の Azure サービスに既にサインインしている場合は、シングル サインオン エクスペリエンスによって HDInsight クラスターにサインインできます。

基本認証 (つまり、ユーザー名とパスワード) のみがサポートされるレガシ アプリケーションはまだ多数存在する可能性があります。 これらのシナリオでは、引き続き HTTP 基本認証を使用してクラスター ゲートウェイに接続できます。 このセットアップでは、ゲートウェイ ノードから Active Directory Federation Services (AD FS) エンドポイントへのネットワーク接続を確保して、ゲートウェイ ノードからの直接の通信経路を確保する必要があります。

次の図は、フェデレーション ユーザーの基本認証フローを示しています。 まず、ゲートウェイで [ROPC フロー](../../active-directory/develop/v2-oauth-ropc.md)を使用した認証が試みられます。 Azure AD に同期されたパスワード ハッシュがない場合は、フォールバックして AD FS エンドポイントを検出し、AD FS エンドポイントへアクセスすることで認証が完了します。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="基本認証を使用したアーキテクチャを示す図。" border="false":::

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID ブローカーを有効にする

HDInsight ID ブローカーが有効になっている Enterprise セキュリティ パッケージ クラスターを作成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Enterprise セキュリティ パッケージ クラスターの基本的な作成手順に従います。 詳細については、[Enterprise セキュリティ パッケージが有効な HDInsight クラスターを作成する](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)方法に関するページを参照してください。
1. **[HDInsight ID ブローカーを有効にする]** を選択します。

HDInsight ID ブローカー機能によって、クラスターに追加の VM が 1 つ追加されます。 この VM は HDInsight ID ブローカー ノードであり、認証をサポートするサーバー コンポーネントが含まれています。 HDInsight ID ブローカー ノードは、Azure AD DS ドメインに参加しているドメインです。

:::image type="content" source="./media/identity-broker/identity-broker-enable.png" alt-text="HDInsight ID ブローカーを有効にするオプションを示す図。" border="true":::

### <a name="use-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの使用

テンプレートのコンピューティング プロファイルに次の属性を含む `idbrokernode` という名前の新しいロールを追加すると、HDInsight ID ブローカー ノードが有効になっているクラスターが作成されます。

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

ARM テンプレートの完全なサンプルについては、[こちら](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)で公開されているテンプレートを参照してください。


## <a name="tool-integration"></a>ツールの統合

HDInsight ツールは、OAuth をネイティブにサポートするように更新されています。 これらのツールは、クラスターへの最新の OAuth ベースのアクセスに使用します。 HDInsight [IntelliJ プラグイン](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib)は、Scala などの Java ベースのアプリケーションに使用できます。 [Visual Studio Code 用の Spark および Hive Tools](../hdinsight-for-vscode.md) は、PySpark および Hive ジョブに使用できます。 これらのツールでは、バッチと対話型ジョブの両方がサポートされています。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS でのパスワード ハッシュを使用しない SSH アクセス

|SSH オプション |検討するべき要素 |
|---|---|
| ローカル VM アカウント (例: sshuser) | このアカウントは、クラスターの作成時に指定済みです。 このアカウントには Kerberos 認証はありません。 |
| クラウド専用アカウント (alice@contoso.onmicrosoft.com など) | パスワード ハッシュは Azure AD DS で入手できます。 Kerberos 認証は、SSH Kerberos 経由で行うことができます。 |
| オンプレミスのアカウント (alice@contoso.com など) | SSH Kerberos 認証は、Azure AD DS でパスワード ハッシュを入手できる場合にのみ可能です。 それ以外の場合、このユーザーはクラスターに SSH 接続できません。 |

ドメイン参加済みの VM に SSH 接続する場合、または `kinit` コマンドを実行する場合は、パスワードを入力する必要があります。 SSH Kerberos 認証を使用するには、Azure AD DS でハッシュを入手できるようにする必要があります。 管理シナリオにのみ SSH を使用する場合は、クラウド専用アカウントを 1 つ作成し、それを使用してクラスターに SSH 接続することができます。 その他のオンプレミスのユーザーは、Azure AD DS でパスワード ハッシュを入手できるようにしなくても、Ambari または HDInsight ツールや HTTP 基本認証を使用できます。

組織では Azure AD DS にパスワード ハッシュが同期されていない場合は、ベスト プラクティスとして、Azure AD にクラウド専用ユーザーを 1 人作成します。 次に、クラスターを作成するときにこれをクラスター管理者として割り当て、管理目的で使用します。 これを使用して、SSH 経由で VM へのルート アクセスを取得できます。

認証問題を解決するには、[こちらのガイド](./domain-joined-authentication-issues.md)を参照してください。

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>HDInsight ID ブローカーで OAuth を使用して HDInsight ゲートウェイに接続するクライアント

HDInsight ID ブローカーのセットアップでは、ゲートウェイに接続するカスタム アプリやクライアントを、必要な OAuth トークンを最初に取得するように更新できます。 [こちらのドキュメント](../../storage/common/storage-auth-aad-app.md)の手順に従って、次の情報を含むトークンを取得します。

*    OAuth リソース URI: `https://hib.azurehdinsight.net` 
*   アプリID: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    アクセス許可: (名前: Cluster.ReadWrite、ID:8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth トークンを取得した後、クラスター ゲートウェイへの HTTP 要求の Authorization ヘッダーでそれを使用します (例: https://<clustername>-int.azurehdinsight.net)。 Apache Livy API へのサンプル curl コマンドは次のようになります。
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Beeline と Livy を使用する場合は、[こちら](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples)で提供されているサンプル コードに従い、OAuth を使用してクラスターに接続するようにクライアントをセットアップすることもできます。

## <a name="faq"></a>よく寄せられる質問
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>AAD では HDInsight によってどのようなアプリが作成されますか?
クラスターごとに、サード パーティのアプリケーションが、identifierUri (`https://clustername.azurehdinsight.net` など) としてクラスター URI とともに AAD に登録されます。

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>HIB 対応クラスターを使用する前にユーザーに同意を求めるのはなぜですか?
AAD では、ユーザーを認証したり、データにアクセスしたりする前に、すべてのサード パーティのアプリケーションに同意を求めます。

### <a name="can-the-consent-be-approved-programatically"></a>同意はプログラムによって承認できますか?
Microsoft Graph API を使用すると、同意を自動化できます。[API のドキュメント](/graph/api/resources/oauth2permissiongrant)を参照してください。同意を自動化する順序は、以下のとおりです。

* アプリを登録し、Microsoft Graph にアクセスするための Application.ReadWrite.All アクセス許可をそのアプリに付与します
* クラスターが作成されたら、識別子 URI に基づいてクラスター アプリを照会します
* アプリの同意を登録します

クラスターが削除されると、HDInsight はアプリを削除します。同意をクリーンアップする必要はありません。

 


## <a name="next-steps"></a>次のステップ

* [Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory ユーザーを HDInsight クラスターに同期する](../hdinsight-sync-aad-users-to-cluster.md)
* [クラスター パフォーマンスの監視](../hdinsight-key-scenarios-to-monitor.md)