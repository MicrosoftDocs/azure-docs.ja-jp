---
title: Azure Machine Learning Studio を仮想ネットワークで利用可能にする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオを構成して、仮想ネットワーク内に格納されているデータにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: da8007a651b62430055f263f082fabf2aa4bf610
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574290"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 仮想ネットワークで Azure Machine Learning Studio を使用する

この記事では、仮想ネットワークで Azure Machine Learning Studio を使用する方法について説明します。 Studio には、AutoML、デザイナー、データのラベル付けなどの機能が含まれています。 仮想ネットワークでこれらの機能を使用するには、この記事の手順に従う必要があります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> - 仮想ネットワーク内に格納されているデータへのアクセス権を Studio に付与します。
> - 仮想ネットワーク内のリソースから Studio にアクセスします。
> - Studio によるストレージのセキュリティへの影響について理解します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートからなるシリーズのパート 5 です。 前のパートを読んで、仮想ネットワーク環境を設定することを強くお勧めします。

このシリーズの他の記事は次のとおりです。

[1.VNet の概要](how-to-network-security-overview.md) > [2.ワークスペースをセキュリティで保護する](how-to-secure-workspace-vnet.md) > [3.トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md) > [4.推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md) > **5.Studio の機能を有効にする**


> [!IMPORTANT]
> ワークスペースが Azure Government や Azure China 21Vianet などの __ソブリン クラウド__ にある場合、統合ノートブックでは仮想ネットワーク内のストレージを使用 "_できません_"。 代わりに、コンピューティング インスタンスから Jupyter Notebook を使用できます。 詳細については、「[コンピューティング インスタンス ノートブック内のデータにアクセスする](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook)」のセクションを参照してください。

## <a name="prerequisites"></a>前提条件

