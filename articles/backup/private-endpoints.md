---
title: プライベート エンドポイント
description: Azure Backup のプライベート エンドポイントを作成するプロセスと、プライベート エンドポイントを使用することでリソースのセキュリティが維持しやすくなるシナリオについて説明します。
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054874"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイント

Azure Backup で[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して、Recovery Services コンテナーから安全にデータをバックアップおよび復元できます。 プライベート エンドポイントによって、ご自分の VNet からの 1 つ以上のプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。

この記事は、Azure Backup のプライベート エンドポイントを作成するプロセスと、プライベート エンドポイントを使用することでリソースのセキュリティが維持しやすくなるシナリオについて理解するのに役立ちます。

## <a name="before-you-start"></a>開始する前に

- プライベート エンドポイントは、新しい Recovery Services コンテナー (そのコンテナーに登録されている項目がない) に対してのみ作成できます。 そのため、コンテナーに項目を保護する前に、プライベート エンドポイントを作成する必要があります。
- 1 つの仮想ネットワークに複数の Recovery Services コンテナーのプライベート エンドポイントを含めることができます。 また、1 つの Recovery Services コンテナーに対して複数の仮想ネットワークにプライベート エンドポイントを含めることができます。 ただし、1 つのコンテナーに対して作成できるプライベート エンドポイントの最大数は 12 です。
- コンテナーに対してプライベート エンドポイントが作成されると、コンテナーはロックダウンされます。 そのコンテナーのプライベート エンドポイントを含むネットワーク以外のネットワークからは、そのコンテナーに (バックアップと復元のために) アクセスできません。 コンテナーのすべてのプライベート エンドポイントが削除されると、そのコンテナーはすべてのネットワークからアクセスできるようになります。
- Backup 用のプライベート エンドポイント接続には、ストレージ用に Azure Backup で使用されるものなど、サブネットで合計 11 個のプライベート IP が使用されます。 一部の Azure リージョンでは、この数がさらに多い場合があります (最大 25)。 そのため、Backup 用のプライベート エンドポイントを作成する場合は、十分な数のプライベート IP を用意することをお勧めします。
- Recovery Services コンテナーは Azure Backup と Azure Site Recovery (の両方) に使用されますが、この記事では、Azure Backup だけのためにプライベート エンドポイントを使用する場合について説明します。
- Azure Active Directory では、現在、プライベート エンドポイントがサポートされていません。 したがって、Azure Active Directory がリージョンで機能するために必要な IP と FQDN には、Azure VM でのデータベースのバックアップおよび MARS エージェントを使用したバックアップを実行するときに、セキュリティで保護されたネットワークからの発信アクセスが許可される必要があります。 また、必要に応じて、NSG タグと Azure Firewall タグを使用して、Azure AD へのアクセスを許可することもできます。
- ネットワーク ポリシーが適用されている仮想ネットワークは、プライベート エンドポイント用にサポートされません。 続行する前に、ネットワーク ポリシーを無効にする必要があります。
- Recovery Services リソース プロバイダーをサブスクリプションに 2020 年 5 月 1 日より前に登録した場合は、再登録する必要があります。 プロバイダーを再登録するには、Azure portal のサブスクリプションに移動し、左側のナビゲーションバーで **[リソース プロバイダー]** に移動し、 **[Microsoft.RecoveryServices]** を選択し、 **[再登録]** を選択します。
- コンテナーでプライベート エンドポイントが有効になっている場合、SQL および SAP HANA データベース バックアップの[リージョンをまたがる復元](backup-create-rs-vault.md#set-cross-region-restore)はサポートされていません。

## <a name="recommended-and-supported-scenarios"></a>推奨されるシナリオとサポートされるシナリオ

プライベート エンドポイントは、コンテナーに対して有効になっている間、Azure VM と MARS エージェントのバックアップにおいてのみ、SQL および SAP HANA のワークロードのバックアップと復元に使用されます。 コンテナーは、他のワークロードのバックアップにも使用できます (ただし、プライベート エンドポイントは必要ありません)。 SQL および SAP HANA のワークロードのバックアップと、MARS エージェントを使用したバックアップに加えて、プライベート エンドポイントを使用して Azure VM バックアップ用のファイルの復旧を実行することもできます。 詳細については、後の表を参照してください。

| Azure VM でのワークロードのバックアップ (SQL、SAP HANA)、MARS エージェントを使用したバックアップ | プライベート エンドポイントの使用をお勧めします。これにより、ご自身の仮想ネットワークから、Azure Backup または Azure Storage のために IP と FQDN の許可リストを作成する必要なくバックアップと復元ができます。 このシナリオでは、SQL データベースをホストする VM から Azure AD の IP または FQDN に接続できることを確認します。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM バックアップ**                                         | VM バックアップでは、どの IP または FQDN へのアクセスも許可する必要はありません。 そのため、ディスクのバックアップと復元のためにプライベート エンドポイントは必要ありません。  <br><br>   ただし、プライベート エンドポイントを含むコンテナーからのファイルの復旧は、コンテナーのプライベート エンドポイントを含む仮想ネットワークに制限されます。 <br><br>    ACL に記載されたアンマネージド ディスクを使用する場合は、ディスクが格納されているストレージ アカウントで、**信頼された Microsoft サービス** へのアクセスが許可されていることを確認してください。 |
| **Azure Files バックアップ**                                      | Azure Files バックアップは、ローカル ストレージ アカウントに格納されます。 そのため、バックアップと復元にプライベート エンドポイントは必要ありません。 |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Backup のプライベート エンドポイントを作成して使用する

このセクションでは、ご自身の仮想ネットワーク内に Azure Backup 用のプライベート エンドポイントを作成して使用する手順を説明します。

>[!IMPORTANT]
> このドキュメントに示されているのと同じ順序で手順に従うことを強くお勧めします。 そうしないと、コンテナーにプライベート エンドポイントを使用するための互換性がなくなり、新しいコンテナーを使ってプロセスをやり直すことが必要になる可能性があります。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Azure Resource Manager クライアントを使用してコンテナーを作成する方法については、[こちらのセクション](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)を参照してください。 これにより、マネージド ID が既に有効になっているコンテナーが作成されます。 Recovery Services コンテナーの詳細については、[こちら](./backup-azure-recovery-services-vault-overview.md)を参照してください。

## <a name="enable-managed-identity-for-your-vault"></a>コンテナーのマネージド ID を有効にする

マネージド ID を使用すると、コンテナーでプライベート エンドポイントを作成して使用できます。 このセクションでは、ご自身のコンテナーのマネージド ID を有効にする方法について説明します。

1. ご自身の Recovery Services コンテナー > **[ID]** に移動します。

    ![ID の状態をオンに変更する](./media/private-endpoints/identity-status-on.png)

1. **[状態]** を **[On]** に変更し、 **[保存]** を選択します。

1. **オブジェクト ID** が生成されます。これがコンテナーのマネージド ID です。

    >[!NOTE]
    >マネージド ID は、いったん有効にしたら (一時的にであっても) 無効に **しないでください**。 マネージド ID を無効にすると、動作に一貫性がなくなる可能性があります。

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>必要なプライベート エンドポイントを作成するためのアクセス許可をコンテナーに付与する

Azure Backup に必要なプライベート エンドポイントを作成するには、コンテナー (コンテナーのマネージド ID) に次のリソース グループへのアクセス許可が必要です。

- 対象の VNet が含まれているリソース グループ
- プライベート エンドポイントが作成されるリソース グループ
- プライベート DNS ゾーンが含まれているリソース グループ (詳細については、[こちら](#creating-private-endpoints-for-backup)で説明しています)

これら 3 つのリソース グループの **共同作成者** ロールをコンテナー (マネージド ID) に付与することをお勧めします。 次の手順では、特定のリソース グループに対してこれを実行する方法について説明します (これは、3 つのリソース グループそれぞれに対して実行する必要があります)。

1. リソース グループに移動し、左側のバーの **[アクセス制御 (IAM)]** に移動します。
1. **[アクセス制御]** に移動したら、 **[ロールの割り当てを追加する]** に進みます。

    ![ロールの割り当てを追加する](./media/private-endpoints/add-role-assignment.png)

1. **[ロールの割り当ての追加]** ウィンドウで、 **[ロール]** として **[共同作成者]** を選択し、コンテナーの **名前** を **[プリンシパル]** として使用します。 コンテナーを選択し、完了したら **[保存]** を選択します。

    ![ロールとプリンシパルの選択](./media/private-endpoints/choose-role-and-principal.png)

より詳細なレベルでアクセス許可を管理するには、「[ロールとアクセス許可を手動で作成する](#create-roles-and-permissions-manually)」を参照してください。

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイントを作成して承認する

### <a name="creating-private-endpoints-for-backup"></a>Azure Backup のプライベート エンドポイントを作成する

このセクションでは、ご自身のコンテナーのプライベート エンドポイントを作成するプロセスについて説明します。

1. 検索バーで "**プライベート リンク**" を検索して選択します。 これにより、**プライベート リンク センター** に移動します。

    ![プライベート リンクの検索](./media/private-endpoints/search-for-private-link.png)

1. 左側のナビゲーション バーで、 **[プライベート エンドポイント]** を選択します。 **[プライベート エンドポイント]** ウィンドウに移動したら、 **[+ 追加]** を選択して、コンテナーのプライベート エンドポイントの作成を開始します。

    ![プライベート リンク センターでプライベート エンドポイントを追加する](./media/private-endpoints/add-private-endpoint.png)

1. **[プライベート エンドポイントの作成]** のプロセスに入ったら、プライベート エンドポイント接続を作成するための詳細を指定する必要があります。

    1. **[基本]** :プライベート エンドポイントの基本的な詳細を入力します。 リージョンは、コンテナーおよびリソースと同じである必要があります。

        ![基本的な詳細を入力する](./media/private-endpoints/basic-details.png)

    1. **リソース**:このタブでは、接続を作成する対象の PaaS リソースについて指定する必要があります。 目的のサブスクリプションのリソースの種類から、 **[Microsoft.RecoveryServices/vaults]** を選択します。 完了したら、 **[リソース]** として Recovery Services コンテナーの名前を選択し、**AzureBackup** を **[対象サブリソース]** としてします。

        ![[リソース] タブに入力する](./media/private-endpoints/resource-tab.png)

    1. **構成**:[構成] では、プライベート エンドポイントを作成する仮想ネットワークとサブネットを指定します。 これは、VM が存在する Vnet です。 プライベート DNS ゾーンに **プライベート エンドポイントを統合する** ことを選択できます。 または、カスタム DNS サーバーを使用するか、プライベート DNS ゾーンを作成することもできます。

        ![[構成] タブに入力する](./media/private-endpoints/configuration-tab.png)

        Azure プライベート DNS ゾーンと統合するのではなく、カスタム DNS サーバーを使用する場合は、[こちらのセクション](#dns-changes-for-custom-dns-servers)を参照してください。  

    1. 必要に応じて、プライベート エンドポイントの **タグ** を追加できます。

    1. 詳細の入力が完了したら、 **[確認および作成]** に進みます。 検証が完了したら、 **[作成]** を選択してプライベート エンドポイントを作成します。

## <a name="approving-private-endpoints"></a>プライベート エンドポイントを承認する

プライベート エンドポイントを作成しているユーザーが Recovery Services コンテナーの所有者でもある場合、上記で作成したプライベート エンドポイントは自動承認されます。 それ以外の場合、プライベート エンドポイントが使用できるようになるには、その前にコンテナーの所有者によって承認される必要があります。 このセクションでは、Azure portal を使用してプライベート エンドポイントを手動で承認する方法について説明します。

Azure Resource Manager クライアントを使用してプライベート エンドポイントを承認する方法については、「[Azure Resource Manager クライアントを使用したプライベート エンドポイントの手動による承認](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)」を参照してください。

1. Recovery Services コンテナーで、左側のバーの **[プライベート エンドポイント接続]** に移動します。
1. 承認するプライベート エンドポイントを選択します。
1. 上部バーにある **[承認]** を選択します。 エンドポイント接続を拒否または削除する場合は、 **[拒否]** または **[削除]** を選択することもできます。

    ![プライベート エンドポイントを承認する](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Backup のプライベート エンドポイントを使用する

ご自身の VNet 内のコンテナー用に作成したプライベート エンドポイントが承認されたら、バックアップと復元を実行するためにそれらの使用を開始できます。

>[!IMPORTANT]
>続行する前に、ドキュメントに記載された前述のすべての手順を確実に完了してください。 まとめると、次のチェックリストの手順を完了している必要があります。
>
>1. (新しい) Recovery Services コンテナーを作成しました
>1. システム割り当てマネージド ID をコンテナーで使用できるようにしました
>1. コンテナーのマネージド ID に、関連するアクセス許可を割り当てました
>1. コンテナーのプライベート エンドポイントを作成しました
>1. プライベート エンドポイントを承認しました (自動承認されない場合)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Azure VM でのワークロードのバックアップと復元 (SQL、SAP HANA)

プライベート エンドポイントが作成され、承認されれば、クライアント側でプライベート エンドポイントを使用するために追加の変更は必要ありません。 セキュリティで保護されたネットワークからコンテナーへのすべての通信とデータ転送は、プライベート エンドポイントを介して実行されます。
ただし、サーバー (SQL または SAP HANA) がボールトに登録された後にボールトのプライベート エンドポイントを削除した場合は、コンテナーをボールトに再登録する必要があります。 それらに対する保護を停止する必要はありません。

### <a name="backup-and-restore-through-mars-agent"></a>MARS エージェントを使用したバックアップと復元

MARS エージェントを使用してオンプレミスのリソースをバックアップする場合は、オンプレミスのネットワーク (バックアップ対象のリソースが含まれている) が Azure VNet (コンテナーのプライベート エンドポイントが含まれている) と確実にピアリングされているようにしてください。これにより、コンテナーを使用できるようになります。 その後、MARS エージェントをインストールして、ここで詳しく説明しているように、バックアップを構成できます。 ただし、バックアップのためのすべての通信が、ピアリングされたネットワークのみを介して行われるようにする必要があります。

ただし、MARS エージェントがボールトに登録された後にボールトのプライベート エンドポイントを削除した場合は、コンテナーをボールトに再登録する必要があります。 それらに対する保護を停止する必要はありません。

## <a name="additional-topics"></a>関連トピック

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを作成する

Azure Resource Manager クライアントを使用して、Recovery Services コンテナーを作成し、そのマネージド ID を有効にすることができます (後で説明するように、マネージド ID の有効化は必ず行う必要があります)。 これを行うサンプルを次に示します。

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

上記の JSON ファイルには、次の内容が含まれています。

要求 JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

応答 JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>この例で Azure Resource Manager クライアントを使用して作成されたコンテナーは、システム割り当てマネージド ID を使用して既に作成されています。

### <a name="managing-permissions-on-resource-groups"></a>リソース グループに対するアクセス許可の管理

コンテナーのマネージド ID には、プライベート エンドポイントが作成されるリソース グループと仮想ネットワークにおいて、次のアクセス許可が必要です。

- `Microsoft.Network/privateEndpoints/*` これは、リソース グループ内のプライベート エンドポイントで CRUD を実行するために必要です。 リソース グループに割り当てられている必要があります。
- `Microsoft.Network/virtualNetworks/subnets/join/action` これは、プライベート IP がプライベート エンドポイントに接続される仮想ネットワークに対して必要です。
- `Microsoft.Network/networkInterfaces/read` これは、プライベート エンドポイント用に作成されたネットワーク インターフェイスを取得するために、リソース グループに対して必要です。
- プライベート DNS ゾーン共同作成者ロール: このロールは既に存在し、`Microsoft.Network/privateDnsZones/A/*` および `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` のアクセス許可を付与するために使用できます。

次のいずれかの方法を使用して、必要なアクセス許可を持つロールを作成できます。

#### <a name="create-roles-and-permissions-manually"></a>ロールとアクセス許可を手動で作成する

次の JSON ファイルを作成し、セクションの最後にある PowerShell コマンドを使用してロールを作成します。

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>スクリプトを使用する

1. Azure portal で **Cloud Shell** を開始し、PowerShell ウィンドウで **[ファイルのアップロード]** を選択します。

    ![PowerShell ウィンドウで [ファイルのアップロード] を選択する](./media/private-endpoints/upload-file-in-powershell.png)

1. 次のスクリプトをアップロードします。[VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. ホーム フォルダー (例: `cd /home/user`) に移動します。

1. 次のスクリプトを実行します。

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    パラメーターは次のとおりです。

    - **subscription**: コンテナーのプライベート エンドポイントが作成されるリソース グループと、コンテナーのプライベート エンドポイントが接続されるサブネットを持つ SubscriptionId

    - **vaultPEResourceGroup**: コンテナーのプライベート エンドポイントが作成されるリソース グループ

    - **vaultPESubnetResourceGroup**: プライベート エンドポイントの参加先となるサブネットのリソース グループ

    - **vaultMsiName**: コンテナーの MSI の名前 (**VaultName** と同じ)

1. 認証を完了すると、スクリプトによって上記で指定されたサブスクリプションのコンテキストが取得されます。 テナントに存在しない場合は適切なロールが作成され、コンテナーの MSI にロールが割り当てられます。

### <a name="creating-private-endpoints-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する

#### <a name="auto-approved-private-endpoints"></a>プライベート エンドポイントの自動承認

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用したプライベート エンドポイントの手動による承認

1. **GetVault** を使用して、ご自身のプライベート エンドポイントのプライベート エンドポイント接続 ID を取得します。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    これにより、プライベート エンドポイント接続 ID が返されます。 接続 ID の最初の部分を次のように使用して、接続の名前を取得できます。

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 次のサンプルに示すように、**プライベート エンドポイント接続 ID** (および必要に応じて **プライベート エンドポイント名**) を応答から取得し、次の JSON および Azure Resource Manager URI 内でそれを置き換えて、状態を "Approved、Rejected、Disconnected" のいずれかに変更します。

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>カスタム DNS サーバーの DNS の変更

#### <a name="create-dns-zones-for-custom-dns-servers"></a>カスタム DNS サーバーの DNS ゾーンを作成する

3 つのプライベート DNS ゾーンを作成し、それらをご自身の仮想ネットワークにリンクする必要があります。 BLOB や Queue とは異なり、バックアップ サービスのパブリック URL は、Private Link DNS ゾーンへのリダイレクトのために Azure パブリック DNS に登録されないことに注意してください。 

| **ゾーン**                                                     | **サービス** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | バックアップ      |
| `privatelink.blob.core.windows.net`                            | BLOB        |
| `privatelink.queue.core.windows.net`                           | キュー       |

>[!NOTE]
>上のテキストでは、*geo* はリージョン コードを指します。 たとえば、*wcus* と *ne* は、それぞれ米国中西部と北ヨーロッパを表します。

リージョン コードについては、[こちらの一覧](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)を参照してください。 各国のリージョンの URL 名前付け規則については、次のリンクを参照してください。

- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [ドイツ](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov ](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>カスタム DNS サーバーの DNS レコードを追加する

これを行うには、ご自身のプライベート エンドポイントの FQDN ごとに、プライベート DNS ゾーンにエントリを作成する必要があります。

Backup、Blob、Queue の各サービス用に作成されたプライベート エンドポイントを使用することに注意してください。

- コンテナーのプライベート エンドポイントには、プライベート エンドポイントの作成時に指定した名前が使用されます。
- Blob service と Queue サービスのプライベート エンドポイントには、コンテナーの名前がプレフィックスとして付けられます。

たとえば、次の図は *pee2epe* という名前のプライベート エンドポイント接続用に作成された、3 つのプライベート エンドポイントを示しています。

![プライベート エンドポイント接続用の 3 つのプライベート エンドポイント](./media/private-endpoints/three-private-endpoints.png)

Backup サービスの DNS ゾーン (`privatelink.<geo>.backup.windowsazure.com`):

1. **プライベート リンク センター** で、Backup のプライベート エンドポイントに移動します。 [概要] ページには、プライベート エンドポイントの FQDN とプライベート IP が一覧表示されます。

1. FQDN およびプライベート IP ごとに 1 つのエントリを種類が A のレコードとして追加します。

    ![FQDN とプライベート IP ごとにエントリを追加する](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob service の DNS ゾーン (`privatelink.blob.core.windows.net`):

1. **プライベート リンク センター** で、Blob のプライベート エンドポイントに移動します。 [概要] ページには、プライベート エンドポイントの FQDN とプライベート IP が一覧表示されます。

1. FQDN およびプライベート IP に対して 1 つのエントリを種類が A のレコードとして追加します。

    ![Blob service 用に FQDN およびプライベート IP のエントリを種類 A のレコードとして追加する](./media/private-endpoints/add-type-a-record-for-blob.png)

Queue サービスの DNS ゾーン (`privatelink.queue.core.windows.net`):

1. **プライベート リンク センター** で、Queue のプライベート エンドポイントに移動します。 [概要] ページには、プライベート エンドポイントの FQDN とプライベート IP が一覧表示されます。

1. FQDN およびプライベート IP に対して 1 つのエントリを種類が A のレコードとして追加します。

    ![Queue サービス用に FQDN およびプライベート IP のエントリを種類 A のレコードとして追加する](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

Q. 既存のバックアップ コンテナー用にプライベート エンドポイントを作成できますか?<br>
A. いいえ。プライベート エンドポイントは、新しいバックアップ コンテナーに対してのみ作成できます。 そのため、コンテナーには、保護されている項目があってはなりません。 実際に、プライベート エンドポイント作成前にコンテナーに項目を保護することはできません。

Q. コンテナーに項目を保護しようとしましたが失敗し、コンテナーには保護された項目がまだ含まれていません。 このコンテナーのプライベート エンドポイントを作成できますか?<br>
A. いいえ。コンテナーに項目を保護しようとしたことが過去にあってはなりません。

Q. バックアップと復元のためにプライベート エンドポイントを使用しているコンテナーがあります。 保護されているバックアップ項目がある場合でも、このコンテナーのプライベート エンドポイントを後で追加または削除できますか?<br>
A. はい。 コンテナーのプライベート エンドポイントと、そのコンテナーに保護されているバックアップ項目を既に作成済みの場合、必要に応じて、後でプライベート エンドポイントを追加または削除できます。

Q. Azure Backup のプライベート エンドポイントを Azure Site Recovery にも使用することはできますか?<br>
A. いいえ。Backup のプライベート エンドポイントは、Azure Backup にのみ使用できます。 Azure Site Recovery 用の新しいプライベート エンドポイントを作成する必要があります (そのサービスでサポートされている場合)。

Q. この記事にある手順の 1 つを実行しないまま続行し、データ ソースを保護しました。 それでもプライベート エンドポイントを使用できますか?<br>
A. 記事の手順に従わずに項目の保護まで続行した場合、コンテナーでプライベート エンドポイントを使用できなくなる可能性があります。 このため、項目の保護に進む前に、このチェックリストを参照することをお勧めします。

Q. Azure プライベート DNS ゾーンや統合されたプライベート DNS ゾーンを使用するのではなく、独自の DNS サーバーを使用できますか?<br>
A. はい。独自の DNS サーバーを使用できます。 ただし、このセクションで提示されているように、必要なすべての DNS レコードを確実に追加してください。

Q. この記事の手順に従った後に、使用しているサーバーで追加の手順を実行する必要がありますか?<br>
A. この記事で詳しく説明されているプロセスに従った後、バックアップと復元にプライベート エンドポイントを使用するために追加の作業を行う必要はありません。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のセキュリティ機能](security-overview.md)に関する記事を読む
