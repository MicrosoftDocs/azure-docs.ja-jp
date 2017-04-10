---
title: "クラシック Azure VM で Java アプリケーション サーバーを実行する | Microsoft Docs"
description: "このチュートリアルでは、クラシック デプロイメント モデルで作成されたリソースを使用して、Windows 仮想マシンを作成して Apache Tomcat アプリケーション サーバーを実行するように構成する方法を説明します。"
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6e02f42613808bcb13c0057e9f8fcc1c02273e77
ms.lasthandoff: 03/25/2017


---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>クラシック デプロイ モデルで作成された仮想マシンに Java アプリケーション サーバーをインストールする方法
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Java 8 と Tomcat を使用して Web アプリをデプロイするための Resource Manager テンプレートについては、[こちら](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/)をご覧ください。

Azure では、仮想マシンを使用してサーバー機能を実現することができます。 たとえば、Apache Tomcat などの Java アプリケーション サーバーをホストできるように、Azure で実行する仮想マシンを構成できます。

このガイドを完了すると、Azure で実行する仮想マシンを作成し、Java アプリケーション サーバーを実行するように構成する方法を理解できます。 次の作業を行う方法について説明します。

* Java Development Kit (JDK) インストール済みの仮想マシンを作成する方法
* 仮想マシンにリモート ログインする方法
* 仮想マシンに Java アプリケーション サーバー (Apache Tomcat) をインストールする方法
* 仮想マシンのエンドポイントを作成する方法
* ファイアウォールでアプリケーション サーバー用にポートを開く方法

インストールが完了すると、仮想マシンで Tomcat が実行されます。

