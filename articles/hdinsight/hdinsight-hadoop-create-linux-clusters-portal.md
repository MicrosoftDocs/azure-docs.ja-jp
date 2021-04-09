---
title: Web ブラウザーを使用して Apache Hadoop クラスターを作成する - Azure HDInsight
description: HDInsight 上での Apache Hadoop、Apache HBase、Apache Storm、または Apache Spark クラスターの作成について説明します。 Web ブラウザーと Azure portal を使用します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: 98d18044693bc6f4d23e04e9403e71686df39451
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872202"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal は、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの Azure HDInsight クラスターを作成する方法について説明します。 追加の詳細については、[HDInsight クラスターの作成](./hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure Portal には、ほとんどのクラスターのプロパティが公開されます。 Azure Resource Manager テンプレートを使用すると、多くの詳細を非表示にできます。 詳しくは、「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-clusters"></a>クラスターの作成

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部のメニューで、 **[+ リソースの作成]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png" alt-text="Azure portal で新しいクラスターを作成する":::

1. **[分析]**  >  **[Azure HDInsight]** を選択して **[HDInsight クラスターの作成]** ページに移動します。

## <a name="basics"></a>基本

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png" alt-text="HDInsight でのクラスター作成の基本":::

**[基本]** タブで次の情報を指定します。

