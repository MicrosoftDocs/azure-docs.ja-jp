---
title: ポータルでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure portal または Python 用 SDK を使用して、Azure Machine Learning ワークスペースを作成、表示、削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518927"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning ワークスペースを作成して管理する 

この記事では、Azure portal または [Python 用 SDK](/python/api/overview/azure/ml/) を使用して、[Azure Machine Learning](overview-what-is-azure-ml.md) 用の [**Azure Machine Learning ワークスペース**](concept-workspace.md)を作成、表示、および削除します。

ニーズに変化が生じたり自動化の要件が増えたりしたときに、[CLI](reference-azure-machine-learning-cli.md) または [VS Code 拡張機能](tutorial-setup-vscode-extension.md)を使用して、ワークスペースの作成と削除を行うこともできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。
* Python SDK を使用する場合は、[その SDK をインストール](/python/api/overview/azure/ml/install)します。

## <a name="limitations"></a>制限事項

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

既定では、ワークスペースを作成すると Azure Container Registry (ACR) も作成されます。  現在、ACR ではリソース グループ名での Unicode 文字がサポートされていないため、これらの文字が含まれていないリソース グループを使用します。

## <a name="create-a-workspace"></a>ワークスペースの作成

# <a name="python"></a>[Python](#tab/python)

* **既定の仕様。** 既定では、依存するリソースとリソース グループが自動的に作成されます。 このコードでは、`myworkspace` という名前のワークスペースと `myresourcegroup` という名前のリソース グループが `eastus2` に作成されます。
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    ワークスペースで使用する既存の Azure リソース グループがある場合は、`create_resource_group` を False に設定します。

* <a name="create-multi-tenant"></a>**複数のテナント。**  複数のアカウントがある場合は、使用する Azure Active Directory のテナント ID を追加します。  [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]、[外部 ID]** からテナント ID を見つけます。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[ソブリン クラウド](reference-machine-learning-cloud-parity.md)** 。 ソブリン クラウドで作業している場合は、Azure に対して認証するための追加のコードが必要になります。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **既存の Azure リソースを使用する**。  既存の Azure リソースを Azure リソース ID 形式で使用するワークスペースを作成することもできます。 Azure portal または SDK を使用して、特定の Azure リソース ID を見つけます。 この例では、リソース グループ、ストレージ アカウント、キー コンテナー、App Insights、およびコンテナー レジストリが既に存在していることを前提としています。

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

詳細については、[ワークスペース SDK リファレンス](/python/api/azureml-core/azureml.core.workspace.workspace)を参照してください。

