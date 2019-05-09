---
title: Recovery Services コンテナーを Azure サブスクリプションをまたいで移動するか、別のリソース グループに移動します。
description: Recovery Services コンテナーを Azure サブスクリプションおよびリソース グループをまたいで移動するための手順。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: f4ab983fbebe9c0219e70fa7bd5742cf1c3a0491
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361967"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Azure サブスクリプションとリソース グループをまたいで Recovery Services コンテナーを移動する (限定パブリック プレビュー)

この記事では、Azure Backup 用に構成された Recovery Services コンテナーを、Azure のサブスクリプションをまたいで移動するか、同じサブスクリプション内の別のリソース グループに移動する方法について説明します。 Azure portal または PowerShell を使用して、Recovery Services コンテナーを移動することができます。

> [!NOTE]
> Recovery Services コンテナーとその関連リソースを別のリソース グループに移動するには、最初に[ソース サブスクリプションを登録する](#register-the-source-subscription-to-move-your-recovery-services-vault)必要があります。

## <a name="supported-geos"></a>サポートされている地域

Recovery Services コンテナーのリソースの移動は、オーストラリア東部、オーストラリア南東部、カナダ中部、カナダ東部、東南アジア、東アジア、米国中部、米国中北部、米国東部、米国東部 2、米国中南部、米国中西部、米国中西部 2、米国西部、インド中部、インド南部、東日本、西日本、韓国中部、韓国南部、北ヨーロッパ、西ヨーロッパ、南アフリカ北部、南アフリカ西部、英国南部、英国西部、アラブ首長国連邦中部、およびアラブ首長国連邦北部でサポートされています。

## <a name="prerequisites-for-moving-a-vault"></a>コンテナーを移動するための前提条件

- リソース グループ間を移動する場合は、その操作の間、ソース リソース グループとターゲット リソース グループの両方がロックされます。 移動が完了するまで、これらのリソース グループに対する書き込み操作および削除操作はブロックされます。
- サブスクリプション管理者のみが、コンテナーを移動するアクセス許可を持ちます。
- サブスクリプション間でコンテナーを移動するとき、ターゲット サブスクリプションは有効な状態で存在する必要があり、ソース サブスクリプションと同じテナントに存在する必要があります。
- ターゲット リソース グループへの書き込み操作を実行するアクセス許可が必要です。
- Recovery Services コンテナーの場所を変更することはできません。 コンテナーを移動すると、リソース グループのみが変更されます。 新しいリソース グループは別の場所に存在する場合もありますが、そのことでコンテナーの場所が変更されるわけではありません。
- 現在、一度に移動できる Recovery Services コンテナーはリージョンごとに 1 つです。
- VM が Recovery Services コンテナーと一緒にサブスクリプションをまたいで移動しないか、または新しいリソース グループに移動しない場合、現在の VM の復旧ポイントは、有効期限が切れるまでコンテナーにそのまま残ります。
- VM をコンテナーと一緒に移動するかどうかに関係なく、VM はコンテナー内に保持されているバックアップ履歴から復元できます。
- Azure Disk Encryption では、Key Vault と VM が同じ Azure リージョンおよびサブスクリプションに属している必要があります。
- 仮想マシンをマネージド ディスクと一緒に移動するには、この[記事](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)を参照してください。
- クラシック モデルを使用してデプロイされるリソースを移動するためのオプションは、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。 詳細については、[こちらの記事](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations)を参照してください。
- コンテナーに対して定義されるバックアップ ポリシーは、コンテナーがサブスクリプションをまたいで移動しても、新しいリソース グループに移動しても保持されます。
- 現時点では、Azure Files、Azure File Sync、または SQL を IaaS VM に含むコンテナーを、サブスクリプションおよびリソース グループをまたいで移動することはできません。
- VM のバックアップ データがあるコンテナーをサブスクリプションをまたいで移動する場合、バックアップを継続するためには、お客様の VM を同じサブスクリプションに移動し、同じターゲット リソース グループを使用する必要があります。<br>

> [!NOTE]
>
> **Azure Site Recovery** で使用するように構成された Recovery Services コンテナーは、まだ移動できません。 **Azure Site Recovery** を使用して、いずれかの VM (Azure IaaS、HYPER-V、VMware) または物理マシンをディザスター リカバリー用に構成している場合、移動操作はブロックされます。 Site Recovery サービスに対するリソースの移動機能は、まだ使用できません。

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>Recovery Services コンテナーの移動元となるサブスクリプションの登録

Recovery Services コンテナーの**移動**元となるサブスクリプションを登録するには、PowerShell ターミナルから次のコマンドレットを実行します。

1. Azure アカウントへのサインイン

   ```
   Connect-AzureRmAccount
   ```

2. 登録するサブスクリプションを選択します

   ```
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```
3. このサブスクリプションを登録します

   ```
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. コマンドを実行します

   ```
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Azure portal または PowerShell を使用して移動操作を開始する前に、サブスクリプションがホワイト リストに登録されるまで 30 分間待機します。

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Azure portal を使用して Recovery Services コンテナーを別のリソース グループに移動する

Recovery Services コンテナーとその関連リソースを別のリソース グループに移動するには

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **Recovery Services コンテナー**の一覧を開き、移動するコンテナーの名前を選択します。 コンテナーのダッシュボードが開き、次の図のように表示されます。

   ![Recover Service コンテナーを開く](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   コンテナーの **[Essentials]** 情報が表示されない場合、ドロップダウン アイコンをクリックします。 コンテナーの Essentials 情報が表示されるはずです。

   ![[Essentials] 情報タブ](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. コンテナーの概要メニューで、**[リソース グループ]** の横にある **[変更]** をクリックして、**[リソースの移動]** ブレードを開きます。

   ![リソース グループを変更する](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **[リソースの移動]** ブレードで、一部のコンテナーについては次の図のようにチェック ボックスをオンにして、オプションの関連リソースを移動することをお勧めします。

   ![サブスクリプションを移動する](./media/backup-azure-move-recovery-services/move-resource.png)

5. ターゲット リソース グループを追加するには、**[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、**[新しいグループを作成する]** オプションをクリックします。

   ![リソースを作成する](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. リソース グループを追加した後、**[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]** というオプションを確認し、**[OK]** をクリックしてコンテナーの移動を完了します。

   ![確認メッセージ](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Azure portal を使用して Recovery Services コンテナーを別のサブスクリプションに移動する

Recovery Services コンテナーとその関連リソースを別のサブスクリプションに移動できます

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Recovery Services コンテナーの一覧を開き、移動するコンテナーを選択します。 コンテナーのダッシュボードが開き、次の図のように表示されます。

    ![Recover Service コンテナーを開く](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    コンテナーの **[Essentials]** 情報が表示されない場合、ドロップダウン アイコンをクリックします。 コンテナーの Essentials 情報が表示されるはずです。

    ![[Essentials] 情報タブ](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. コンテナーの概要メニューで、**[サブスクリプション]** の横にある **[変更]** をクリックして、**[リソースの移動]** ブレードを開きます。

   ![サブスクリプションを変更する](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 移動するリソースを選択します。 ここで、**[すべて選択]** オプションを使用して、表示されているすべてのオプション リソースを選択することをお勧めします。

   ![リソースの移動](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. コンテナーを移動する先のターゲットのサブスクリプションを、**[サブスクリプション]** ドロップダウン リストから選択します。
6. ターゲット リソース グループを追加するには、**[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、**[新しいグループを作成する]** オプションをクリックします。

   ![サブスクリプションを追加する](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]** オプションをクリックして確認し、**[OK]** をクリックします。

> [!NOTE]
> クロス サブスクリプション バックアップ (RS コンテナーと保護対象の VM が別のサブスクリプションにある) シナリオは、サポートされていません。 また、ローカル冗長ストレージ (LRS) からグローバル冗長ストレージ (GRS) への (およびその逆の) ストレージ冗長オプションは、コンテナー移動操作中に変更できません。
>
>

## <a name="use-powershell-to-move-a-vault"></a>PowerShell を使用したコンテナーの移動

Recovery Services コンテナーを別のリソース グループに移動するには、`Move-AzureRMResource` コマンドレットを使用します。 `Move-AzureRMResource` ではリソース名とリソースの種類が必要です。 どちらも `Get-AzureRmRecoveryServicesVault` コマンドレットから取得できます。

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

リソースを他のサブスクリプションに移動するには、`-DestinationSubscriptionId` パラメーターを含めます。

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

上記のコマンドレットを実行した後、指定したリソースを移動することの確認を求められます。 「**Y**」と入力して確認します。 検証が成功した後、リソースが移動します。

## <a name="use-cli-to-move-a-vault"></a>CLI を使用してコンテナーを移動する

Recovery Services コンテナーを別のリソース グループに移動するには、次のコマンドレットを使用します。

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

## <a name="post-migration"></a>移行後の処理

1. リソース グループのアクセス制御を設定および検証する必要があります。  
2. 移動の完了後のコンテナーに対して、バックアップのレポートと監視機能をもう一度構成する必要があります。 以前の構成は、移動操作中に失われます。



## <a name="next-steps"></a>次の手順

リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。

詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)」を参照してください。
