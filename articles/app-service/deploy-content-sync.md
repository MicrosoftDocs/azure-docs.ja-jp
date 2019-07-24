---
title: クラウド フォルダーからのコンテンツを同期する - Azure App Service
description: コンテンツ同期を使用してクラウド フォルダーから Azure App Service にアプリをデプロイする方法を説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 65f372196671e95f7d9af7f47011e9ca1f9de316
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551120"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>クラウド フォルダーから Azure App Service へのコンテンツの同期
この記事では、Dropbox と OneDrive のコンテンツを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) と同期する方法を示します。 

オンデマンド コンテンツ同期デプロイでは、App Service の [Kudu デプロイ エンジン](https://github.com/projectkudu/kudu/wiki) を利用しています。 指定したクラウド フォルダーのアプリ コードとコンテンツを操作した後、ボタンのクリックで App Service に同期できます。 コンテンツの同期では、Kudu ビルド サーバーが使用されます。 

## <a name="enable-content-sync-deployment"></a>コンテンツ同期デプロイを有効にする

コンテンツの同期を有効にするのには、[Azure Portal](https://portal.azure.com) で App Service アプリのページに移動します。

左側のメニューで、**[デプロイ センター]** > **[OneDrive]** または **[Dropbox]** > **[承認]** をクリックします。 承認のプロンプトに従います。 

![](media/app-service-deploy-content-sync/choose-source.png)

OneDrive または Dropbox の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、**[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認済みの OneDrive または Dropbox のアカウントを変更できます。

![](media/app-service-deploy-content-sync/continue.png)

**[構成]** ページで、同期するフォルダーを選択します。 このフォルダーは、OneDrive または Dropbox の次のコンテンツ パスの下に作成されます。 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

完了したら、**[続行]** をクリックします。

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

## <a name="synchronize-content"></a>コンテンツを同期する

クラウド フォルダーのコンテンツを App Service と同期する場合は、**[デプロイ センター]** ページに戻り、**[同期]** をクリックします。

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > API に違いがあるため、**OneDrive for Business** は現時点ではサポートされていません。 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>コンテンツ同期デプロイを無効にする

コンテンツの同期を無効にするのには、[Azure Portal](https://portal.azure.com) で App Service アプリのページに移動します。

左側のメニューで、**[デプロイ センター]** > **[切断]** をクリックします。

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ローカル Git リポジトリからデプロイする](deploy-local-git.md)