サブスクリプションへのアクセスで問題が発生した場合は、[Azure Machine Learning のリソースとワークフローのための認証の設定](how-to-setup-authentication.md)に関する記事と「[Azure Machine Learning での認証](https://aka.ms/aml-notebook-auth)」ノートブックを参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ご利用の Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。

      ![新しいリソースを作成](./media/how-to-manage-workspace/create-workspace.gif)

1. 検索バーを使用して、**Machine Learning** を見つけます。

1. **[Machine Learning]** を選択します。

1. **[Machine Learning]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明 
   ---|---
   ワークスペース名 |ワークスペースを識別する一意の名前を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。 ワークスペース名では、大文字と小文字は区別されません。
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 既存のリソース グループを使用するには、*共同作成者* または *所有者* のロールが必要です。  アクセスの詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」の記事をご覧ください。
   リージョン | ユーザーとデータ リソースに最も近い Azure リージョンを選択し、ワークスペースを作成します。
   | ストレージ アカウント | ワークスペースの既定のストレージ アカウント。 既定では、新しいものが作成されます。 |
   | Key Vault | ワークスペースで使用される Azure Key Vault。 既定では、新しいものが作成されます。 |
   | Application Insights | ワークスペースの Application Insights インスタンス。 既定では、新しいものが作成されます。 |
   | Container Registry | ワークスペースの Azure Container Registry。 既定では、新しいものは、ワークスペース用に最初に作成されたものでは "_ありません_"。 これは、トレーニングまたはデプロイ中に Docker イメージを作成するときに必要になったときに作成されます。 |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="ワークスペースを構成する。":::

1. ワークスペースの構成が完了したら、 **[確認および作成]** を選択します。 必要に応じて、[ネットワーク](#networking)に関するセクションと[詳細設定](#advanced)に関するセクションを使用し、ワークスペースに対してさらに多くの設定を行います。

1. 設定を確認し、追加の変更または訂正があれば実行します。 設定に問題がなければ、 **[作成]** を選択します。

   > [!Warning] 
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。 
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。
 
---



### <a name="networking"></a>ネットワーク  

> [!IMPORTANT]  
> お使いのワークスペースでのプライベート エンドポイントと仮想ネットワークの使用の詳細については、[ネットワークの分離とプライバシー](how-to-network-security-overview.md)に関する記事を参照してください。


# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK には、[PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) クラスが用意されています。これを [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) で使用すると、プライベート エンドポイントを使用してワークスペースを作成できます。 このクラスには、既存の仮想ネットワークが必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 既定のネットワーク構成では、公衆インターネット上の __パブリック エンドポイント__ を使用します。 作成した Azure Virtual Network にお使いのワークスペースへのアクセスを制限するには、代わりに __[接続方法]__ として __[プライベート エンドポイント]__ (プレビュー) を選択し、 __[+ 追加]__ を使用してそのエンドポイントを構成します。   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="プライベート エンドポイントを選択する":::  

1. __[プライベート エンドポイントの作成]__ フォームで、使用する場所、名前、仮想ネットワークを設定します。 そのエンドポイントをプライベート DNS ゾーンと共に使用する場合は、 __[プライベート DNS ゾーンと統合する]__ を選択し、 __[プライベート DNS ゾーン]__ フィールドを使用してそのゾーンを選択します。 __[OK]__ を選択してエンドポイントを作成します。   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="プライベート エンドポイントを作成する":::   

1. ネットワークの構成が完了したら、 __[確認および作成]__ を選択するか、オプションの __[詳細]__ 構成に進みます。

---

> [!IMPORTANT]  
> Azure Machine Learning ワークスペースでのプライベート エンドポイントの使用は、現在パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。     
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

### <a name="multiple-workspaces-with-private-endpoint"></a>プライベート エンドポイントを使用する複数のワークスペース

プライベート エンドポイントを作成すると、__privatelink.api.azureml.ms__ という名前の新しいプライベート DNS ゾーンが作成されます。 これには、仮想ネットワークへのリンクが含まれます。 プライベート エンドポイントがあるワークスペースを同じリソース グループ内に複数作成した場合、最初のプライベート エンドポイントの仮想ネットワークしか DNS ゾーンに追加されない場合があります。 追加のワークスペースまたはプライベート エンドポイントで使用される仮想ネットワークを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、そのワークスペースが含まれているリソース グループを選択します。 次に、__privatelink.api.azureml.ms__ という名前のプライベート DNS ゾーン リソースを選択します。
2. __[設定]__ で、 __[仮想ネットワーク リンク]__ を選択します。
3. __[追加]__ を選択します。 __[仮想ネットワーク リンクの追加]__ ページで一意の __[リンク名]__ を指定し、次いで追加する __仮想ネットワーク__ を選択します。 __[OK]__ を選択して、ネットワーク リンクを追加します。

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください。

### <a name="vulnerability-scanning"></a>脆弱性のスキャン

Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Azure Security Center に対して、リソースのスキャンと推奨事項の順守を許可する必要があります。 詳細については、「[Security Center による Azure Container Registry のイメージ スキャン](../security-center/defender-for-container-registries-introduction.md)」および「[Azure Kubernetes Service と Security Center の統合](../security-center/defender-for-kubernetes-introduction.md)」を参照してください。

### <a name="advanced"></a>詳細設定

ワークスペースのメタデータは、既定で Microsoft が管理する Azure Cosmos DB インスタンスに格納されます。 このデータは Microsoft のマネージド キーで暗号化されます。

Microsoft がお使いのワークスペースで収集するデータを制限するには、ポータルで __[High business impact workspace]\(業務への影響が大きいワークスペース\)__ を選択するか、Python で `hbi_workspace=true ` を設定します。 この設定の詳細については、「[保存時の暗号化](concept-data-encryption.md#encryption-at-rest)」を参照してください。

> [!IMPORTANT]  
> High Business Impact の選択は、ワークスペースの作成時にのみ実行できます。 ワークスペースの作成後にこの設定を変更することはできません。   

#### <a name="use-your-own-key"></a>独自のキーを使用する

データ暗号化用の独自のキーを用意できます。 これを行うと、自分の Azure サブスクリプションにメタデータを格納する Azure Cosmos DB インスタンスが作成されます。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

独自のキーを用意するには、次の手順を実行します。

> [!IMPORTANT]  
> これらの手順を実行する前に、まず次のアクションを実行する必要があります。   
>
> 1. サブスクリプションに対する共同作成者のアクセス許可を使用して、__Machine Learning アプリ__ (ID とアクセスの管理) を承認します。  
> 1. [カスタマー マネージド キーの構成](../cosmos-db/how-to-setup-cmk.md)に関する記事の手順に従って以下を行います。
>     * Azure Cosmos DB プロバイダーを登録する
>     * Azure Key Vault を作成して構成する
>     * キーを生成する
>   
>     Azure Cosmos DB インスタンスを手動で作成する必要はありません。ワークスペースの作成時に作成されます。 この Azure Cosmos DB インスタンスは、`<your-workspace-resource-name>_<GUID>` というパターンに基づく名前を使用して、別のリソース グループ内に作成されます。   
>   
> ワークスペースの作成後にこの設定を変更することはできません。 ワークスペースによって使用されている Azure Cosmos DB を削除する場合は、それを使用しているワークスペースも削除する必要があります。

# <a name="python"></a>[Python](#tab/python)

`cmk_keyvault` と `resource_cmk_uri` を使用して、カスタマー マネージド キーを指定します。

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. __[カスタマー マネージド キー]__ を選択し、 __[クリックしてキーを選択します]__ を選択します。

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="カスタマー マネージド キー":::。

1. __[Azure Key Vault からのキーの選択]__ フォームから既存の Azure Key Vault、それに含まれているキー、およびそのキーのバージョンを選択します。 このキーは、Azure Cosmos DB に格納されているデータを暗号化するために使用されます。 最後に、 __[選択]__ ボタンをクリックして、このキーが使用されるようにします。

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="キーを選択する":::

---

### <a name="download-a-configuration-file"></a>構成ファイルをダウンロードする

[コンピューティング インスタンス](tutorial-1st-experiment-sdk-setup.md#azure)を作成する場合は、この手順をスキップしてください。  このファイルのコピーは、コンピューティング インスタンスによって既に作成されています。

# <a name="python"></a>[Python](#tab/python)

このワークスペース (`ws`) を参照するローカル環境でコードを使用する場合は、構成ファイルを記述します。

```python
ws.write_config()
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

このワークスペースを参照するローカル環境でコードを使用する場合は、ワークスペースの **[概要]** セクションから **[config. json をダウンロード]** を選択します。  

   ![config.json をダウンロードする](./media/how-to-manage-workspace/configure.png)

---

このファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内に置きます。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。 コンピューティング インスタンスを作成するとき、このファイルは VM 上の正しいディレクトリに自動的に追加されます。

## <a name="connect-to-a-workspace"></a>ワークスペースに接続する

Python コードで、ワークスペースに接続するワークスペース オブジェクトを作成します。  このコードにより、構成ファイルの内容が読み取られ、ワークスペースが検索されます。  まだ認証されていない場合は、サインインを求めるメッセージが表示されます。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**複数のテナント。**  複数のアカウントがある場合は、使用する Azure Active Directory のテナント ID を追加します。  [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]、[外部 ID]** からテナント ID を見つけます。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[ソブリン クラウド](reference-machine-learning-cloud-parity.md)** 。 ソブリン クラウドで作業している場合は、Azure に対して認証するための追加のコードが必要になります。

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
サブスクリプションへのアクセスで問題が発生した場合は、[Azure Machine Learning のリソースとワークフローのための認証の設定](how-to-setup-authentication.md)に関する記事と「[Azure Machine Learning での認証](https://aka.ms/aml-notebook-auth)」ノートブックを参照してください。

## <a name="find-a-workspace"></a><a name="view"></a>ワークスペースの検索

使用できるすべてのワークスペースの一覧を表示します。

# <a name="python"></a>[Python](#tab/python)

[Azure portal の [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、サブスクリプションを見つけます。  ID をコピーして下のコードで使用して、そのサブスクリプションで使用可能なすべてのワークスペースを確認します。

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 上部の検索フィールドに「**Machine Learning**」と入力します。  

1. **[Machine Learning]** を選択します。

   ![Azure Machine Learning ワークスペースの検索](./media/how-to-manage-workspace/find-workspaces.png)

1. 見つかったワークスペースの一覧にひととおり確認します。 サブスクリプション、リソース グループ、場所に基づいてフィルター処理できます。  

1. ワークスペースを選択して、そのプロパティを表示します。

---


## <a name="delete-a-workspace"></a>ワークスペースを削除する

ワークスペースが不要になったら、削除します。  

# <a name="python"></a>[Python](#tab/python)

ワークスペースを削除する`ws`:

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

既定のアクションでは、ワークスペースに関連付けられているリソース (コンテナー レジストリ、ストレージ アカウント、キー コンテナー、Application Insights) は削除されません。  これらのリソースも削除するには、`delete_dependent_resources` を True に設定します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure portal](https://portal.azure.com/) で、削除するワークスペースの上部にある **[削除]** を選択します。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="ワークスペースの削除":::

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **Azure Machine Learning スタジオでサポートされているブラウザー**:オペレーティング システムと互換性のある最新ブラウザーを使うことをお勧めします。 次のブラウザーがサポートされています。
  * Microsoft Edge (新しい Microsoft Edge の最新バージョンです。 Microsoft Edge レガシではありません)。
  * Safari (最新バージョン、Mac のみ)
  * Chrome (最新バージョン)
  * Firefox (最新バージョン)

* **Azure ポータル**: 
  * SDK または Azure portal で共有リンクからワークスペースに直接移動する場合、拡張機能のサブスクリプション情報を含む標準の **[概要]** ページは表示できません。 このシナリオでは、別のワークスペースに切り替えることもできません。 別のワークスペースを表示するには、[Azure Machine Learning Studio](https://ml.azure.com) に直接移動し、そのワークスペース名を検索します。
  * すべての資産 (データセット、実験、コンピューティングなど) は、[Azure Machine Learning Studio](https://ml.azure.com) でのみ使用できます。 Azure portal から使用することは "*できません*"。

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>例

ワークスペースの作成例:
* Azure portal を使用して、[ワークスペースとコンピューティング インスタンスを作成する](tutorial-1st-experiment-sdk-setup.md)
* Python SDK を使用して、[独自の環境内にワークスペースを作成する](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>次のステップ

ワークスペースを作成したら、[モデルをトレーニングしてデプロイする](tutorial-train-models-with-aml.md)方法を確認します。