+ [ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照して、一般的な仮想ネットワークのシナリオとアーキテクチャについて理解してください。

+ 使用する仮想ネットワークとサブネットが既に存在すること。

+ [Private Link が有効になっている Azure Machine Learning ワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) (既存)。

+ [仮想ネットワークに追加された Azure ストレージ アカウント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) (既存)。

## <a name="configure-data-access-in-the-studio"></a>Studio でデータ アクセスを構成する

仮想ネットワークでは、Studio の機能の一部が既定で無効になっています。 これらの機能を再度有効にするには、Studio で使用する予定のストレージ アカウントのマネージド ID を有効にする必要があります。 

仮想ネットワークでは、次の操作が既定で無効になっています。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* デザイナーでのモデルのデプロイ ([既定のストレージ アカウント](#enable-managed-identity-authentication-for-default-storage-accounts))。
* AutoML 実験の送信 ([既定のストレージ アカウント](#enable-managed-identity-authentication-for-default-storage-accounts))。
* ラベル付けプロジェクトの開始

スタジオでは、仮想ネットワーク内の次のデータストアの種類からのデータの読み取りがサポートされています。

* Azure BLOB
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL データベース

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>ワークスペースのマネージド ID を使用するようにデータストアを構成する

[サービス エンドポイント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)または[プライベート エンドポイント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)のいずれかが設定されている仮想ネットワークに Azure ストレージ アカウントを追加した後、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) 認証を使用するようにデータストアを構成する必要があります。 これにより、Studio でストレージ アカウント内のデータにアクセスできるようになります。

Azure Machine Learning では、[データストア](concept-data.md#datastores)を使用してストレージ アカウントに接続します。 マネージド ID を使用するようにデータストアを構成するには、次の手順に従います。

1. スタジオで、 __[データストア]__ を選択します。

1. 既存のデータストアを更新する場合は、そのデータストアを選択して __[資格情報の更新]__ を選択します。

    新しいデータストアを作成する場合は、 __[+ 新しいデータストア]__ を選択します。

1. データストアの設定で、 __[Azure Machine Learning Studio でのデータのプレビューとプロファイルにワークスペースのマネージド ID を使用する]__ で __[はい]__ を選択します。

    ![ワークスペースのマネージド ID を有効にする方法を示すスクリーンショット](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

この手順により、Azure RBAC を使用して、ワークスペースのマネージド ID がストレージ サービスに __閲覧者__ として追加されます。 __閲覧者__ のアクセス権を指定することにより、ワークスペースはファイアウォールの設定を取得し、データが仮想ネットワークから離れないようにすることができます。 変更が有効になるまでに最大 10 分かかる場合があります。

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>既定のストレージ アカウントでマネージド ID 認証を有効にする

各 Azure Machine Learning ワークスペースには、既定の BLOB ストレージ アカウントと既定のファイル ストア アカウントの 2 つの既定のストレージ アカウントがあり、これらはワークスペースの作成時に定義されます。 **データストア** 管理ページで新しい既定値を設定することもできます。

![既定のデータストアがある場所を示すスクリーンショット](./media/how-to-enable-studio-virtual-network/default-datastores.png)

次の表では、ワークスペースの既定のストレージ アカウントでマネージド ID 認証を有効にする必要がある理由について説明します。

|ストレージ アカウント  | Notes  |
|---------|---------|
|ワークスペースの既定の BLOB ストレージ| デザイナーからのモデル アセットが格納されます。 デザイナーでモデルをデプロイするには、このストレージ アカウントでマネージド ID 認証を有効にする必要があります。 <br> <br> マネージド ID を使用するように構成されている既定以外のデータストアを使用するデザイナー パイプラインは、視覚化して実行できます。 ただし、既定のデータストアでマネージド ID を有効にせずにトレーニング済みのモデルをデプロイしようとすると、他のデータストアの使用に関係なく、デプロイは失敗します。|
|ワークスペースの既定のファイル ストア| AutoML 実験アセットが格納されます。 AutoML 実験を送信するには、このストレージ アカウントでマネージド ID 認証を有効にする必要があります。 |

> [!WARNING]
> 既定のファイル ストアでは、AutoML 実験を送信するために必要な `azureml-filestore` フォルダーが自動的に作成されないという既知の問題があります。 このエラーは、ワークスペースの作成時に、ユーザーが既存のファイルストアを既定のファイルストアとして設定した場合に発生します。
> 
> この問題を回避するには、次の 2 とおりの方法があります。1) ワークスペースの作成時に自動的に作成される既定のファイルストアを使用します。 2) 独自のファイルストアを使用するには、ワークスペースの作成時にそのファイルストアが VNet の外部にあることを確認します。 ワークスペースが作成されたら、仮想ネットワークにストレージ アカウントを追加します。
>
> この問題を解決するには、仮想ネットワークから ファイルストア アカウントを削除してから、仮想ネットワークに追加し直します。

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>ストレージ プライベート リンクにワークスペースのマネージド ID __閲覧者__ アクセス権を付与する

Azure ストレージ アカウントでプライベート エンドポイントを使用している場合は、プライベート リンクへの **閲覧者** アクセス権をワークスペースのマネージド ID に付与する必要があります。 詳細については、[閲覧者](../role-based-access-control/built-in-roles.md#reader)組み込みロールに関するページを参照してください。 

ストレージ アカウントでサービス エンドポイントを使用している場合は、この手順を省略できます。

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 内のリソースから Studio にアクセスする

仮想ネットワーク内のリソース (コンピューティング インスタンスや仮想マシンなど) からスタジオにアクセスする場合は、仮想ネットワークからスタジオへの送信トラフィックを許可する必要があります。 

たとえば、ネットワーク セキュリティ グループ (NSG) を使用して送信トラフィックを制限している場合は、__AzureFrontDoor.Frontend__ の __サービス タグ__ 宛先に規則を追加します。

## <a name="technical-notes-for-managed-identity"></a>マネージド ID のテクニカル ノート

ストレージ サービスへのアクセス時にマネージド ID を使用すると、セキュリティの考慮事項に影響します。 このセクションでは、ストレージ アカウントの種類ごとの変化について説明します。 

これらの考慮事項は、アクセスしている __ストレージ アカウントの種類__ によって異なります。

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

__Azure Blob ストレージ__ では、ワークスペース マネージド ID は [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)としても追加されるため、BLOB ストレージからデータを読み取ることができます。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 のアクセスの制御

仮想ネットワーク内のデータ アクセスの制御には、Azure RBAC と POSIX スタイルのアクセス制御リスト (ACL) の両方を使用できます。

Azure RBAC を使用するには、ワークスペース マネージド ID を [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)のロールに追加します。 詳細については、[Azure のロールベースのアクセス制御](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)に関するページを参照してください。

ACL を使用する場合は、他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にアクセス権を割り当てることができます。 詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 のアクセスの制御

Azure Data Lake Storage Gen1 では、POSIX スタイルのアクセス制御リストのみをサポートしています。 他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にリソースに対するアクセス権を割り当てることができます。 詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database 包含ユーザー

Azure SQL Database の格納データにマネージド ID を使用してアクセスするには、そのマネージド ID にマップする SQL 包含ユーザーを作成する必要があります。 外部プロバイダーからユーザーを作成する方法の詳細については、「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)」を参照してください。

SQL 包含ユーザーを作成したら、これに対してアクセス許可を付与するため、[GRANT T-SQL コマンド](/sql/t-sql/statements/grant-object-permissions-transact-sql)を使用します。

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Azure Machine Learning デザイナーの中間モジュール出力

デザイナーでは、任意のモジュールの出力場所を指定できます。 これは、セキュリティ、ログ、または監査の目的で、中間データセットを別の場所に格納する場合に使用します。 出力を指定するには、次のようにします。

1. 出力を指定するモジュールを選択します。
1. 右側に表示される [モジュール設定] ウィンドウで、 **[出力設定]** を選択します。
1. 各モジュールの出力に使用するデータストアを指定します。
 
仮想ネットワーク内の中間ストレージ アカウントにアクセスできることを確認してください。 そうでない場合、パイプラインは失敗します。

また、出力データを視覚化するために、中間ストレージ アカウントの[マネージド ID 認証を有効にする](#configure-datastores-to-use-workspace-managed-identity)必要があります。

## <a name="next-steps"></a>次のステップ

この記事は、全 5 パートからなる仮想ネットワーク シリーズのパート 5 です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 1: 仮想ネットワークの概要](how-to-network-security-overview.md)
* [パート 2: ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [パート 3: トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [パート 4: 推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)

名前の解決については、[カスタム DNS](how-to-custom-dns.md) の使用に関する記事も参照してください。