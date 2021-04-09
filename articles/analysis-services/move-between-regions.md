---
title: Azure Analysis Services を別のリージョンに移動する | Microsoft Docs
description: Azure Analysis Services リソースを別のリージョンに移動する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96497930"
---
# <a name="move-analysis-services-to-a-different-region"></a>Analysis Services を別のリージョンに移動する

この記事では、Analysis Services サーバー リソースを別の Azure リージョンに移動する方法について説明します。 サーバーを別のリージョンに移動する理由はさまざまで、たとえば、ユーザーに近い Azure リージョンを利用する、特定のリージョンでのみサポートされているサービス プランを使用する、内部のポリシーとガバナンスの要件を満たすため、などがあります。 

この記事と関連するリンク先の記事で、次の方法について説明します。

> [!div class="checklist"]
> 
> * ソース サーバーのモデル データベースを [BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)にバックアップします。
> * ソース サーバーの[リソース テンプレート](../azure-resource-manager/templates/overview.md)をエクスポートします。
> * ストレージの [Shared Access Signature (SAS)](../storage/common/storage-sas-overview.md) を取得します。
> * リソース テンプレートを変更します。
> * テンプレートをデプロイして、新しいターゲット サーバーを作成します。
> * 新しいターゲット サーバーにモデル データベースを復元します。
> * 新しいターゲット サーバーとデータベースを確認します。
> * ソース サーバーを削除します。

この記事では、リソース テンプレートを使用して、**基本構成** を持つ単一の Analysis Services サーバーを、同じサブスクリプション内の別のリージョン "*および*" リソース グループに移行する方法について説明します。 テンプレートを使用すると、構成済みのサーバー プロパティが保持され、ターゲット サーバーが、同じプロパティ (リージョンとリソース グループは除く) を使用して、ソース サーバーとして構成されることが確実になります。 この記事では、データ ソース、ストレージ、ゲートウェイ リソースなど、同じリソース グループに属する可能性がある関連リソースの移動については説明しません。 

サーバーを別のリージョンに移動する前に、詳細なプランを作成することをお勧めします。 ゲートウェイやストレージなど、他にも移動が必要になる可能性があるリソースを検討します。 いずれのプランでも、運用サーバーを移動する前に、テスト サーバーを使用して 1 つ以上の試用移動操作を完了することが重要です。

> [!IMPORTANT]
> クライアント アプリケーションと接続文字列は、完全なサーバー名 (サーバーが存在するリージョンが含まれる URI) を使用して Analysis Services に接続します。 たとえば、「 `asazure://westcentralus.asazure.windows.net/advworks01` 」のように入力します。 サーバーを別のリージョンに移動する場合は、別のリージョンに新しいサーバー リソースを作成するのが効果的です。別のリージョンがそのサーバー名の URI に含まれるようになります。 スクリプトで使用するクライアント アプリケーションと接続文字列は、新しいサーバー名の URI を使用して新しいサーバーに接続される必要があります。 [サーバー名のエイリアス](analysis-services-server-alias.md)を使用すると、サーバー名の URI を変更する必要がある場所を減らすことができますが、リージョン移動の前に実装する必要があります。

> [!IMPORTANT]
> Azure リージョンにより、異なる IP アドレス範囲が使用されます。 サーバーやストレージ アカウントが含まれているリージョンに対してファイアウォールの例外が構成されている場合は、別の IP アドレス範囲の構成が必要になる場合があります。 詳細については、「[Analysis Services ネットワーク接続についてよく寄せられる質問](analysis-services-network-faq.md)」を参照してください。

