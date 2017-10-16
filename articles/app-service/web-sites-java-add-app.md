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
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 1309985d7f1b93230b38ada2ee2687b1db10a791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Azure App Service Web Apps への Java アプリケーションの追加
「[Azure App Service での Java Web アプリの作成](app-service-web-get-started-java.md)」の説明にあるように、Java Web アプリを [Azure App Service][Azure App Service] で初期設定した後、WAR を **webapps** フォルダーに配置してアプリケーションをアップロードすることができます。

**webapps** フォルダーへのナビゲーション パスは Web アプリのインスタンス設定方法によって異なります。

* Azure Marketplace を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\home\site\wwwroot\bin\application\_server\webapps** という形式になります (**application\_server** は、Web Apps のインスタンスで有効になっているアプリケーション サーバーの名前です)。 
* Azure の構成 UI を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\home\site\wwwroot\webapps** という形式になります。 

[継続的な統合シナリオ](app-service-continuous-deployment.md)などで、ソース管理を使用してアプリケーションや Web ページをアップロードできます。 FTP も、アプリケーションまたは Web ページをアップロードするためのオプションです。FTP 経由でのアプリケーションのデプロイの詳細については、「[Deploy your app using FTP](app-service-deploy-ftp.md)」 (FTP を利用したアプリのデプロイ) を参照してください。

WAR ファイルを **webapps** フォルダーにアップロードすると、Tomcat アプリケーション サーバーで WAR ファイルの追加が検出され、WAR ファイルが自動的に読み込まれます。 ルート ディレクトリにファイル (WAR ファイル以外) をコピーした場合は、それらのファイルを使用する前に、アプリケーション サーバーの再起動が必要になります。 Azure で実行されている Tomcat Java Web アプリの自動読み込み機能は、 **webapps** フォルダーに追加される新しい WAR ファイル、または新しいファイルやディレクトリに基づいて動作します。 

<a name="see-also"></a>

## <a name="see-also"></a>関連項目
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
