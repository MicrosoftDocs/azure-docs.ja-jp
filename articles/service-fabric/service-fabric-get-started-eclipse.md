---
title: "Eclipse 用の Azure Service Fabric プラグイン | Microsoft Docs"
description: "Eclipse 用の Service Fabric プラグインの概要を紹介します。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0407eab7e70649999ba07730425366b7b62e4e7a
ms.lasthandoff: 03/22/2017


---

# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Eclipse Java アプリケーション開発用の Service Fabric プラグイン
Eclipse は、Java 開発者の間で最も広く使用されている統合開発環境 (IDE) の 1 つです。 この記事では、Eclipse 開発環境をセットアップして Azure Service Fabric を操作する方法について説明します。 Service Fabric プラグインのインストール、Service Fabric アプリケーションの作成、ローカルまたはリモートの Service Fabric クラスターへの Service Fabric アプリケーションのデプロイを Eclipse Neon で行う方法を確認します。

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Eclipse Neon への Service Fabric プラグインのインストールまたは更新
Service Fabric プラグインを Eclipse にインストールすることができます。 このプラグインを使用すると、Java サービスのビルドとデプロイの手順を簡素化できます。

1.  最新バージョンの Eclipse Neon と Buildship (1.0.17 以降) がインストールされていることを確認します。
    -   インストールされているコンポーネントのバージョンを確認するには、Eclipse Neon で **[Help (ヘルプ)]** > **[Installation Details (インストールの詳細)]** の順に移動します。
    -   Buildship を更新するには、「[Eclipse Buildship: Eclipse Plug-ins for Gradle (Eclipse Buildship: Gradle 用の Eclipse プラグイン)][buildship-update]」を参照してください。
    -   Eclipse Neon の更新プログラムを確認してインストールするには、**[Help (ヘルプ)]** > **[Check for Updates (更新プログラムの確認)]** の順に移動します。

2.  Service Fabric プラグインをインストールするには、Eclipse Neon で **[Help (ヘルプ)]** > **[Install New Software (新しいソフトウェアのインストール)]** の順に移動します。
  1.    **[Work with (作業対象)]** ボックスに、「**http://dl.windowsazure.com/eclipse/servicefabric**」と入力します。
  2.    **[追加]**をクリックします。
    ![Eclipse Neon 用の Service Fabric プラグイン][sf-eclipse-plugin-install]
  3.    Service Fabric プラグインを選択して **[Next (次へ)]** をクリックします。
  4.    インストール手順を完了し、マイクロソフト ソフトウェア ライセンス条項に同意します。

Service Fabric プラグインを既にインストールしてある場合は、最新バージョンを使用していることを確認してください。 使用できる更新プログラムを確認するには、**[Help (ヘルプ)]** > **[Installation Details (インストールの詳細)]** の順に移動します。 インストールされているプラグインの一覧で Service Fabric を選択して、**[Update (更新)]** をクリックします。 使用できる更新プログラムがインストールされます。

