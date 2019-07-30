---
title: Azure アカウントをパートナー ID にリンクする | Microsoft Docs
description: Azure の顧客のリソースを管理する際に使用するユーザー アカウントにパートナー ID をリンクすることで、顧客のエンゲージメントを追跡します。
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 97866a1f1299c028cdc8f86245308ae4a8b5db88
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502683"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Azure アカウントにパートナー ID をリンクする

Microsoft パートナーは、お客様が Microsoft 製品を使用してビジネスや任務の目的を達成できるよう支援するサービスを提供しています。 パートナー ユーザーは、Azure サービスの管理、構成、サポートをお客様に代わって行うときに、お客様の環境にアクセスする必要があります。 パートナーは、パートナー管理者リンクを使用して、パートナー ネットワーク ID をサービス提供に使用される資格情報に関連付けることができます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>顧客からアクセス権を取得する

パートナー ID をリンクする前に、次のオプションのいずれかを使用することで、顧客の Azure リソースに対するアクセス権を顧客から与えてもらう必要があります。

- **ゲスト ユーザー**: 顧客は、貴社をゲスト ユーザーとして追加し、ロールベースのアクセス制御 (RBAC) ロールを割り当てることができます。 詳細については、[別のディレクトリからのゲスト ユーザーの追加](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)に関する記事を参照してください。

- **ディレクトリ アカウント**: 貴社の顧客は、顧客のディレクトリ内に貴社のユーザー アカウントを作成し、任意の RBAC ロールを割り当てることができます。

- **サービス プリンシパル**: 貴社の顧客は、貴社の組織から顧客のディレクトリ内にアプリまたはスクリプトを追加し、RBAC ロールを割り当てることができます。 アプリまたはスクリプトの ID は、サービス プリンシパルと呼ばれます。

## <a name="link-to-a-partner-id"></a>パートナー ID へリンク

顧客のリソースにアクセスできるようになったら、Azure portal、PowerShell、または Azure CLI を使用して、貴社の Microsoft Partner Network ID (MPN ID) を貴社のユーザー ID またはサービス プリンシパルにリンクします。 パートナー ID は、顧客のテナントごとにリンクしてください。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure portal を使用して新しいパートナー ID にリンクする

1. Azure portal で [[パートナー ID へリンク]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) に移動します。

2. Azure ポータルにサインインします。

3. Microsoft パートナー ID を入力します。 パートナー ID は、貴社の組織の [Microsoft Partner Network](https://partner.microsoft.com/) ID です。

   ![パートナー ID へのリンクを示すスクリーンショット](./media/billing-link-partner-id/link-partner-ID.PNG)

4. 別の顧客用にパートナー ID をリンクするには、ディレクトリを切り替えます。 **[ディレクトリの切り替え]** でディレクトリを選びます。

   ![[ディレクトリの切り替え] を示すスクリーンショット](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell を使用して新しいパートナー ID にリンクする

1. [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell モジュールをインストールします。

2. ユーザー アカウントまたはサービス プリンシパルで顧客のテナントにサインインします。 詳細については、[PowerShell を使用したサインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)に関するページを参照してください。

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. 新しいパートナー ID にリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network](https://partner.microsoft.com/) ID です。

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>リンク済みのパートナー ID を取得する
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>リンク済みのパートナー ID を更新する
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>リンク済みのパートナー ID を削除する
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Azure CLI を使用して新しいパートナー ID にリンクする
1. Azure CLI 拡張機能をインストールします。

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. ユーザー アカウントまたはサービス プリンシパルで顧客のテナントにサインインします。 詳細については、[Azure CLI を使用したサインイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)に関するページを参照してください。

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. 新しいパートナー ID にリンクします。 パートナー ID は、貴社の組織の [Microsoft Partner Network](https://partner.microsoft.com/) ID です。

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>リンク済みのパートナー ID を取得する
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>リンク済みのパートナー ID を更新する
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>リンク済みのパートナー ID を削除する
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>次の手順

[Microsoft パートナー コミュニティ](https://aka.ms/PALdiscussion)のディスカッションに参加して、更新プログラムを受け取ったり、フィードバックを送ったりします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**誰がパートナー ID をリンクできますか。**

顧客の Azure リソースを管理しているパートナー組織の任意のユーザーが、パートナー ID をアカウントにリンクできます。

**パートナー ID はリンク後に変更できますか。**

はい。 はい、リンク済みのパートナー ID は、変更、追加、または削除できます。

**ユーザーのアカウントが複数の顧客のテナントに存在する場合はどうなりますか。**

パートナー ID とアカウント間のリンクは、各顧客テナントに対して行われます。 パートナー ID は、顧客のテナントごとにリンクしてください。

**他のパートナーまたは顧客がパートナー ID へのリンクを編集または削除できますか。**

リンクはユーザー アカウント レベルで関連付けられます。 パートナー ID へのリンクは、貴社のみが編集または削除できます。 顧客と他のパートナーは、パートナー ID へのリンクを変更できません。


**会社に複数の MPN ID がある場合は、どれを使用すべきですか。**

パートナー拠点アカウントおよび関連付けられている MPN ID を、パートナー ID のリンクに使用する必要があります。  パートナー アカウントの詳細については、[こちら](https://docs.microsoft.com/partner-center/account-structure)をご覧ください。

**リンク済みのパートナー ID の影響を受ける収益レポートはどこで確認できますか。**

"クラウド製品のパフォーマンス" レポートは、パートナー センターの [My Insights ダッシュボード](https://partner.microsoft.com/membership/reports/myinsights)で利用できます。 パートナーの関連付けの種類として [Partner Admin Link]\(パートナー管理リンク\) を選択する必要があります。

**レポートに顧客が表示されないのはなぜですか。**

レポートに顧客が表示されないのは、次の理由によります。

1. リンクされたユーザー アカウントに、顧客の Azure サブスクリプションまたはリソースに対する[ロールベースのアクセス](https://docs.microsoft.com/azure/role-based-access-control/overview)がありません。

2. ユーザーの[ロールベースのアクセス](https://docs.microsoft.com/azure/role-based-access-control/overview)がある Azure サブスクリプションで使用量が発生していません。

**パートナー ID のリンクは、Azure Stack で動作しますか。**

はい、Azure Stack に対してパートナー ID をリンクできます。
