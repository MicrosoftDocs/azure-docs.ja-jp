---
title: "Azure Toolkit for IntelliJ のインストール | Microsoft Docs"
description: "Azure Toolkit for IntelliJ IDEA をインストールする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 349252e6629c0eda4d603399345206903d530578
ms.lasthandoff: 01/05/2017


---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ のインストール
Azure Toolkit for IntelliJ は、IntelliJ IDEA 開発環境を使って Azure アプリケーションを簡単に作成、開発、テスト、デプロイできるテンプレートと機能を提供します。 Azure Toolkit for IntelliJ はオープン ソース プロジェクトであり、そのソース コードは、GitHub のプロジェクト サイトから MIT License で入手できます。URL は次のとおりです。

<https://github.com/microsoft/azure-tools-for-java>

Azure Toolkit for IntelliJ には&2; とおりのインストール方法があります。[Settings]\(設定) ダイアログ ボックスを使用する方法と、スタート画面の [Configure]\(構成) メニューを使用する方法です。その両方のインストール方法を以降の手順で説明します。

[!INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Azure Toolkit for IntelliJ を [Settings]\(設定) ダイアログ ボックスからインストールするには
1. IntelliJ IDEA を起動します。
2. IntelliJ IDEA が起動したら **[File (ファイル)]** をクリックし、**[Settings (設定)]** をクリックします。
   
    ![Open the IntelliJ IDEA Settings Dialog Box][01a]
3. [Settings (設定)] ダイアログ ボックスの **[Plugins (プラグイン)]** をクリックし、**[Browse repositories (リポジトリの参照)]** をクリックします。
   
    ![IntelliJ IDEA Settings Dialog Box][02a]
4. **[Browse Repositories]** (リポジトリの参照) ダイアログ ボックスの検索ボックスに「Azure」と入力します。 **Azure Toolkit for IntelliJ** を強調表示して **Install (インストール)** をクリックします。
   
    ![Search for the Azure Toolkit for IntelliJ][03]
   
    インストールの進行状況がダイアログ ボックスに表示されます。
   
    ![Installation progress][04]
5. インストールが完了したら、 **[Restart IntelliJ IDEA]**(IntelliJ IDEA の再起動) をクリックします。
   
    ![[Restart IntelliJ IDEA]][05]
6. **[OK]** をクリックして [Settings]\(設定) ダイアログ ボックスを閉じます。
   
    ![Close IntelliJ IDEA Settings Dialog Box][06]
7. IntelliJ IDEA をすぐに再起動するか、後から再起動するかを求められたら、 **[Restart]**(再起動) をクリックします。
   
    ![[Restart IntelliJ IDEA]][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Azure Toolkit for IntelliJ をスタート画面からインストールするには
1. IntelliJ IDEA を起動します。
2. IntelliJ IDEA のスタート画面が表示されたら、**[Configure (構成)]** をクリックし、**[Plugins (プラグイン)]** をクリックします。
   
    ![Install IntelliJ IDEA Plugins][01b]
3. **[Plugins (プラグイン)]** ダイアログ ボックスで **[Browse repositories (リポジトリの参照)]** をクリックします。
   
    ![Browse IntelliJ IDEA Plugin Repositories][02b]
4. **[Browse Repositories]** (リポジトリの参照) ダイアログ ボックスの検索ボックスに「Azure」と入力します。 **Azure Toolkit for IntelliJ** を強調表示して **Install (インストール)** をクリックします。
   
    ![Search for the Azure Toolkit for IntelliJ][03]
   
    インストールの進行状況がダイアログ ボックスに表示されます。
   
    ![Installation progress][04]
5. インストールが完了したら、 **[Restart IntelliJ IDEA]**(IntelliJ IDEA の再起動) をクリックします。
   
    ![[Restart IntelliJ IDEA]][05]
6. IntelliJ IDEA をすぐに再起動するか、後から再起動するかを求められたら、 **[Restart]**(再起動) をクリックします。
   
    ![[Restart IntelliJ IDEA]][07]

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse のインストール]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for Eclipse の新機能]
* [Azure Toolkit for IntelliJ]
  * *Azure Toolkit for IntelliJ のインストール (この記事)*
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for IntelliJ の新機能]

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png

