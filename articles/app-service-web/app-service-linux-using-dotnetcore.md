---
title: "Azure App Service Web Apps on Linux での .NET Core の使用 | Microsoft Docs"
description: "Azure App Service Web Apps on Linux での .NET Core の使用について説明します。"
keywords: "Azure App Service, Web アプリ, .NET, コア, Linux, OSS"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Web Apps on Linux での .NET Core の使用 #

バックエンドの最新の更新で、.NET Core v.1.0 のサポートが導入されました。 Linux の Web アプリの構成を設定することで、アプリケーション スタックを変更できます。


## <a name="using-xplat-cli"></a>XPlat CLI の使用 ##

最新の Azure クロス プラットフォーム CLI を使用することで、**azure webapp config set** コマンドを使用し、アプリケーション スタックを変更することができます。 たとえば次のようになります。

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** ファイルはアプリの dll です。 アプリでは任意の名前を使用することができます。

これで .Net Core イメージが読み込まれ、Web アプリが開始されます。 設定が正しいかどうかを確認するには、**azure webapp config show** を使用します。 たとえば次のようになります。

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](./app-service-linux-how-to-create-a-web-app.md)
* [Azure Web アプリのクロス プラットフォーム CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Azure App Service Web Apps on Linux の FAQ](app-service-linux-faq.md)
