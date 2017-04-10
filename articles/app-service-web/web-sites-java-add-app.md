---
title: "Azure App Service Web Apps への Java アプリケーションの追加"
description: "このチュートリアルでは、Java を使用するように設定された Azure App Service Web Apps のインスタンスに、ページやアプリケーションを追加する方法について説明します。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 627930ca68a94ecc56e7ef9ac9435f4b5f3f41c7
ms.openlocfilehash: 61466be17a52f1f230207b71bb94e10f88ee075c


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Azure App Service Web Apps への Java アプリケーションの追加
「[Azure App Service での Java Web アプリの作成](web-sites-java-get-started.md)」の説明にあるように、Java Web アプリを [Azure App Service][Azure App Service] で初期設定した後、WAR を **webapps** フォルダーに配置してアプリケーションをアップロードすることができます。

**webapps** フォルダーへのナビゲーション パスは Web アプリのインスタンス設定方法によって異なります。

* Azure Marketplace を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\home\site\wwwroot\bin\application\_server\webapps** という形式になります (**application\_server** は、Web Apps のインスタンスで有効になっているアプリケーション サーバーの名前です)。 
* Azure の構成 UI を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\home\site\wwwroot\webapps** という形式になります。 

[継続的な統合シナリオ](app-service-continuous-deployment.md)などで、ソース管理を使用してアプリケーションや Web ページをアップロードできます。 FTP も、アプリケーションまたは Web ページをアップロードするためのオプションです。FTP 経由でのアプリケーションのデプロイの詳細については、「[Azure App Service へのアプリのデプロイ]」を参照してください。

WAR ファイルを **webapps** フォルダーにアップロードすると、Tomcat アプリケーション サーバーで WAR ファイルの追加が検出され、WAR ファイルが自動的に読み込まれます。 ルート ディレクトリにファイル (WAR ファイル以外) をコピーした場合は、それらのファイルを使用する前に、アプリケーション サーバーの再起動が必要になります。 Azure で実行されている Tomcat Java Web アプリの自動読み込み機能は、 **webapps** フォルダーに追加される新しい WAR ファイル、または新しいファイルやディレクトリに基づいて動作します。 

<a name="see-also"></a>

## <a name="see-also"></a>関連項目
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

[!INCLUDE [application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)]

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure App Service へのアプリのデプロイ]: ./web-sites-deploy.md



<!--HONumber=Jan17_HO2-->