> [!NOTE]
> Service Fabric プラグインのインストールまたは更新に時間がかかる場合、Eclipse の設定が原因である可能性があります。 Eclipse は、Eclipse インスタンスに登録されている更新サイトに対するすべての変更のメタデータを収集します。 Service Fabric プラグインの更新プログラムを確認してインストールする手順を高速化するには、**[Available Software Sites (利用できるソフトウェア サイト)]** に移動します。 Service Fabric プラグインの場所 (http://dl.windowsazure.com/eclipse/servicefabric) を指すサイトを除き、すべてのサイトのチェック ボックスをオフにします。

## <a name="create-a-service-fabric-application-in-eclipse"></a>Eclipse での Service Fabric アプリケーションの作成

1.  Eclipse Neon で、**[File (ファイル)]** > **[New (新規)]** > **[Other (その他)]** の順に移動します。 **Service Fabric プロジェクト**を選択して **[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 1][create-application/p1]

2.  プロジェクトの名前を入力し、**[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 2][create-application/p2]

3.  テンプレートの一覧で **[サービス テンプレート]** を選択します。 サービス テンプレートの種類 (アクター、ステートレス、コンテナー、またはゲスト バイナリ) を選択して、**[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 3][create-application/p3]

4.  サービス名とサービスの詳細を入力して、**[Finish (完了)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 4][create-application/p4]

5. Service Fabric プロジェクトを初めて作成する場合、**[Open Associated Perspective (関連付けられている観点を開く)]** ダイアログ ボックスで **[Yes (はい)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 5][create-application/p5]

6.  新しいプロジェクトは次のようになります。

    ![新しい Service Fabric プロジェクトのページ 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Eclipse での Service Fabric アプリケーションのビルドとデプロイ

1.  新しい Service Fabric アプリケーションを右クリックして、**[Service Fabric]** を選択します。

    ![[Service Fabric] の右クリック メニュー][publish/RightClick]

2. サブメニューで目的のオプションを選択します。
    -   クリーニングなしでアプリケーションをビルドするには、**[Build Application (アプリケーションのビルド)]** をクリックします。
    -   アプリケーションのクリーン ビルドを実行するには、**[Rebuild Application (アプリケーションのリビルド)]** をクリックします。
    -   ビルド アーティファクトのアプリケーションをクリーンするには、**[Clean Application (アプリケーションのクリーン)]** をクリックします。

3.  このメニューから、アプリケーションのデプロイ、デプロイ解除、発行も実行できます。
    -   ローカル クラスターにデプロイするには、**[Deploy Application (アプリケーションのデプロイ)]** をクリックします。
    -   **[Publish Application (アプリケーションの発行)]** ダイアログ ボックスで、次のいずれかの発行プロファイルを選択します。
        -  **Local.json**
        -  **Cloud.json**

     これらの JavaScript Object Notation (JSON) ファイルには、ローカルまたはクラウド (Azure) のクラスターに接続するために必要な情報 (接続エンドポイントやセキュリティ情報など) が格納されます。

  ![Service Fabric の発行メニュー][publish/Publish]

Eclipse 実行構成を使用して Service Fabric アプリケーションをデプロイするという方法もあります。

  1.    **[Run (実行)]** > **[Run Configurations (構成の実行)]** の順に移動します。
  2.    **[Gradle Project (Gradle プロジェクト)]** で **[ServiceFabricDeployer]** 実行構成を選択します。
  3.    右側のウィンドウの **[Arguments (引数)]** タブで、**[publishProfile]** として **[local (ローカル)]** または **[cloud (クラウド)]** を選択します。  既定値は **[local (ローカル)]** です。 リモート クラスターまたはクラウド クラスターにデプロイするには、**[cloud (クラウド)]** を選択します。
  4.    必要に応じて **Local.json** または **Cloud.json** を編集して、発行プロファイルに正しい情報が入力されているようにします。 エンドポイントの詳細とセキュリティ資格情報を追加または更新できます。
  5.    **[Working Directory (作業ディレクトリ)]** が、デプロイするアプリケーションを指していることを確認します。 アプリケーションを変更するには、**[Workspace (ワークスペース)]** ボタンをクリックし、目的のアプリケーションを選択します。
  6.    **[Apply (適用)]** をクリックし、**[Run (実行)]** をクリックします。

すぐにアプリケーションのビルドとデプロイが行われます。 Service Fabric Explorer でデプロイの状態を監視できます。  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric アプリケーションへの Service Fabric サービスの追加

既存の Service Fabric アプリケーションに Service Fabric サービスを追加するには、次の手順を実行します。

1.  サービスの追加先となるプロジェクトを右クリックし、**[Service Fabric]** をクリックします。

    ![Service Fabric のサービス追加のページ 1][add-service/p1]

2.  **[Add Service Fabric Service (Service Fabric サービスの追加)]** をクリックし、一連の手順を完了してサービスをプロジェクトに追加します。
3.  プロジェクトに追加するサービス テンプレートを選択して、**[Next (次へ)]** をクリックします。

    ![Service Fabric のサービス追加のページ 2][add-service/p2]

4.  (必要に応じて他の詳細と共に) サービス名を入力し、**[Add Service (サービスの追加)]** ボタンをクリックします。  

    ![Service Fabric のサービス追加のページ 3][add-service/p3]

5.  サービスの追加後、プロジェクト全体の構造は次のようになります。

    ![Service Fabric のサービス追加のページ 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Service Fabric Java アプリケーションのアップグレード

アップグレード シナリオの一例を挙げます。たとえば、Eclipse で Service Fabric プラグインを使用して **App1** プロジェクトを作成したとします。 また、そのプラグインを使用してプロジェクトをデプロイし、**fabric:/App1Application** というアプリケーションを作成してあります。 アプリケーションの種類は **App1AppicationType**、アプリケーションのバージョンは 1.0 です。 この場合に、可用性を維持しつつアプリケーションをアップグレードする必要が生じたとしましょう。

最初にアプリケーションに変更を加え、変更されたサービスをリビルドします。 変更されたサービスのマニフェスト ファイル (ServiceManifest.xml) を、サービスの更新されたバージョン (と関連する Code、Config、Data) で更新します。 さらに、アプリケーションのマニフェスト (ApplicationManifest.xml) を、アプリケーションの更新されたバージョン番号と変更されたサービスで変更します。  

Eclipse Neon を使用してアプリケーションをアップグレードするには、実行構成プロファイルの複製を作成します。 次に、この構成を使用して、必要に応じてアプリケーションをアップグレードします。

1.  **[Run (実行)]** > **[Run Configurations (構成の実行)]** の順に移動します。 左側のウィンドウにある **[Gradle Project (Gradle プロジェクト)]** の左側の小さな矢印をクリックします。
2.  **[ServiceFabricDeployer]** を右クリックし、**[Duplicate (複製)]** を選択します。 この構成に新しい名前を付けます (**ServiceFabricUpgrader** など)。
3.  右側のパネルの **[Arguments (引数)]** タブで、**-Pconfig='deploy'** を **-Pconfig='upgrade'** に変更し、**[Apply (適用)]** をクリックします。

この手順を行うと、いつでもアプリケーションのアップグレードに使用できる実行構成プロファイルが作成されて保存されます。 これを使用すると、アプリケーション マニフェスト ファイルから、アプリケーションの種類の更新後の最新バージョンも取得されます。

アプリケーションのアップグレードには数分かかります。 アプリケーションのアップグレードは Service Fabric Explorer で監視できます。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

