---
title: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する
description: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する方法について学習します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: f6bee53268ea99e99d79e218eaf7a5ae9fcb1d05
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104460"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する

Azure Data Lake Storage Gen2 は、ビッグ データ分析専用のクラウド ストレージ サービスで、Azure Blob Storage 上に構築されています。 Data Lake Storage Gen2 は Azure Blob Storage と Azure Data Lake Storage Gen1 の機能を結合したものです。 その結果、Azure Data Lake Storage Gen1 に由来するファイル システム セマンティクス、ディレクトリ レベルとファイル レベルのセキュリティ、スケーラビリティなどの機能と、Azure Blob Storage に由来する低コストの階層型記憶域、高可用性、ディザスター リカバリー機能とがこのサービスで提供されます。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 の可用性

Data Lake Storage Gen2 は、ほぼすべての Azure HDInsight クラスターの種類のストレージ オプションとして、既定のストレージ アカウントと追加のストレージ アカウントの両方で使用できます。 ただし、HBase が持てる Data Lake Storage Gen2 アカウントは 1 つのみです。

Data Lake Storage Gen2 を使用したクラスター作成オプションの詳細な比較については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](hdinsight-hadoop-compare-storage-options.md)」を参照してください。

> [!Note]  
> Data Lake Storage Gen2 を**プライマリ ストレージの種類**として選択すると、追加のストレージとして Data Lake Storage Gen1 アカウントを選択できなくなります。

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Data Lake Storage Gen2 を使用して Azure portal からクラスターを作成する

ストレージに Data Lake Storage Gen2 を使用する HDInsight クラスターを作成するには、次の手順に従って Data Lake Storage Gen2 アカウントを作成します。

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID をまだお持ちでない場合には、作成します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左上の **[リソースの作成]** をクリックします。
1. 検索ボックスに「**ユーザー割り当て**」と入力し **[ユーザー割り当てマネージド ID]** をクリックします。
1. **Create** をクリックしてください。
1. ご自身のマネージド ID 名を入力し、適切なサブスクリプション、リソース グループ、および場所を選択します。
1. **Create** をクリックしてください。

Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Azure HDInsight のマネージド ID](hdinsight-managed-identities.md)」を参照してください。

