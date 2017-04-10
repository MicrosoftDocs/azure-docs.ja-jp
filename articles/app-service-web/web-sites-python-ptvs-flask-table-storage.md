---
title: "Python Tools 2.2 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ"
description: "Python Tools for Visual Studio を使用して、Azure テーブル ストレージにデータを保存する Flask Web アプリを作成し、Azure App Service Web Apps にデプロイする方法について説明します。"
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: erikre
editor: 
ms.assetid: d8e70a29-aca1-4010-95f5-cfe769e3be06
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 96b27bdd528b0f57b9e0a9959ead23b8f820a82d
ms.lasthandoff: 02/16/2017


---
# <a name="flask-and-azure-table-storage-on-azure-with-python-tools-22-for-visual-studio"></a>Python Tools 2.2 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ
このチュートリアルでは、 [Python Tools for Visual Studio] のサンプル テンプレートを使用して、単純な投票 Web アプリを作成します。 このチュートリアルは、 [ビデオ](https://www.youtube.com/watch?v=qUtZWtPwbTk)でもご覧いただけます。

この投票 Web アプリでは、リポジトリの抽象化を定義します。そのため、異なる種類のリポジトリ (メモリ内、Azure テーブル ストレージ、MongoDB) を簡単に切り替えることができます。

ここでは、Azure ストレージ アカウントを作成する方法、Web アプリが Azure テーブル ストレージを使用するよう構成する方法、および [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)にアプリケーションを発行する方法を学習します。

Bottle、Flask、Django の各 Web フレームワークと、MongoDB、Azure テーブル ストレージ、MySQL、SQL Database の各サービスを使用した、PTVS での Azure App Service Web Apps の開発を取り上げたその他の記事については、 [Python デベロッパー センター] をご覧ください。 この記事では App Service について重点的に説明していますが、 [Azure Cloud Services]の開発も同様の手順で行います。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2015
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio サンプル VSIX]
* [Azure SDK Tools for VS 2015]
* [Python 2.7 (32 ビット)] または [Python 3.4 (32 ビット)]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-the-project"></a>プロジェクトを作成する
このセクションでは、サンプル テンプレートを使用して Visual Studio プロジェクトを作成します。 仮想環境を作成し、必要なパッケージをインストールします。 次に、既定のメモリ内リポジトリを使用してアプリケーションをローカルで実行します。

