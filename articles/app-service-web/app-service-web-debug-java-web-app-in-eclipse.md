---
title: "Eclipse で Azure の Java Web アプリをデバッグする | Microsoft Docs"
description: "このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。"
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 321d2d19-9ce0-4165-8555-b6b15e671393
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 8a4f22442d030dae602171555bafb3733d61ebd7
ms.lasthandoff: 01/05/2017


---
# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Eclipse で Azure の Java Web アプリをデバッグする
このチュートリアルでは、 [Azure Toolkit for Eclipse]を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。 わかりやすくするために、このチュートリアルでは基本的な Java Server Page (JSP) サンプルを使用しますが、Azure でのデバッグであれば Java サーブレットを使用した場合も同様の手順になります。

Eclipse でデバッグする場合、このチュートリアルの完了時には、アプリケーションが次のような画面に表示された状態になります。

![][01]

## <a name="prerequisites"></a>前提条件
* Java Developer Kit (JDK) v 1.8 以降。
* Eclipse IDE for Java EE Developers Indigo 以降。 <http://www.eclipse.org/downloads/> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、Jetty など)。
* Azure サブスクリプション。<https://azure.microsoft.com/en-us/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for Eclipse。 詳細については、「 [Azure Toolkit for Eclipse のインストール]」を参照してください。
* 作成して Azure App Service にデプロイした Dynamic Web Project。「[Eclipse で Azure 用の Hello World Web アプリを作成する]」の例を参照してください。

## <a name="to-debug-a-java-web-app-on-azure"></a>Azure の Java Web アプリをデバッグするには
このセクションの各手順を実行する際は、Azure に Java Web アプリとしてデプロイした既存の Dynamic Web Project を使用するか、[サンプル Dynamic Web Project] をダウンロードし、「[Eclipse で Azure 用の Hello World Web アプリを作成する]」の手順に従って Azure にデプロイして使用することができます。 

1. Eclipse を開きます。
2. リモート デバッグ用のタイムアウトを構成します。
   
   1. Eclipse で **[Window (ウィンドウ)]** メニューをクリックし、**[Preferences (ユーザー設定)]** をクリックします。
   2. **[Java]** ノードを展開し、**[Debug (デバッグ)]** を選択します。
   3. **[Debugger timeout (ms) (デバッガー タイムアウト (ミリ秒))]** と **[Launch timeout (ms) (起動タイムアウト (ミリ秒))]** の各設定を両方とも `120000` にします。
      
       ![][02]
   4. **[OK]** をクリックして **[Preferences (ユーザー設定)]** ダイアログ ボックスを閉じます。
3. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ビューで、Azure にデプロイした Dynamic Web Project を右クリックします。 コンテキスト メニューが表示されたら、**[Debug As (デバッグ形式)]** を選択し、**[Azure Web App (Azure Web アプリ)]** をクリックします。
   
    ![][03]
4. Dynamic Web Project を初めてデバッグする場合は、**[Debug Configurations (デバッグ構成)]** ダイアログ ボックスが開きます。Toolkit によって **[Connect (接続)]** タブで指定されている既定値はそのまま使用できます。 **[Source (ソース)]** タブで **[Add (追加)]**、**[Java project (Java プロジェクト)]** の順にクリックし、**[Dynamic Web Project]** を選択して **[OK]** をクリックします。 これらの手順を最後まで実行したら、**[Debug (デバッグ)]** をクリックします。
   
    ![][04]
5. "**Enable remote debugging in the remote Web App now? (リモート Web アプリで今すぐリモート デバッグを有効にしますか?)**" というメッセージが表示されたら、**[OK]** をクリックします。
6. **"Your web app is now ready for remote debugging (Web アプリでリモート デバッグの準備ができました)"** というメッセージが表示されたら、**[OK]** をクリックします。
   
    ![][05]
7. **[Debug Configurations (デバッグ構成)]** ダイアログ ボックスが再度表示されたら、**[Debug (デバッグ)]** をクリックします。
8. Windows コマンド プロンプトまたは Unix shell が開き、デバッグに必要な接続が用意されます。リモート Java Web アプリへの接続に成功するまで、先へ進むことはできません。 Windows を使用している場合は、次のような画面表示になります。
   
    ![][06]
9. JSP ページにブレーク ポイントを挿入し、Java Web アプリの URL をブラウザーで開きます。
   
   1. Eclipse で **[Azure Explorer]** を開きます。
   2. **[Web Apps]** に移動し、デバッグする Web アプリに移動します。
   3. Web アプリを右クリックし、 **[Open in Browser (ブラウザーで開く)]**をクリックします。
   4. これで、Eclipse がデバッグ モードに切り替わります。

## <a name="next-steps"></a>次のステップ
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

Azure Web Apps の作成の詳細については、「 [Web Apps の概要]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit for Eclipse のインストール]: ../azure-toolkit-for-eclipse-installation.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web-eclipse-create-hello-world-web-app.md
[サンプル Dynamic Web Project]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

