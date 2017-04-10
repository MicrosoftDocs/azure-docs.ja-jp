---
title: "Azure App Service Web Apps on Linux での Ruby の使用 | Microsoft Docs"
description: "Azure App Service Web Apps on Linux での Ruby の使用"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS, Ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Web Apps on Linux での Ruby の使用 #

バックエンドの最新の更新で、Ruby v.2.3 のサポートが導入されました。 Linux の Web アプリの構成を設定することで、アプリケーション スタックを変更できます。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用 ##

[Azure Portal](https://portal.azure.com) の新しいメニューでは、次の画像に示す [Web + モバイル] オプションから Linux での Web アプリの作成を選択できます。

![Azure Portal を使用した Web アプリの作成の開始][1]

次に、次の図のように **[作成] ブレード**が開きます。

![[作成] ブレード][2]

1. Web アプリに名前を付けます。
2. 既存のリソース グループを選択するか、新しいリソース グループを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、使用可能なリージョンを確認してください)。
3. 既存の Azure App Service プランを選択するか、新しいプランを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、App Service プランの注意事項を確認してください)。
4. 組み込みのランタイム スタックから Ruby を選択します。

Ruby Web アプリが作成されたら、Git または FTP を使用してデプロイできます。

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](app-service-linux-how-to-create-a-web-app.md)
* [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)
* [Azure App Service Web Apps on Linux の FAQ](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
