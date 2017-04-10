---
title: "IntelliJ で Azure の Java Web アプリをデバッグする | Microsoft Docs"
description: "このチュートリアルでは、Azure Toolkit for IntelliJ を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。"
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 78148648-5a89-4b7d-98f1-9cf8f38dfe8d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 50db8f7d1d514d076af453a34ca3a147e357e905
ms.lasthandoff: 01/05/2017


---
# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>IntelliJ で Azure の Java Web アプリをデバッグする
このチュートリアルでは、 [Azure Toolkit for IntelliJ]を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。 わかりやすくするために、このチュートリアルでは基本的な Java Server Page (JSP) サンプルを使用しますが、Azure でのデバッグであれば Java サーブレットを使用した場合も同様の手順になります。

IntelliJ でデバッグする場合、このチュートリアルの完了時には、アプリケーションが次のような画面に表示された状態になります。

![][01]

## <a name="prerequisites"></a>前提条件
* Java Developer Kit (JDK) v 1.8 以降。
* IntelliJ IDEA Ultimate エディション。 <https://www.jetbrains.com/idea/download/index.html> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、Jetty など)。
* Azure サブスクリプション。<https://azure.microsoft.com/en-us/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for IntelliJ。 詳細については、「 [Azure Toolkit for IntelliJ のインストール]」を参照してください。
* 作成して Azure App Service にデプロイした Dynamic Web Project。「[IntelliJ で Azure 用の Hello World Web アプリを作成する]」の例を参照してください。

## <a name="to-debug-a-java-web-app-on-azure"></a>Azure の Java Web アプリをデバッグするには
このセクションの各手順を実行する際は、Azure に Java Web アプリとしてデプロイした既存の Dynamic Web Project を使用するか、[サンプル Dynamic Web Project] をダウンロードし、「[IntelliJ で Azure 用の Hello World Web アプリを作成する]」の手順に従って Azure にデプロイして使用することができます。 

1. Azure にデプロイした Java Web アプリ プロジェクトを IntelliJ で開きます。
2. **[Run (実行)]** メニューをクリックし、**[Edit Configurations (構成の編集)]** をクリックします。
   
    ![][02]
3. **[Run/Debug Configurations (実行/デバッグ構成)]** ダイアログ ボックスが開いたら、次の手順を実行します。 
   
   1. **[Azure Web App (Azure Web アプリ)]**を選択します。
   2. **+** をクリックして新しい構成を追加します。
   3. 構成の **[Name (名前)]** を入力します。
   4. Azure Toolkit によって入力されている残りの既定値をそのまま使用し、 **[OK]**をクリックします。
      
       ![][03]
4. メニューの右上で、作成した Azure Web アプリのデバッグ構成を選択し、 **[Debug (デバッグ)]**
   
    ![][04]
5. "**Enable remote debugging in the remote Web App now? (リモート Web アプリで今すぐリモート デバッグを有効にしますか?)**" というメッセージが表示されたら、**[OK]** をクリックします。
6. **"Your web app is now ready for remote debugging (Web アプリでリモート デバッグの準備ができました)"** というメッセージが表示されたら、**[OK]** をクリックします。
   
    ![][05]
7. メニューの右上で、作成した Azure Web アプリのデバッグ構成を選択し、 **[Debug (デバッグ)]**をクリックします。
8. Windows コマンド プロンプトまたは Unix shell が開き、デバッグに必要な接続が用意されます。リモート Java Web アプリへの接続に成功するまで、先へ進むことはできません。 Windows を使用している場合は、次のような画面表示になります。
   
    ![][06]
9. JSP ページにブレーク ポイントを挿入し、Java Web アプリの URL をブラウザーで開きます。
   
   1. IntelliJ で **[Azure Explorer]** を開きます。
   2. **[Web Apps]** に移動し、デバッグする Web アプリに移動します。
   3. Web アプリを右クリックし、 **[Open in Browser (ブラウザーで開く)]**をクリックします。
   4. これで、IntelliJ がデバッグ モードに切り替わります。

## <a name="next-steps"></a>次のステップ
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

Azure Web Apps の作成の詳細については、「 [Web Apps の概要]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij.md
[Azure Toolkit for IntelliJ のインストール]: ../azure-toolkit-for-intellij-installation.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web-intellij-create-hello-world-web-app.md
[サンプル Dynamic Web Project]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