1. Visual Studio で、**[ファイル]****[新しいプロジェクト]** の順に選択します。
2. [Python Tools 2.2 for Visual Studio サンプル VSIX] のプロジェクト テンプレートは、**[Python]** の **[サンプル]** にあります。 **[投票 Flask Web プロジェクト]** を選択し、[OK] をクリックしてプロジェクトを作成します。
   
     ![[新しいプロジェクト] ダイアログ](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. 外部のパッケージをインストールするよう求めるメッセージが表示されます。 **[仮想環境にインストールする]**を選択します。
   
     ![External Packages ダイアログ](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. ベース インタープリターとして **[Python 2.7]** または **[Python 3.4]** を選択します。
   
     ![Add Virtual Environment ダイアログ](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. `F5`キーを押して、アプリケーションが動作することを確認します。 既定では、アプリケーションは、構成を必要としないメモリ内リポジトリを使用します。 Web サーバーが停止すると、すべてのデータが失われます。
6. **[Create Sample Polls]**をクリックし、投票内容をクリックして投票します。
   
     ![Web ブラウザー](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
ストレージ操作を行うには、Azure のストレージ アカウントが必要です。 ストレージ アカウントを作成するには、次のステップを実行します

1. [Azure Portal](https://portal.azure.com/)にログインします。
2. ポータルの左上にある **[新規]** アイコンをクリックし、**[データ + ストレージ]** > **[ストレージ アカウント]** の順にクリックします。 **[作成]**をクリックし、ストレージ アカウントに一意の名前を付け、アカウントの新しい [リソース グループ](../azure-resource-manager/resource-group-overview.md) を作成します。
   
      ![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    ストレージ アカウントが作成されると、**[通知]** ボタンに緑色の **[成功]** という文字が点滅し、ストレージ アカウントのブレードが開いて、作成した新しいリソース グループに属していることが示されます。
3. ストレージ アカウントのブレードで **[アクセス キー]** の部分をクリックします。 アカウント名と key1 を書き留めます。
   
      ![構成する](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    この情報は、次のセクションでプロジェクトを構成するために必要です。

## <a name="configure-the-project"></a>プロジェクトを構成する
このセクションでは、先ほど作成したストレージ アカウントを使用するための構成をアプリケーションに対して行います。 Azure ポータルから接続の設定を取得する方法を見ていきます。 その後、アプリケーションをローカルで実行します。

1. Visual Studio のソリューション エクスプローラーで、使用するプロジェクト ノードを右クリックし、 **[プロパティ]**を選択します。 **[デバッグ]** タブをクリックします。
   
     ![Project Debug 設定](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. **[サーバー コマンドのデバッグ]** の **[環境]** で、アプリケーションに必要な環境変数の値を設定します。
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   これにより、 **デバッグを開始**したときに環境変数が設定されます。 **デバッグなしで開始**したときに変数を設定する場合は、**[サーバー コマンドの実行]** でも同じ値を設定します。
   
   Windows コントロール パネルを使用して環境変数を定義してもかまいません。 ソース コードまたはプロジェクト ファイルに資格情報を格納するのを避ける場合には、これがより優れた方法です。 新しい環境変数の値をアプリケーションから利用するためには、Visual Studio を再起動する必要があります。
3. Azure テーブル ストレージ リポジトリを実装するコードは、 **models/azuretablestorage.py**にあります。 Python から Table サービスを使用する方法の詳細については、 [ドキュメント] をご覧ください。
4. `F5`キーでアプリケーションを実行します。 **[サンプル投票の作成]** で作成された投票と、投票によって送信されたデータは、Azure テーブル ストレージでシリアル化されます。
   
   > [!NOTE]
   > Python 2.7 仮想環境では、Visual Studio で例外による中断が発生する場合があります。  `F5` を押して Web プロジェクトの読み込みを続行します。
   > 
   > 
5. アプリケーションが、**Azure Table Storage** リポジトリを使用していることを確認するには、**[情報]** ページを確認します。
   
     ![Web ブラウザー](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## <a name="explore-the-azure-table-storage"></a>Azure テーブル ストレージを調査する
ストレージ テーブルは、Visual Studio の Cloud Explorer を使用して簡単に表示したり編集したりできます。 このセクションでは、投票アプリケーションに使用されているテーブルの内容をサーバー エクスプローラーを使用して表示します。

> [!NOTE]
> これには、Microsoft Azure Tools をインストールする必要があります。このツールは、[Azure SDK for .NET] の一部として使用できます。
> 
> 

1. **Cloud Explorer**を開きます。 **[ストレージ アカウント]**、ストレージ アカウント名、**[テーブル]** の順に展開します。
   
     ![Cloud Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. **投票**テーブルか**選択肢**テーブルをダブルクリックすると、ドキュメント ウィンドウでテーブルの内容を表示し、エンティティを追加、削除、編集できます。
   
     ![テーブル照会結果](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Web アプリを Azure App Service に発行する
Azure .NET SDK を使用すると、Web アプリを Azure App Service に簡単にデプロイできます。

1. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックし、**[発行]** をクリックします。
   
     ![Publish Web ダイアログ](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. **[Microsoft Azure Web Apps]**をクリックします。
3. **[新規]** をクリックして、新しい Web アプリを作成します。
4. 次のフィールドに入力し、 **[作成]**をクリックします。
   
   * **[Web アプリケーション名]**
   * **[App Service プラン]**
   * **[リソース グループ]**
   * **[リージョン]**
   * **[データベース サーバー]** は、**[データベースなし]** のままにしておきます。
5. 他のすべての既定値をそのまま使用し、 **[発行]**をクリックします。
6. Web ブラウザーが自動的に開いて、発行した Web アプリが表示されます。 [情報] ページを参照すると、**Azure Table Storage** リポジトリではなく、**メモリ内**リポジトリを使用していることが確認できます。
   
   これは、Azure App Service の Web Apps インスタンスで環境変数が設定されていないので、 **settings.py**で指定された既定値が使用されているためです。

## <a name="configure-the-web-apps-instance"></a>Web Apps インスタンスを構成する
このセクションでは、Web Apps インスタンスの環境変数を構成します。

1. [Azure Portal](https://portal.azure.com) で、**[参照]** > **[App Services]**> Web アプリ名の順にクリックして、Web アプリのブレードを開きます。
2. Web アプリのブレードで、**[すべての設定]**、**[アプリケーションの設定]** の順にクリックします。
3. 下方向へ **[アプリケーション設定]** セクションまでスクロールし、上の「**プロジェクトを構成する**」セクションで説明したように **REPOSITORY\_NAME**、**STORAGE\_NAME** および **STORAGE\_KEY** の値を設定します。
   
     ![アプリ設定](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. **[Save]**をクリックします。 変更の適用を知らせる通知を受け取ったら、Web アプリのメイン ブレードで **[参照]** をクリックします。
5. 想定どおりに Web アプリが **Azure テーブル ストレージ** リポジトリを使用して動作していることが確認できます。
   
   ご利用ありがとうございます。
   
     ![Web ブラウザー](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## <a name="next-steps"></a>次のステップ
Python Tools for Visual Studio、Flask および Azure テーブル ストレージの詳細については、以下のリンクを参照してください。

* [Python Tools for Visual Studio のドキュメント]
  * [Web プロジェクト]
  * [クラウド サービス プロジェクト]
  * [Microsoft Azure でのリモート デバッグ]
* [Flask のドキュメント]
* [Azure Storage]
* [Azure SDK for Python]
* [Python からテーブル ストレージ サービスを使用する方法]

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Python デベロッパー センター]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md
[ドキュメント]: ../storage/storage-python-how-to-use-table-storage.md
[Python からテーブル ストレージ サービスを使用する方法]: ../storage/storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Azure SDK for .NET]: http://azure.microsoft.com/downloads/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio のドキュメント]: http://aka.ms/ptvsdocs
[Flask のドキュメント]: http://flask.pocoo.org/
[Microsoft Azure でのリモート デバッグ]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web プロジェクト]: http://go.microsoft.com/fwlink/?LinkId=624027
[クラウド サービス プロジェクト]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure Storage]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python