> [!NOTE]
> この記事では、ソース サーバーのリージョンにあるストレージ コンテナーからターゲット サーバーへのデータベース バックアップの復元について説明します。 場合によっては、別のリージョンからバックアップを復元すると、特に大規模なデータベースの場合にパフォーマンスが低下することがあります。 データベースの復元中に最適なパフォーマンスを得るには、ターゲット サーバー リージョンに新しいストレージ コンテナーを移行または作成します。 ターゲット サーバーにデータベースを復元する前に、ソース リージョンのストレージ コンテナーからターゲット リージョンのストレージ コンテナーに .abf バックアップ ファイルをコピーします。 この記事の範囲からは外れますが、特に非常に大規模なデータベースでは、ソース サーバーからデータベースをスクリプト化し、再作成した後、ターゲット サーバーで処理して、データベース データを読み込む方が、バックアップまたは復元を使用するよりもコスト効率が向上する場合があります。

> [!NOTE]
> オンプレミス データ ゲートウェイを使用してデータ ソースに接続する場合は、ゲートウェイ リソースもターゲット サーバー リージョンに移動させる必要があります。 詳細については、「[オンプレミスのデータ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure ストレージ アカウント**:.abf バックアップ ファイルを格納するために必要です。
- **SQL Server Management Studio (SSMS)** :モデル データベースをバックアップおよび復元するために必要です。
- **Azure PowerShell**。 このタスクの完了に PowerShell を使用するように選択した場合にのみ必要です。

## <a name="prepare"></a>準備

### <a name="backup-model-databases"></a>モデル データベースをバックアップする

**ストレージ設定** がまだソース サーバー用に構成されていない場合は、「[ストレージ設定の構成](analysis-services-backup.md#configure-storage-settings)」の手順に従ってください。

ストレージ設定が構成されたら、「[バックアップ](analysis-services-backup.md#backup)」の手順に従って、ストレージ コンテナーにモデル データベースの .abf バックアップを作成します。 .abf バックアップは、後で新しいターゲット サーバーに復元します。


### <a name="export-template"></a>テンプレートをエクスポートする

このテンプレートには、ソース サーバーの構成プロパティが含まれています。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してテンプレートをエクスポートするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[すべてのリソース]** を選択して、Analysis Services サーバーを選択します。

3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。

4. **[テンプレートのエクスポート]** ブレードで **[ダウンロード]** を選択します。

5. ポータルからダウンロードした .zip ファイルを見つけたら、そのファイルをフォルダーに解凍します。

   zip ファイルには、新しいサーバーのデプロイに必要なテンプレートとパラメーターで構成される json ファイルが含まれています。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してテンプレートをエクスポートするには:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、移動するサーバー リソースのサブスクリプションに設定します。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. ソース サーバーのテンプレートをエクスポートします。 これらのコマンドによって、ResourceGroupName がファイル名に含まれる json テンプレートが現在のディレクトリに保存されます。

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>ストレージの Shared Access Signature (SAS) を取得する

テンプレートからターゲット サーバーをデプロイする場合は、データベース バックアップが含まれるストレージ コンテナーを指定するために、ユーザー委任の SAS トークンを URI として指定する必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルを使用して Shared Access Signature を取得するには:

1. ポータルで、サーバー データベースのバックアップに使用するストレージ アカウントを選択します。

2. **[Storage Explorer]** を選択し、 **[BLOB コンテナー]** を展開します。 

3. ストレージ コンテナーを右クリックしてから、 **[Shared Access Signature の取得]** を選択します。

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS の取得":::

4. **[Shared Access Signature]** で、 **[作成]** を選択します。 既定では、SAS は 24 時間で有効期限が切れます。

5. **URI** をコピーして保存します。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して Shared Access Signature を取得するには、「[PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)」の手順に従います。

---

### <a name="modify-the-template"></a>テンプレートの変更

テキスト エディターを使用して、エクスポートしたテンプレートの json ファイルを変更し、リージョンおよび BLOB コンテナーのプロパティを変更します。 

テンプレートを変更するには:

1. テキスト エディターを使い、**location** プロパティで新しいターゲット リージョンを指定します。 **backupBlobContainerUri** プロパティで、SAS キーを使用してストレージ コンテナーの URI を貼り付けます。 

    次の例では、サーバー advworks1 のターゲット リージョンを `South Central US` に設定し、Shared Access Signature を使用してストレージ コンテナーの URI を指定します。 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. テンプレートを保存します。

#### <a name="regions"></a>リージョン

Azure リージョンを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページをご覧ください。 PowerShell を使用してリージョンを取得するには、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを実行します。

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>詳細ビュー

新しいサーバー リソースを別のリージョンにデプロイするには、前のセクションでエクスポートして変更した **template.json** ファイルを使用します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ポータルで **[リソースの作成]** を選択します。

2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。

3. **[テンプレートのデプロイ]** を選択します。

4. **［作成］** を選択します

5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。

6. **[ファイルの読み込み]** を選択し、手順に従って、エクスポートして変更した **template.json** ファイルを読み込みます。

7. テンプレート エディターに新しいターゲット サーバーの正しいプロパティが表示されていることを確認します。

8. **[保存]** を選択します。

9. プロパティ値を入力または選択します。

    - **サブスクリプション**:Azure サブスクリプションを選択します。
    
    - **[リソース グループ]** : **[新規作成]** を選択して、リソース グループ名を入力します。 名前が同じ Analysis Services サーバーがまだ含まれていない場合は、既存のリソース グループを選択できます。
    
    - **[場所]** :テンプレートで指定したのと同じリージョンを選択します。

10. **[確認と作成]** を選択します。

11. 条件と基本を確認し、 **[作成]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

テンプレートをデプロイするには、次のコマンドを使用します。

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>ターゲット サーバーの URI を取得する

SSMS から新しいターゲット サーバーに接続してモデル データベースを復元するには、新しいターゲット サーバーの URI を取得する必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルでサーバー URI を取得するには:

1. ポータルで、新しいターゲット サーバー リソースに移動します。

2. **[概要]** ページで、 **[サーバー名]** の URI をコピーします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してサーバー URI を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
**ServerFullName** を出力からコピーします。

---

### <a name="restore-model-database"></a>モデル データベースを復元する

「[復元](analysis-services-backup.md#restore)」で説明されている手順に従って、モデル データベースの .abf バックアップを新しいターゲット サーバーに復元します。

省略可能:モデル データベースを復元した後、モデルとテーブルを処理してデータ ソースのデータを更新します。 SSMS を使用してモデルとテーブルを処理するには:

1. SSMS でモデル データベースを右クリックし、 **[データベースの処理]** をクリックします。

2. **[テーブル]** を展開し、テーブルを右クリックします。 **[テーブルの処理]** で、すべてのテーブルを選択し、 **[OK]** を選択します。

## <a name="verify"></a>確認

1. ポータルで、新しいターゲット サーバーに移動します。

2. [概要] ページの **[Analysis Services サーバー上のモデル]** で、復元されたモデルが表示されることを確認します。

3. Power BI や Excel などのクライアント アプリケーションを使用して、新しいサーバー上のモデルに接続します。 テーブル、メジャー、階層などのモデル オブジェクトが表示されることを確認します。 

4. 任意の自動化スクリプトを実行します。 正常に実行されたことを確認します。

省略可能:[ALM Toolkit](http://alm-toolkit.com/) は、Power BI データセット "*および*" Analysis Services 表形式モデル データベースの比較と管理を行うための "*オープン ソース*" ツールです。 このツールキットを使用して、ソース サーバーとターゲット サーバーの両方のデータベースに接続し、比較します。 データベースの移行が成功した場合、モデル オブジェクトの定義が同じになります。 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クライアント アプリケーションから新しいサーバーに接続でき、自動化スクリプトが正常に実行されていることを確認したら、ソース サーバーを削除します。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルからソース サーバーを削除するには:

ソース サーバーの **[概要]** ページで、 **[削除]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してソース サーバーを削除するには、Remove-AzAnalysisServicesServer コマンドを使用します。

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> リージョンの移動が完了したら、新しいターゲット サーバーでは、バックアップには、ソース サーバー リージョンのストレージ コンテナーではなく、同じリージョンのストレージ コンテナーを使用することをお勧めします。