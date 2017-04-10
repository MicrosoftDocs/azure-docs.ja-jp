---
title: "FTP/S を使用した Azure App Service へのアプリのデプロイ | Microsoft Docs"
description: "FTP または FTPS を使用して Azure App Service にアプリをデプロイする方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
translationtype: Human Translation
ms.sourcegitcommit: 816113f7635a003e22a5172113e5039dbcc1ceac
ms.openlocfilehash: 5dc546849bd02ccf4d02f3e6363a3e2fc3898259


---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S を使用した Azure App Service へのアプリのデプロイ
この記事では、FTP または FTPS を使用して、Web アプリ、モバイル アプリ バックエンド、または API アプリを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法について説明します。

アプリの FTP/S エンドポイントは既にアクティブです。 FTP/S デプロイを有効にするための構成は必要ありません。 

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>手順 1: デプロイ資格情報を設定する

アプリの FTP サーバーにアクセスするには、最初にデプロイ資格情報が必要です。 

デプロイ資格情報を設定またはリセットするには、[Azure App Service のデプロイ資格情報](app-service-deployment-credentials.md)に関するページをご覧ください。 このチュートリアルでは、ユーザー レベルの資格情報の使用について説明します。

## <a name="step-2-get-ftp-connection-information"></a>手順 2: FTP の接続情報を取得する

1. [Azure Portal](https://portal.azure.com) で、アプリの[リソース ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)を開きます。
2. 左側のメニューで **[概要]** を選択し、**FTP/デプロイ ユーザー**、**FTP ホスト名**、および **FTPS ホスト名**の値をメモします。 

    ![FTP 接続情報](./media/web-sites-deploy/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Azure Portal に表示される **[FTP/デプロイ ユーザー]** のユーザー値には、FTP サーバーに適切なコンテキストを提供するためにアプリ名が含まれます。
    > 左側のメニューで **[プロパティ]** を選択した場合も、これと同じ情報を確認できます。 
    >
    > また、デプロイ パスワードは表示されません。 デプロイ パスワードを忘れた場合は、[手順 1](#step1) に戻って、デプロイ パスワードをリセットする必要があります。
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>手順 3: ファイルを Azure にデプロイする

1. FTP クライアント ([Visual Studio](https://www.visualstudio.com/vs/community/)、[FileZilla](https://filezilla-project.org/download.php?type=client) など) から、収集した接続情報を使用してアプリに接続します。
3. ファイルとそれぞれのディレクトリ構造を、Azure の [**/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App_Data/Jobs/** ディレクトリ) にコピーします。
4. アプリの URL を参照して、アプリが正しく動作していることを確認します。 

> [!NOTE] 
> [git ベース デプロイ](app-service-deploy-local-git.md)とは異なり、FTP デプロイでは、次のデプロイ自動化はサポートされません。 
>
> - 依存関係の復元 (NuGet、NPM、PIP、Composer の自動化など)
> - .NET バイナリのコンパイル
> - web.config の生成 ([Node.js の例はこちら](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> このようなファイルが必要な場合は、ローカル コンピューターで手動で復元、ビルド、および生成し、アプリと共にデプロイする必要があります。
>
>

## <a name="next-steps"></a>次のステップ

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](app-service-deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [PHP-MySQL Web アプリの作成と FTP でのデプロイ](web-sites-php-mysql-deploy-use-ftp.md)。
* [Azure App Service のデプロイ資格情報](app-service-deploy-ftp.md)



<!--HONumber=Jan17_HO4-->


