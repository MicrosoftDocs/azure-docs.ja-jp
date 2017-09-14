---
title: "Spring Boot アプリケーションを Azure App Service にデプロイする | Microsoft Docs"
description: "このチュートリアルは、開発者に Spring Boot Getting Started Web アプリを Azure App Service にデプロイする手順について説明します。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 0c388862d927a1492745832225c686670c071f86
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Spring Boot アプリケーションを Azure App Service にデプロイする

**[Spring Framework]** は、Java 開発者がエンタープライズ レベルのアプリケーションを作成するのに役立つオープン ソースのソリューションであり、そのプラットフォームの上に構築されたより一般的なプロジェクトの 1 つが、スタンドアロンの Java アプリケーションを作成するための簡略化されたアプローチを提供する [Spring Boot] です。

このチュートリアルでは、サンプルの Spring Boot Getting Started Web アプリを作成し、それを [Azure App Service] にデプロイする方法について説明します。

### <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、次のものが必要です。

* Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、または[無料の Azure アカウント]にサインアップできます。
* 最新の [Java Developer Kit (JDK)]。
* Apache の [Maven] 構築ツール (バージョン 3)。
* [Git] クライアント。

## <a name="create-the-spring-boot-getting-started-web-app"></a>Spring Boot Getting Started Web アプリを作成する

次の手順では、単純な Spring Boot Web アプリケーションを作成し、それをローカルにテストするために必要な手順について説明します。

1. コマンド プロンプトを開き、アプリケーションを保持するためのローカル ディレクトリを作成して、そのディレクトリに変更します。次に例を示します。
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- または --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. [Spring Boot Getting Started] サンプル プロジェクトを今作成したディレクトリに複製します。次に例を示します。
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Maven を使用して JAR ファイルを構築します。次に例を示します。
   ```
   mvn package
   ```

1. Web アプリが作成されたら、JAR ファイルにディレクトリを変更し、Web アプリを起動します。次に例を示します。
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. Web ブラウザーを使用して http://localhost:8080 を参照することによって Web アプリをテストするか、または curl が使用可能な場合は次の例のような構文を使用します。
   ```
   curl http://localhost:8080
   ```

1. **Greetings from Spring Boot! (Spring Boot からのあいさつ)** というメッセージが表示されます。

   ![サンプル アプリを見る][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Java で使用する Azure Web アプリを作成する

次の手順では、Azure Web アプリを作成し、Java に必要な設定を構成して、FTP 資格情報を構成する手順について説明します。

1. [Azure Portal] を参照して、ログインします。

1. Azure Portal で自分のアカウントにログインしたら、**[App Services]** のメニュー アイコンをクリックします。
   
   ![Azure Portal][AZ01]

1. **[App Services]** ページが表示されたら、**[+ 追加]** をクリックして新しい App Service を作成します。

   ![App Service を作成する][AZ02]

1. Web アプリ テンプレートの一覧が表示されたら、基本的な Microsoft Web アプリのリンクをクリックします。

   ![Web アプリ テンプレート][AZ03]

1. Web アプリ テンプレートの情報ページが表示されたら、**[作成]** をクリックします。

   ![Create Web App][AZ04]

1. Web アプリの一意の名前を指定し、追加の設定をすべて指定してから、**[作成]** をクリックします。

   ![Web アプリの設定を作成する][AZ05]

1. Web アプリが作成されたら、**[App Services]** のメニュー アイコンをクリックしてから、新しく作成された Web アプリをクリックします。

   ![Web アプリを一覧表示する][AZ06]

1. Web アプリが表示されたら、次の手順を使用して Java バージョンを指定します。

   a. **[アプリケーションの設定]** メニュー項目をクリックします。

   b. Java バージョンとして **[Java 8]** を選択します。

   c. マイナー Java バージョンとして **[Newest] \(最新)** を選択します。

   d. Web コンテナーとして **[Newest Tomcat 8.5] \(最新の Tomcat 8.5)** を選択します。 (このコンテナーは、実際には使用されません。Azure は、Spring Boot アプリケーションからコンテナーを使用します。)

   e. [ **Save**] をクリックします。

   ![アプリケーションの設定][AZ07]

1. 次の手順を使用して FTP デプロイ資格情報を指定します。

   a. **[デプロイ資格情報]** メニュー項目をクリックします。

   b. ユーザー名とパスワードを指定します。

   c. [ **Save**] をクリックします。

   ![デプロイ資格情報を指定する][AZ08]

1. 次の手順を使用して FTP 接続情報を取得します。

   a. **[デプロイ資格情報]** メニュー項目をクリックします。

   b. 完全な FTP ユーザー名と URL をコピーし、それらをこのチュートリアルの次のセクションのために保存します。

   ![FTP URL と資格情報][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>Spring Boot Web アプリを Azure にデプロイする

次の手順では、Spring Boot Web アプリを Azure にデプロイする手順について説明します。

1. Windows のメモ帳などのテキスト エディタを開き、次のテキストを新しいドキュメントに貼り付けてから、そのファイルを *web.config* として保存します。
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. *web.config* ファイルをシステムに保存した後、このチュートリアルの前のセクションの URL、ユーザー名、およびパスワードを使用して FTP 経由で Web アプリに接続します。 For example:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. リモート ディレクトリを Web アプリのルート フォルダー (*/site/wwwroot*) に変更してから、Spring Boot アプリケーションの JAR ファイルと前の *web.config* をコピーします。 For example:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. JAR および *web.config* ファイルを Web アプリにデプロイした後、Azure Portal を使用して Web アプリを再起動する必要があります。

   ![][AZ10]

1. Web ブラウザーを使用して Web アプリの URL を参照することによって Web アプリをテストするか、または curl が使用可能な場合は次の例のような構文を使用します。
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. **Greetings from Spring Boot! (Spring Boot からのあいさつ)** というメッセージが表示されます。

   ![サンプル アプリを見る][SB02]

## <a name="next-steps"></a>次のステップ

Azure での Spring Boot アプリケーションの使用の詳細については、次の記事を参照してください。

* [Azure Container Service で Spring Boot アプリケーションを Linux にデプロイする](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [Azure Container Service で Spring Boot アプリケーションを Kubernetes クラスターにデプロイする](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター] と[Java Tools for Visual Studio Team Services] を参照してください。

FTP を使用した Azure への Web アプリのデプロイの詳細については、「[FTP/S を使用した Azure App Service へのアプリのデプロイ]」を参照してください。

Spring Boot サンプル プロジェクトの詳細については、「[Spring Boot Getting Started]」を参照してください。

独自の Spring Boot アプリケーションの使用開始に関するヘルプについては、「**Spring Initializr**」(https://start.spring.io/) を参照してください。

Web アプリの追加設定の構成の詳細については、「[Configure web apps in Azure App Service (Azure App Service で Web アプリを構成する)]」を参照してください。

<!-- URL List -->

[Azure App Service]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure Portal]: https://portal.azure.com/
[Configure web apps in Azure App Service (Azure App Service で Web アプリを構成する)]: /azure/app-service-web/web-sites-configure
[FTP/S を使用した Azure App Service へのアプリのデプロイ]: https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp
[無料の Azure アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot Getting Started]: https://github.com/spring-guides/gs-spring-boot
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png