![仮想マシンで実行されている Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>仮想マシンを作成するには
1. [Azure ポータル](https://portal.azure.com)にサインインします。  
2. **[新規]**、**[Compute]** の順にクリックし、**[おすすめアプリ]** で **[すべて表示]** をクリックします。
3. **[JDK]** をクリックし、**[JDK]** ウィンドウで **[JDK 8]** をクリックします。  
   JDK 8 での実行に対応していないレガシ アプリケーションがある場合は、**JDK 6** および **JDK 7** をサポートする仮想マシン イメージを使用できます。
4. [JDK 8] ウィンドウで **[クラシック]** を選択し、**[作成]** をクリックします。
5. **[基本]** ブレードで、次の操作を行います。
   1. 仮想マシンの名前を指定します。
   2. **[ユーザー名]** フィールドに、管理者の名前を入力します。 この名前と、次のフィールドに入力する関連付けられたパスワードを覚えておいてください。 これらは、仮想マシンにリモートでサインインするときに必要になります。
   3. **[新しいパスワード]** フィールドにパスワードを入力し、**[パスワードの確認]** フィールドにもう一度パスワードを入力します。 これは Administrator アカウントのパスワードです。
   4. 適切な**サブスクリプション**を選択します。
   5. **[リソース グループ]** で、**[新規作成]** をクリックし、新しいリソース グループの名前を入力します。 または、**[既存のものを使用]** をクリックし、使用可能なリソース グループのいずれかを選択します。
   6. 仮想マシンが存在する場所 (**[米国中南部]** など) を選択します。
6. **[次へ]**をクリックします。
7. **[Virtual machine image size (仮想マシン イメージのサイズ)]** ブレードで、**[A1 Standard]** または別の適切なイメージを選択します。
8. **[選択]**をクリックします。

9. **[設定]** ブレードで **[OK]** をクリックします。 Azure で提供される既定値を使用できます。  
10. **[概要]** ブレードで、**[OK]** をクリックします。

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>仮想マシンにリモート ログインするには
1. [Azure Portal](https://portal.azure.com) にログオンします。
2. **[仮想マシン (クラシック)]** をクリックします。 必要に応じて、サービス カテゴリの左下隅にある **[その他のサービス]** をクリックします。 **[仮想マシン (クラシック)]** エントリは、**[Compute]** グループに表示されます。
3. ログインする仮想マシンの名前をクリックします。
4. 仮想マシンが起動したら、ウィンドウの上部のメニューを使用して接続できます。
5. **[接続]**をクリックします。
6. 表示される画面で必要に応じて入力して、仮想マシンに接続します。 通常は、接続の詳細が含まれた .rdp ファイルを保存するか開きます。 .rdp ファイルの最初の行の最後の部分として、url: ポートをコピーし、それをリモートのログイン アプリケーション内に貼り付けなければならない場合があります。

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>仮想マシンに Java アプリケーション サーバーをインストールするには
Java アプリケーション サーバーを仮想マシンにコピーすることも、インストーラーを使用して Java アプリケーション サーバーをインストールすることもできます。

このチュートリアルでは、インストールする Java アプリケーション サーバーとして Tomcat を使用します。

1. 仮想マシンにログインしている場合は、 [Apache Tomcat](http://tomcat.apache.org/download-80.cgi)のブラウザー セッションを開きます。
2. **32 ビット/64 ビット Windows サービスのインストーラー**のリンクをダブルクリックします。 この場合、Tomcat は Windows サービスとしてインストールされます。
3. 確認メッセージが表示されたら、インストーラーを実行します。
4. **Apache Tomcat Setup** ウィザードの指示に従って、Tomcat をインストールします。 このチュートリアルでは、既定値のまま進めて問題ありません。 **[Completing the Apache Tomcat Setup Wizard]** ダイアログ ボックスが表示されたら、必要に応じて **[Run Apache Tomcat]** チェック ボックスをオンにすることにより、Tomcat を起動することができます。 **[Finish]** をクリックして Tomcat のセットアップ プロセスを完了します。

## <a name="to-start-tomcat"></a>Tomcat を開始するには

Tomcat を手動で起動するには、仮想マシンでコマンド プロンプトを開き、**net&nbsp;start&nbsp;Tomcat8** コマンドを実行します。

Tomcat が実行されたら、仮想マシンのブラウザーで URL として <http://localhost:8080> を入力することで Tomcat にアクセスできます。

Tomcat が実行されていることを外部コンピューターから確認するには、エンドポイントを作成してポートを開く必要があります。

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>仮想マシンのエンドポイントを作成するには
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[仮想マシン (クラシック)]** をクリックします。
3. Java アプリケーション サーバーを実行している仮想マシンの名前をクリックします。
4. **[エンドポイント]**をクリックします。
5. **[追加]**をクリックします。
6. **[エンドポイントの追加]** ダイアログ ボックスで、次の操作を行います。
   1. エンドポイントの [名前] \(**HttpIn**など) を指定します。
   2. プロトコルとして **[TCP]** を選択します。
   3. [パブリック ポート] に **80** を指定します。
   4. [プライベート ポート] に **8080** を指定します。
   5. Floating IP アドレスで **[無効]** を選択します。
   6. アクセス制御リストはそのままにしておきます。
   7. **[OK]** をクリックしてダイアログ ボックスを閉じ、エンドポイントを作成します。

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>ファイアウォールで仮想マシン用にポートを開くには
1. 仮想マシンにログインします。
2. **Windows の [スタート]**をクリックします。
3. **[コントロール パネル]**をクリックします。
4. **[システムとセキュリティ]**、**[Windows ファイアウォール]**、**[詳細設定]** の順にクリックします。
5. **[受信の規則]**、**[新しい規則]** の順にクリックします。  
   ![新しい受信の規則][NewIBRule]
6. **[規則の種類]** で、**[ポート]** を選択して **[次へ]** をクリックします。  
   ![新しい受信の規則のポート][NewRulePort]
7. **[プロトコルとポート]** 画面で、**[TCP]** を選択し、**[特定のローカル ポート]** を選択して「**8080**」と入力し、**[次へ]** をクリックします。  
  ![新しい受信の規則][NewRuleProtocol]
8. **[操作]** 画面で、**[接続を許可する]** を選択し、**[次へ]** をクリックします。
   ![新しい受信の規則の操作][NewRuleAction]
9. **[プロファイル]** 画面で、**[ドメイン]**、**[プライベート]**、および **[パブリック]** の各チェック ボックスがオンになっていることを確認し、**[次へ]** をクリックします。
   ![新しい受信の規則のプロファイル][NewRuleProfile]
10. **[名前]** 画面で、**HttpIn** などの規則の名前を指定し (ただし、規則の名前がエンドポイント名と一致する必要はありません)、**[完了]** をクリックします。  
    ![新しい受信の規則の名前][NewRuleName]

これで、外部ブラウザーから Tomcat の Web サイトを表示できます。 ブラウザーのアドレス ウィンドウに、**http://*your\_DNS\_name*.cloudapp.net**(***your\_DNS\_name*** は、仮想マシンの作成時に指定した DNS 名) という形式の URL を入力します。

## <a name="application-lifecycle-considerations"></a>アプリケーションのライフサイクルについて
* 独自の Web アプリケーション アーカイブ (WAR) を作成し、 **webapps** フォルダーに追加することもできます。 たとえば、基本的な Java Service Page (JSP) 動的 Web プロジェクトを作成し、WAR ファイルとしてエクスポートします。 次に、仮想マシン上の Apache Tomcat **webapps** フォルダーに WAR をコピーし、ブラウザーで実行します。
* 既定では、Tomcat サービスがインストールされると、手動で開始する設定が適用されます。 この設定は、サービス スナップインを使用して、自動的に開始する設定に変更することができます。 サービス スナップインを起動するには、**Windows の [スタート]**、**[管理ツール]**、**[サービス]** の順にクリックします。 **Apache Tomcat** サービスをダブルクリックし、**[スタートアップの種類]** を **[自動]** に設定します。

    ![サービスが自動的に起動するように設定する][service_automatic_startup]

    Tomcat を自動的に起動する利点は、仮想マシンが再起動されたとき (再起動を必要とするソフトウェア更新プログラムのインストール後など) に実行が開始されることです。

## <a name="next-steps"></a>次のステップ
Java アプリケーションに含めることのできるその他のサービス (Azure Storage、Service Bus、SQL Database など) について確認します。 詳細については、[Java デベロッパー センター](https://azure.microsoft.com/develop/java/)を参照してください。

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->

