---
title: "Azure Toolkit for Eclipse のインストール | Microsoft Docs"
description: "Azure Toolkit for Eclipse をインストールする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 5bb67803be0c9cb2fa21545eaee1d26b85bae3c5


---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse のインストール
Azure Toolkit for Eclipse は、Eclipse 開発環境を使って Azure アプリケーションを簡単に作成、開発、テスト、またデプロイできるテンプレートと機能を提供します。 Azure Toolkit for Eclipse はオープン ソース プロジェクトであり、そのソース コードは、GitHub のプロジェクト サイトから MIT License で入手できます。URL は次のとおりです。

<https://github.com/microsoft/azure-tools-for-java>

次の手順は、Azure Toolkit for Eclipse をインストールする方法を示しています。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse をインストールするには
1. Eclipse を起動します。
2. Eclipse が開いたら、次の図に示すように、**[Help (ヘルプ)]** メニューをクリックし、**[Install New Software (新しいソフトウェアのインストール)]** をクリックします。
   
    ![Azure Toolkit for Eclipse のインストール][01]
3. **[Available Software (利用可能なソフトウェア)]** ダイアログで、**[Work with (対象)]** ボックスに「**http://dl.microsoft.com/eclipse**」と入力し、**Enter** キーを押します。
4. **[Name]** ウィンドウで、**Azure Toolkit for Eclipse** をオンにし、**[Contact all update sites during install to find required software]** をオフにします。 画面は次のようになります。
   
    ![Azure Toolkit for Eclipse のインストール][02]
5. **Azure Toolkit for Eclipse**を展開すると、次の項目が表示されます。
   
   * **Application Insights Plugin for Java**: このコンポーネントによって、アプリケーションとサーバー インスタンスに対する Azure のテレメトリ ログと分析サービスを使用できます。
   * **Azure Access Control Services Filter**: Azure ACS を使用するアプリケーション ユーザーの認証を支援するコンポーネントです。シングル サインオンの利用形態が可能となり、ID のロジックをアプリケーションから切り離すことができます。
   * **Azure Common Plugin**: 他のツールキット コンポーネント全般で必要となる機能を備えたコンポーネントです。
   * **Azure Explorer for Eclipse**: 他のツールキット コンポーネント全般で必要となる機能を備えたコンポーネントです。
   * **Azure Plugin for Eclipse with Java**: Microsoft Azure クラウド向け Java アプリケーションを Eclipse やコマンド ラインによってビルド、テスト、デプロイできるプロジェクトの開発を支援するコンポーネントです。
   * **Azure Web Apps Plugin with Java**: Microsoft Azure Web アプリ コンテナーへの Java Web アプリケーションのデプロイを支援するコンポーネントです。
   * **Microsoft JDBC Driver 4.2 for SQL Server**: SQL Server と Microsoft Azure SQL Database 向けに、Java Platform Enterprise Edition 8 の JDBC API を提供するコンポーネントです。
   * **Package for Apache Qpid Client Libraries for JMS**: Apache Qpid プロジェクトの JMS クライアント ライブラリを備えたコンポーネントです。Microsoft Azure でアプリケーションから AMQP メッセージングを利用するには、このコンポーネントを使用します。
   * **Package for Microsoft Azure Libraries for Java**: Microsoft Azure の各種サービス (ストレージ、Service Bus、サービス ランタイムなど) にアクセスするための API を備えたコンポーネントです。
6. **[次へ]**をクリックします。 (ツールキットのインストール時に異常な遅延が発生する場合は、**[Contact all update sites during install to find required software (インストール中にすべての更新サイトに接続して必要なソフトウェアを調べる)]** がオフになっていることを確認してください)。
7. **[Install Details (インストールの詳細)]** ダイアログで、**[Next (次へ)]** をクリックします。
   
    ![Review Installation Details][03]
8. **[Review Licenses (ライセンスの確認)]** ダイアログで、ライセンス契約の条項を確認します。 ライセンス契約の条項に同意する場合は、**[I accept the terms of the license agreements (ライセンス契約の条項に同意する)]** をクリックし、**[Finish (完了)]** をクリックします。 (残りの手順は、ライセンス契約の条項に同意したことを前提としています。 ライセンス契約の条項に同意しない場合はインストール プロセスを終了します。)
   
    ![Review Licenses][04]
   
    必要なパッケージが Eclipse によってダウンロードされてインストールされます。
   
    ![Installation Progress][05]
9. インストールを完了するために Eclipse を再起動することを求めるメッセージが表示されたら、 **[Yes]**をクリックします。
   
    ![Restart Prompt][06]

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * *Azure Toolkit for Eclipse のインストール (この記事）*
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for Eclipse の新機能]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ のインストール]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for IntelliJ の新機能]

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->



<!--HONumber=Jan17_HO1-->