![ユーザー割り当てマネージド ID を作成する](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを作成する

Azure Data Lake Storage Gen2 ストレージ アカウントを作成します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左上の **[リソースの作成]** をクリックします。
1. 検索ボックスに「**ストレージ**」と入力し、 **[ストレージ アカウント]** をクリックします。
1. **Create** をクリックしてください。
1. **[ストレージ アカウントの作成]** 画面で次を実行します。
    1. 正しいサブスクリプションとリソース グループを選択します。
    1. ご自分の Data Lake Storage Gen2 アカウントの名前を入力します。 ストレージ アカウントの命名規則の詳細については、「[Azure リソースの名前付け規則](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage)」を参照してください。
    1. **[詳細]** タブをクリックします。
    1. **[Data Lake Storage Gen2]** の下の **[階層構造の名前空間]** の横の **[有効]** をクリックします。
    1. **[Review + create]\(レビュー + 作成\)** をクリックします。
    1. **[作成]**

ストレージ アカウントを作成する場合のその他のオプションについては、「[Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](../storage/blobs/data-lake-storage-quickstart-create-account.md)」を参照してください。

![Azure portal でのストレージ アカウントの作成を示したスクリーンショット](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントにマネージド ID のアクセス許可を設定する

ストレージ アカウントの **[ストレージ BLOB データ所有者]** ロールにマネージド ID を割り当てます。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。
1. ストレージ アカウントを選択し、 **[アクセス制御 (IAM)]** を選択して、そのアカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
    
    ![ストレージのアクセス制御設定を示すスクリーンショット](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)
    
1. **[+ ロールの割り当ての追加]** ボタンを選択して新しいロールを追加します。
1. **[ロールの割り当ての追加]** ウィンドウで、 **[ストレージ BLOB データ所有者]** ロールを選択します。 次に、マネージド ID とストレージ アカウントを持つサブスクリプションを選択します。 次に、以前作成したユーザー割り当てマネージド ID を検索して見つけます。 最後に、マネージド ID を選択すると、その ID が **[選択したメンバー]** の下に一覧表示されます。
    
    ![RBAC ロールの割り当て方法を示すスクリーンショット](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)
    
1. **[保存]** を選択します。 選択したユーザー割り当て ID が、選択されたロールの下に表示されるようになります。
1. この初期セットアップを完了すると、ポータルを通じてクラスターを作成できます。 クラスターは、ストレージ アカウントと同じ Azure リージョンに存在する必要があります。 クラスターの作成メニューの **[ストレージ]** セクションで、次のオプションを選択します。
        
    * **[プライマリ ストレージの種類]** で、 **[Azure Data Lake Storage Gen2]** を選択します。
    * **[ストレージ アカウントの選択]** から、新しく作成した Data Lake Storage Gen2 ストレージ アカウントを探して選択します。
        
        ![Azure HDInsight で Data Lake Storage Gen2 を使用するためのストレージ設定](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * **[ID]** から、正しいサブスクリプションと新しく作成されたユーザー割り当てマネージド ID を選択します。

        ![HDInsight で Data Lake Storage Gen2 を使用するための ID 設定](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)

> [!Note]
> セカンダリ Data Lake Storage Gen2 アカウントを追加するには、ストレージ アカウント レベルで、追加する新しい Data Lake Storage Gen2 ストレージ アカウントに以前に作成したマネージド ID を割り当てます。セカンダリ Data Lake Storage Gen2 アカウントを HDInsight 上の「追加のストレージ アカウント」 ブレードを使用して追加することはサポートされませんのでご注意ください。 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Data Lake Storage Gen2 を使用して Azure CLI からクラスターを作成する

[サンプルのテンプレート ファイルをダウンロード](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)して、[サンプルのパラメーター ファイルをダウンロード](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)できます。 このテンプレートと以下の Azure CLI のコード スニペットを使用する前に、次のプレース ホルダーを正しい値に置き換えます。

| プレースホルダー | 説明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure サブスクリプションの ID です |
| `<RESOURCEGROUPNAME>` | 新しいクラスターとストレージ アカウントを作成するリソース グループです。 |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage Gen2 アカウントに対するアクセス許可を付与するマネージド ID の名前です。 |
| `<STORAGEACCOUNTNAME>` | 作成される新しい Azure Data Lake Storage Gen2 アカウントです。 |
| `<CLUSTERNAME>` | HDInsight クラスターの名前です。 |
| `<PASSWORD>` | SSH と Ambari ダッシュ ボードを使用してクラスターにサインインするために選択したパスワードです。 |

以下のコード スニペットでは、次の初期手順が実行されます。

1. Azure アカウントにログインします。
1. 作成操作が実行されるアクティブなサブスクリプションを設定します。
1. 新しいデプロイ アクティビティ用の新しいリソース グループを作成します。 
1. ユーザー割り当てマネージド ID を作成します。
1. Data Lake Storage Gen2 の機能を使用するために、Azure CLI に拡張機能を追加します。
1. `--hierarchical-namespace true` フラグを使用して、新しい Data Lake Storage Gen2 アカウントを作成します。 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

次に、ポータルにサインインします。 [ Azure Portal の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)に関するページの手順 3 の説明に従って、ストレージ アカウントの **[ストレージ BLOB データ共同作成者]** ロールに新しいユーザー割り当てマネージド ID を追加します。

ユーザー割り当てマネージド ID のロールを割り当てたら、次のコード スニペットを使用してテンプレートをデプロイします。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight での Data Lake Storage Gen2 のアクセス制御

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 をサポートするアクセス許可の種類

Data Lake Storage Gen2 では、ロール ベースのアクセス制御 (RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートするアクセス制御モデルを使用します。 Data Lake Storage Gen1 では、データへのアクセス制御の場合のみアクセス制御リストがサポートされていました。

RBAC では、ロールの割り当てを使用して、Azure リソースのユーザー、グループ、サービス プリンシパルにアクセス許可のセットを効果的に適用します。 通常、これらの Azure リソースは、最上位のリソース (例: Azure のストレージ アカウント) に制約されます。 Azure Storage および Data Lake Storage Gen2 の場合、このメカニズムがファイル システムのリソースにまで拡張されています。

 RBAC を使用したファイルのアクセス許可の詳細については、「[Azure のロールベースのアクセス制御 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)」を参照してください。

ACL を使用したファイルのアクセス許可の詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で自分のデータへのアクセスを制御する方法

Data Lake Storage Gen2 で HDInsight クラスターがファイルにアクセスする機能は、マネージド ID によって制御されます。 マネージド ID とは、資格情報が Azure によって管理されている Azure Active Directory (Azure AD) に登録されている ID です。 マネージド ID があれば、Azure AD にサービス プリンシパルを登録して証明書などの資格情報を維持する必要はありません。

Azure サービスには、システム割り当てとユーザー割り当ての 2 種類のマネージド ID があります。 HDInsight は、ユーザー割り当てマネージド ID を使用して Data Lake Storage Gen2 にアクセスします。 ユーザー割り当てマネージド ID は、スタンドアロン Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。

ユーザー割り当て ID のライフサイクルは、その ID が割り当てられている Azure サービス インスタンスのライフサイクルとは個別に管理されます。 マネージド ID の詳細については、「[Azure リソースのマネージド ID のしくみ](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)」を参照してください。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hive またはその他のサービスを使用して Data Lake Storage Gen2 でデータをクエリするためのアクセス許可を Azure AD ユーザーに設定する方法

データをクエリするためのアクセス許可をユーザーに設定するには、ACL で割り当て済みのプリンシパルとして Azure AD セキュリティ グループを使用します。 個々のユーザーまたはサービス プリンシパルにファイルのアクセス許可を直接割り当てないでください。 Azure AD セキュリティ グループを使用してアクセス許可のフローを制御すると、ACL をディレクトリ構造全体に再適用することなく、ユーザーまたはサービス プリンシパルを追加および削除できます。 適切な Azure AD セキュリティ グループからユーザーまたはサービス プリンシパルを追加または削除するだけです。 ACL は継承されないため、ACL を再適用するには、すべてのファイルとサブディレクトリで ACL を更新する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight と Data Lake Storage Gen2 プレビューとの統合 - ACL とセキュリティ更新プログラム](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
