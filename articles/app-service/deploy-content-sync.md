---
title: クラウド フォルダーからコンテンツを同期する
description: OneDrive や Dropbox などのクラウドフォルダーからのコンテンツ同期を介して、アプリをAzure App Serviceにデプロイする方法を学びます。
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051237"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>クラウド フォルダーから Azure App Service へのコンテンツの同期
この記事では、Dropbox と OneDrive のコンテンツを [Azure App Service](./overview.md) と同期する方法を示します。 

コンテンツ同期という手法では、指定されたクラウド フォルダーに含まれるアプリ コードとコンテンツを扱い、いつでもデプロイできる状態にします。その後、ボタンをクリックして App Service に同期します。 

API に違いがあるため、**OneDrive for Business** は現時点ではサポートされていません。

> [!NOTE]
> Azure portal の **[開発センター (クラシック)]** ページ (以前のデプロイ エクスペリエンス) は、2021 年 3 月に非推奨となる予定です。 この変更はアプリの既存のデプロイ設定には影響せず、 **[デプロイ センター]** ページで引き続きアプリのデプロイを管理できます。

## <a name="enable-content-sync-deployment"></a>コンテンツ同期デプロイを有効にする

1. [Azure portal](https://portal.azure.com)で、App Service アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]** 、 **[設定]** の順にクリックします。 

1. **[ソース]** で、 **[OneDrive]** または **[Dropbox]** を選択します。

1. **[承認]** をクリックし、承認プロンプトに従います。 

    ![Azure portal のデプロイ センターで OneDrive または Dropbox を承認する方法を示します。](media/app-service-deploy-content-sync/choose-source.png)

    OneDrive または Dropbox の承認は、Azure アカウントに 1 回だけ実行します。 アプリで別の OneDrive または Dropbox アカウントを承認するには、 **[アカウントの変更]** をクリックします。

1. **[フォルダー]** で、同期するフォルダーを選択します。 このフォルダーは、OneDrive または Dropbox の次のコンテンツ パスの下に作成されます。 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. **[保存]** をクリックします。

## <a name="synchronize-content"></a>コンテンツを同期する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com)で、App Service アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]** 、 **[Redeploy/Sync]\(再デプロイまたは同期\)** の順にクリックします。 

    ![クラウド フォルダーを App Service と同期する方法を示します。](media/app-service-deploy-content-sync/synchronize.png)
   
1. **[OK]** をクリックして同期を確定します。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

次のコマンドを実行して \<group-name> および \<app-name> を置換し、同期を開始します。

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

次のコマンドを実行して \<group-name> および \<app-name> を置換し、同期を開始します。

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>コンテンツ同期デプロイを無効にする

1. [Azure portal](https://portal.azure.com)で、App Service アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]** 、 **[設定]** 、 **[接続解除]** の順にクリックします。 

    ![Azure portal の App Service アプリでクラウド フォルダーの同期を切断する方法を示します。](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル Git リポジトリからデプロイする](deploy-local-git.md)