|プロパティ |説明 |
|---|---|
|サブスクリプション|ドロップダウン リストから、このクラスターに使用する Azure サブスクリプションを選択します。|
|Resource group|ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
|クラスター名|グローバルに一意の名前を入力します。|
|リージョン|ドロップダウン リストから、クラスターの作成先となるリージョンを選択します。|
|クラスターの種類|**[クラスターの種類の選択]** をクリックして、一覧を開きます。 一覧から、目的のクラスターの種類を選択します。 HDInsight クラスターには、さまざまな種類があります。 それぞれに対応するワークロードやテクノロジがあり、それに合わせてクラスターが調整されます。 複数の種類を組み合わせたクラスターを作成する方法はサポートされていません。|
|Version|ドロップダウン リストから **バージョン** を選択します。 どれを選択すべきかわからない場合は、既定のバージョンを使用します。 詳細については、「 [HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。|
|クラスター ログイン ユーザー名|ユーザー名を指定します。既定値は **admin** です。|
|クラスター ログイン パスワード|パスワードを指定します。|
|クラスター ログイン パスワードを確認する|パスワードを再入力します。|
|Secure Shell (SSH) ユーザー名|ユーザー名を指定します。既定値は **sshuser** です。|
|SSH にクラスター ログイン パスワードを使用する|SSH パスワードを前に指定した管理者パスワードと同じにする場合は、 **[SSH にクラスター ログイン パスワードを使用する]** チェック ボックスをオンします。 そうでない場合は、SSH ユーザーを認証するための **[パスワード]** または **[公開キー]** を指定します。 公開キーを使用することをお勧めします。 下部にある **[選択]** を選択して、資格情報の構成を保存します。  詳しくは、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。|

**ストレージ >>** を選択して、次のタブに進みます。

## <a name="storage"></a>ストレージ

> [!WARNING] 
> 2020 年 6 月 15 日以降、HDInsight を使用して新しいサービス プリンシパルを作成することはできません。 Azure Active Directory 使用した[サービス プリンシパルと証明書の作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png" alt-text="HDInsight でのクラスター ストレージの作成":::

### <a name="primary-storage"></a>プライマリ ストレージ

**[プライマリ ストレージの種類]** ドロップダウン リストから、既定のストレージの種類を選択します。 後で入力するフィールドは、選択内容によって異なります。 **Azure Storage** の場合:

1. **[選択方法]** については、 **[一覧から選択する]** または **[アクセス キーを使用する]** を選択します。
    * **[一覧から選択する]** では、ドロップダウン リストからお使いの **プライマリ ストレージ アカウント** を選択するか、 **[新規作成]** を選択します。
    * **[アクセス キーを使用する]** では、お使いの **ストレージ アカウント名** を入力します。 次に、**アクセス キー** を指定します。

1. **[コンテナー]** では、既定値をそのまま使用するか、新しい値を入力します。

### <a name="additional-azure-storage"></a>追加の Azure Storage

省略可能:追加のクラスター ストレージに対する **[Azure Storage の追加]** を選択します。 HDInsight クラスター以外のリージョンでの追加のストレージ アカウントの使用はサポートされていません。

### <a name="metastore-settings"></a>メタストアの設定

省略可能:既存の SQL Database を指定して、クラスターの外部に Apache Hive、Apache Oozie、または Apache Ambari メタデータを保存します。 メタストアに使用される Azure SQL データベースでは、Azure HDInsight を含む他の Azure サービスに接続できる必要があります。 メタストアを作成するとき、データベース名にダッシュやハイフンを使用しないでください。 これらの文字が含まれると、クラスター作成プロセスが失敗する可能性があります。

> [!IMPORTANT]
> メタストアをサポートするクラスター図形の場合、既定のメタストアでは、**DTU 上限が Basic レベルの 5 (アップグレード不可能)** である Azure SQL Database が提供されます。 基本的なテスト目的に適しています。 より大きなワークロードや運用環境のワークロードの場合は、外部のメタストアに移行することをお勧めします。

**セキュリティとネットワーク >>** を選択して、次のタブに進みます。

## <a name="security--networking"></a>セキュリティとネットワーク

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png" alt-text="HDInsight でのクラスター セキュリティ ネットワークの作成":::

**[セキュリティとネットワーク]** タブで、次の情報を入力します。

|プロパティ |説明 |
|---|---|
|Enterprise セキュリティ パッケージ|省略可能:**Enterprise セキュリティ パッケージ** を使用するには、このチェックボックスをオンにします。 詳細については、[Azure Active Directory Domain Services を使用した HDInsight クラスターの Enterprise セキュリティ パッケージを含む構成](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)に関する記事を参照してください。|
|TLS|省略可能:ドロップダウン リストから TLS バージョンを選択します。 詳細については、「[トランスポート層セキュリティ](./transport-layer-security.md)」を参照してください。|
|仮想ネットワーク|省略可能:ドロップダウン リストから既存の仮想ネットワークとサブネットを選択します。 詳細については、[Azure HDInsight クラスター用の仮想ネットワークのデプロイ計画](hdinsight-plan-virtual-network-deployment.md)に関する記事を参照してください。 その記事には、仮想ネットワークの具体的な構成要件が含まれます。|
|ディスク暗号化設定|省略可能:暗号化を使用するには、このチェックボックスをオンにします。 詳細については、「[お客様が管理するキー ディスクの暗号化](./disk-encryption.md)」を参照してください。|
|Kafka REST プロキシ|この設定は、Kafka のクラスターの種類にのみ使用できます。 詳細については、[REST プロキシの使用](./kafka/rest-proxy.md)に関する記事を参照してください。|
|ID|省略可能:ドロップダウン リストから、ユーザーが割り当てた既存のサービス ID を選択します。 詳細については、「[Azure HDInsight のマネージド ID](./hdinsight-managed-identities.md)」を参照してください。|

**構成と価格 >>** を選択して、次のタブに進みます。

## <a name="configuration--pricing"></a>構成と価格

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png" alt-text="HDInsight でのクラスターの構成の作成":::

**[構成と価格]** タブで、次の情報を入力します。

|プロパティ |説明 |
|---|---|
|+ アプリケーションの追加|省略可能:目的のアプリケーションを選択します。 これらのアプリケーションは、Microsoft、独立系ソフトウェア ベンダー (ISV)、またはご自身が独自に開発できます。 詳しくは、「[クラスター作成時のアプリケーションのインストール](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)」をご覧ください。|
|ノード サイズ|省略可能:別のサイズのノードを選択します。|
|ノードの数|省略可能:指定したノードの種類のノードの数を入力します。 32 個を超えるワーカー ノードを計画する場合は、コア数が 8 個以上で RAM が 14 GB 以上のサイズのヘッド ノードを選択します。 クラスターの作成時に、または作成後にクラスターのスケーリングで、ノードを計画します。|
|自動スケールの有効化|省略可能:この機能を有効にするには、チェックボックスをオンにします。 詳細については、「[Azure HDInsight クラスターを自動的にスケール調整する](./hdinsight-autoscale-clusters.md)」を参照してください。|
|+ スクリプト アクションの追加|省略可能:このオプションは、クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合に使用します。 スクリプト アクションについて詳しくは、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。|

**[確認と作成 >>]** を選択してクラスターの構成を検証し、最後のタブに進みます。

## <a name="review--create"></a>確認と作成

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight でのクラスター作成の概要":::

設定を確認します。 **[作成]** を選択して、クラスターを作成します。

クラスターが作成されるまで、通常は約 20 分かかります。 **[通知]** を監視して、プロビジョニング プロセスをチェックします。

## <a name="post-creation"></a>投稿の作成

作成プロセスが完了したら、 **[デプロイが成功しました]** という通知から **[リソースに移動]** を選択します。 クラスター ウィンドウには、次の情報が表示されます。

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png" alt-text="HDI Azure portal クラスターの概要":::

ウィンドウのいくつかのアイコンの説明は以下のとおりです。

|プロパティ | 説明 |
|---|---|
|概要|クラスターに関するすべての重要な情報が提供されます。 たとえば、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などです。|
|クラスター ダッシュボード|クラスターに関連付けられている Ambari ポータルに移動します。|
|SSH およびクラスターのログイン|SSH を使用してクラスターにアクセスするために必要な情報が提供されます。|
|削除|HDInsight クラスターを削除します。|

## <a name="delete-the-cluster"></a>クラスターを削除する

「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](./hdinsight-delete-cluster.md)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](./hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight クラスターが正常に作成されました。 次に、クラスターを操作する方法を学習してください。

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)