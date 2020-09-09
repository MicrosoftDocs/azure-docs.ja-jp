---
title: クラウド フォルダーからコンテンツを同期する
description: OneDrive や Dropbox などのクラウドフォルダーからのコンテンツ同期を介して、アプリをAzure App Serviceにデプロイする方法を学びます。
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 693e552c8743b435fac6fda9d5ab023be5d9adeb
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221138"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>クラウド フォルダーから Azure App Service へのコンテンツの同期
この記事では、Dropbox と OneDrive のコンテンツを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) と同期する方法を示します。 

オンデマンド コンテンツ同期デプロイでは、App Service の [Kudu デプロイ エンジン](https://github.com/projectkudu/kudu/wiki) を利用しています。 指定したクラウド フォルダーのアプリ コードとコンテンツを操作した後、ボタンをクリックして App Service に同期できます。 コンテンツの同期では、Kudu ビルド サーバーが使用されます。 

## <a name="enable-content-sync-deployment"></a>コンテンツ同期デプロイを有効にする

コンテンツの同期を有効にするのには、[Azure Portal](https://portal.azure.com) で App Service アプリのページに移動します。

左側のメニューで、 **[デプロイ センター]**  >  **[OneDrive]** または **[Dropbox]**  >  **[承認]** をクリックします。 承認のプロンプトに従います。 

![Azure portal のデプロイ センターで OneDrive または Dropbox を承認する方法を示します。](media/app-service-deploy-content-sync/choose-source.png)

OneDrive または Dropbox の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、 **[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認済みの OneDrive または Dropbox のアカウントを変更できます。

![Azure portal のデプロイ センターで承認された OneDrive または Dropbox アカウントを変更する方法を示します。](media/app-service-deploy-content-sync/continue.png)

**[構成]** ページで、同期するフォルダーを選択します。 このフォルダーは、OneDrive または Dropbox の次のコンテンツ パスの下に作成されます。 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

完了したら、 **[続行]** をクリックします。

**[概要]** ページで、選択内容を確認し、 **[完了]** をクリックします。

## <a name="synchronize-content"></a>コンテンツを同期する

クラウド フォルダーのコンテンツを App Service と同期する場合は、 **[デプロイ センター]** ページに戻り、 **[同期]** をクリックします。

![クラウド フォルダーを App Service と同期する方法を示します。](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > API に違いがあるため、**OneDrive for Business** は現時点ではサポートされていません。 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>コンテンツ同期デプロイを無効にする

コンテンツの同期を無効にするのには、[Azure Portal](https://portal.azure.com) で App Service アプリのページに移動します。

左側のメニューで、 **[デプロイ センター]**  >  **[切断]** をクリックします。

![Azure portal の App Service アプリでクラウド フォルダーの同期を切断する方法を示します。](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル Git リポジトリからデプロイする](deploy-local-git.md)
