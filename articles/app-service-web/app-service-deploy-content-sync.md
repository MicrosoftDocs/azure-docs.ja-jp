---
title: "クラウド フォルダーから Azure App Service へのコンテンツの同期"
description: "コンテンツ同期を使用してクラウド フォルダーから Azure App Service にアプリをデプロイする方法を説明します。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ddda0e60dc9d5414142791175b77a5a65e3f40e3
ms.lasthandoff: 11/17/2016


---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>クラウド フォルダーから Azure App Service へのコンテンツの同期
このチュートリアルでは、Dropbox や OneDrive などの一般的なクラウド ストレージ サービスからコンテンツを同期することによって [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) をデプロイする方法について説明します。 

## <a name="a-nameoverviewaoverview-of-content-sync-deployment"></a><a name="overview"></a>コンテンツ同期デプロイの概要
オンデマンド コンテンツ同期デプロイでは、App Service と統合された [Kudu デプロイ エンジン](https://github.com/projectkudu/kudu/wiki) を使用しています。 [Azure ポータル](https://portal.azure.com)では、クラウド ストレージのフォルダーを指定して、そのフォルダー内でアプリのコードとコンテンツの作業を行い、ボタンをクリックするだけで App Service に同期できます。 コンテンツ同期では、ビルドとデプロイに Kudu プロセスを使用します。 

## <a name="a-namecontentsyncahow-to-enable-content-sync-deployment"></a><a name="contentsync"></a>コンテンツ同期デプロイを有効化する方法
[Azure ポータル](https://portal.azure.com)からのコンテンツ同期を有効化するには、次の手順を実行します。

1. Azure Portal の対象アプリのブレードで、**[設定]** > **[デプロイ ソース]** をクリックします。 **[ソースの選択]** をクリックし、デプロイのソースとして **[OneDrive]** または **[Dropbox]** を選択します。 
   
    ![コンテンツ同期](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > API に違いがあるため、**OneDrive for Business** は現時点ではサポートされていません。 
   > 
   > 
2. OneDrive または Dropbox の専用の事前定義された指定パスに App Service がアクセスできるようにする、認証ワークフローを完了します。このパスが、App Service のコンテンツの保存先となります。  
    認証が完了すると、App Service プラットフォームで、指定のコンテンツ パスの下にコンテンツ フォルダーを作成するか、または指定のコンテンツ パスにある既存のコンテンツ フォルダーを選択できるようになります。 App Service 同期に使用するクラウド ストレージ アカウントの指定コンテンツ パスは次のとおりです。  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. コンテンツ同期は、初回以降は Azure ポータルからオンデマンドで開始できます。 デプロイ履歴は **[デプロイ]** ブレードで確認できます。
   
    ![デプロイ履歴](./media/app-service-deploy-content-sync/onedrive_sync.png)

Dropbox デプロイの詳細については、 [Dropbox からのデプロイ](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)に関する記事を参照してください。 


