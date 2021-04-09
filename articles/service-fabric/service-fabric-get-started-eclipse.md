---
title: Eclipse 用の Azure Service Fabric プラグイン
description: Eclipse および Service Fabric で提供されているプラグインを使用した、Java の Azure Service Fabric の概要について説明します。
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.custom: devx-track-java
ms.openlocfilehash: b85206f9b1e92607bba7b6f141b700922c129b65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656922"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Eclipse Java アプリケーション開発用の Service Fabric プラグイン
Eclipse は、Java 開発者の間で最も広く使用されている統合開発環境 (IDE) の 1 つです。 この記事では、Eclipse 開発環境をセットアップして Azure Service Fabric を操作する方法について説明します。 Service Fabric プラグインのインストール、Service Fabric アプリケーションの作成、ローカルまたはリモートの Service Fabric クラスターへの Service Fabric アプリケーションのデプロイを Eclipse で行う方法を確認します。 

> [!NOTE]
> Eclipse プラグインは、現在、Windows でサポートされていません。 

> [!IMPORTANT]
> JDK 8 がシステムにインストールされ、Eclipse で選択されていることを確認してください。

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Eclipse で Service Fabric プラグインをインストールまたは更新する
Service Fabric プラグインを Eclipse にインストールすることができます。 このプラグインを使用すると、Java サービスのビルドとデプロイの手順を簡素化できます。

> [!IMPORTANT]
> Service Fabric プラグインには、Eclipse Neon またはそれ以降のバージョンが必要です。 Eclipse のバージョンを確認する方法については、この注の後の手順を参照してください。 以前のバージョンの Eclipse がインストールされている場合は、より新しいバージョンを [Eclipse サイト](https://www.eclipse.org)からダウンロードすることができます。 Eclipse の既存のインストールの上にインストールする (上書きする) ことはお勧めしません。 インストーラーを実行する前に削除するか、新しいバージョンを別のディレクトリにインストールすることができます。 
> 
> Ubuntu では、パッケージ インストーラー (`apt` または `apt-get`) を使用するのではなく、Eclipse サイトから直接インストールすることをお勧めします。 そうすることで、Eclipse の最新バージョンを確実に入手することができます。 

[Eclipse サイト](https://www.eclipse.org)から Eclipse Neon またはそれ以降をインストールします。  さらに、Buildship のバージョン 2.2.1 以降をインストールします (Service Fabric プラグインは旧バージョンの Buildship と互換性がありません)。
-   インストールされているコンポーネントのバージョンを確認するには、Eclipse で **[Help]\(ヘルプ\)**  >  **[About Eclipse]\(Eclipse について\)**  >  **[Installation Details]\(インストールの詳細\)** の順に移動します。
-   Buildship は、[Eclipse Buildship: Gradle 用の Eclipse プラグイン][buildship-update]に関するページの手順に従って更新できます。
-   Eclipse の更新プログラムを確認してインストールするには、 **[Help]\(ヘルプ\)**  >  **[Check for Updates]\(更新プログラムの確認\)** の順に移動します。

Service Fabric プラグインをインストールするために、Eclipse で **[Help]\(ヘルプ\)**  >  **[Install New Software]\(新しいソフトウェアのインストール\)** の順に移動します。
1. **[Work with]\(作業対象\)** ボックスに、「https:\//dl.microsoft.com/eclipse」と入力します。
2. **[追加]** をクリックします。

   ![Eclipse 用の Service Fabric プラグイン][sf-eclipse-plugin-install]
3. Service Fabric プラグインを選択して **[Next (次へ)]** をクリックします。
4. インストール手順を完了し、マイクロソフト ソフトウェア ライセンス条項に同意します。

Service Fabric プラグインが既にインストールされている場合は、最新バージョンをインストールしてください。 
1. 使用できる更新プログラムを確認するには、 **[Help]\(ヘルプ\)**  >  **[About Eclipse]\(Eclipse について\)**  >  **[Installation Details]\(インストールの詳細\)** の順に移動します。 
2. インストールされているプラグインの一覧で Service Fabric を選択して、 **[Update (更新)]** をクリックします。 使用できる更新プログラムがインストールされます。
3. Service Fabric プラグインを更新したら、Gradle プロジェクトも更新します。  **build.gradle** を右クリックし、 **[Refresh]\(最新の情報に更新\)** を選択します。

> [!NOTE]
> Service Fabric プラグインのインストールまたは更新に時間がかかる場合、Eclipse の設定が原因である可能性があります。 Eclipse は、Eclipse インスタンスに登録されている更新サイトに対するすべての変更のメタデータを収集します。 Service Fabric プラグインの更新プログラムを確認してインストールする手順を高速化するには、 **[Available Software Sites (利用できるソフトウェア サイト)]** に移動します。 Service Fabric プラグインの場所 (https:\//dl.microsoft.com/eclipse/azure/servicefabric) を指すサイトを除き、すべてのサイトのチェック ボックスをオフにします。

> [!NOTE]
>Eclipse が Mac で意図したように動かない場合、またはスーパー ユーザーとして実行する必要がある場合は、**ECLIPSE_INSTALLATION_PATH** フォルダーの **Eclipse.app/Contents/MacOS** サブフォルダーに移動します。 `./eclipse` を実行して Eclipse を起動します。

## <a name="create-a-service-fabric-application-in-eclipse"></a>Eclipse での Service Fabric アプリケーションの作成

1.  Eclipse で、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Other]\(その他\)** の順に移動します。 **Service Fabric プロジェクト** を選択して **[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 1][create-application/p1]

2.  プロジェクトの名前を入力し、 **[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 2][create-application/p2]

3.  テンプレートの一覧で **[サービス テンプレート]** を選択します。 サービス テンプレートの種類 (アクター、ステートレス、コンテナー、またはゲスト バイナリ) を選択して、 **[Next (次へ)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 3][create-application/p3]

4.  サービス名とサービスの詳細を入力して、 **[Finish (完了)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 4][create-application/p4]

5. Service Fabric プロジェクトを初めて作成する場合、 **[Open Associated Perspective (関連付けられている観点を開く)]** ダイアログ ボックスで **[Yes (はい)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 5][create-application/p5]

6.  新しいプロジェクトは次のようになります。

    ![新しい Service Fabric プロジェクトのページ 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Eclipse での Service Fabric アプリケーションの構築

1.  新しい Service Fabric アプリケーションを右クリックして、 **[Service Fabric]** を選択します。

    ![[Service Fabric] の右クリック メニュー][publish/RightClick]

2. コンテキスト メニューで、次のいずれかを選択します。
    -   クリーニングなしでアプリケーションをビルドするには、 **[Build Application (アプリケーションのビルド)]** をクリックします。
    -   アプリケーションのクリーン ビルドを実行するには、 **[Rebuild Application (アプリケーションのリビルド)]** をクリックします。
    -   ビルド アーティファクトのアプリケーションをクリーンするには、 **[Clean Application (アプリケーションのクリーン)]** をクリックします。

## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Eclipse を使用してローカル クラスターに Service Fabric アプリケーションをデプロイする

Service Fabric アプリケーションを構築したら、以下の手順に従ってローカル クラスターにデプロイします。

1. ローカル クラスターを起動していない場合は、[ローカル クラスターのセットアップ](./service-fabric-get-started-linux.md#set-up-a-local-cluster)に関する記事の説明に従ってローカル クラスターを起動し、実行されていることを確認します。
2. Service Fabric アプリケーションを右クリックして、 **[Service Fabric]** を選択します。

    ![[Service Fabric] の右クリック メニュー][publish/RightClick]

3.  コンテキスト メニューから **[アプリケーションの展開]** をクリックします。
4.  デプロイ操作の進捗状況は、コンソール ウィンドウから確認できます。
5.  アプリケーションが実行されていることを確認するには、ブラウザーのウィンドウでローカル クラスターの Service Fabric Explorer を開きます `http://localhost:19080/Explorer`。 **Applications** ノードを展開し、アプリケーションが実行されていることを確認します。 

ローカル クラスターを使用する Eclipse でアプリケーションをデバッグする方法については、[Eclipse での Java サービスのデバッグ](./service-fabric-debugging-your-application-java.md)に関する記事を参照してください。

**[Publish Application]\(アプリケーションの発行)** コマンドを使用してローカル クラスターにアプリケーションをデプロイすることもできます。

1. Service Fabric アプリケーションを右クリックして、 **[Service Fabric]** を選択します。
2. コンテキスト メニューから **[Publish Application...]\(アプリケーションの発行)** をクリックします。
3. **[Publish Application]\(アプリケーションの発行)** ウィンドウで、ターゲット プロファイルとして **PublishProfiles/Local.json** を選択し、 **[発行]** をクリックします。

    ![ローカルの [発行] ダイアログ](./media/service-fabric-get-started-eclipse/localjson.png)

    既定では、Local.json 発行プロファイルはローカル クラスターに発行するために設定されています。 発行プロファイルにある接続およびエンドポイント パラメーターの詳細については、次のセクションを参照してください。

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Eclipse を使用して Azure に Service Fabric アプリケーションを発行する

アプリケーションをクラウドに発行するには、次の手順を実行します。

1. クラウド内のセキュリティで保護されたクラスターにアプリケーションを発行するには、X.509 証明書を使用してクラスターと通信する必要があります。 テストおよび開発環境では、多くの場合、クラスター証明書が使用されます。 運用環境ではクラスター証明書とは異なるクライアント証明書である必要があります。 証明書と秘密キーの両方が必要です。 証明書 (およびキー) ファイルは、PEM 形式である必要があります。 PFX ファイルからの証明書と秘密キーを含む PEM ファイルは、次の openssl コマンドを使用して作成できます。

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   PFX ファイルがパスワードで保護されていない場合は、最後のパラメーターとして `--passin pass:` を使用します。

2. **PublishProfiles** ディレクトリにある **Cloud.json** ファイルを開きます。 クラスターに適切なクラスター エンドポイントとセキュリティ資格情報を構成する必要があります。

   - `ConnectionIPOrURL` フィールドには、クラスターの IP アドレスまたは URL が含まれています。 値には URL スキームにが含まれていないことに注意してください (`https://`)。
   - 既定では、このポートをクラスター用に明示的に変更した場合を除き、`ConnectionPort` フィールドは `19080` である必要があります。
   - `ClientKey` フィールドは、クライアントまたはクラスター証明書のための秘密キーを含む、ローカル コンピューター上の PEM 形式の .pem または .key ファイルを指す必要があります。
   - `ClientCert` フィールドは、クライアントまたはクラスター 証明書のための証明書データを含む、ローカル コンピューター上の PEM 形式の .pem または .crt ファイルを指す必要があります。 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Service Fabric アプリケーションを右クリックして、 **[Service Fabric]** を選択します。
3. コンテキスト メニューから **[Publish Application...]\(アプリケーションの発行)** をクリックします。
3. **[Publish Application]\(アプリケーションの発行)** ウィンドウで、ターゲット プロファイルとして **PublishProfiles/Cloud.json** を選択し、 **[発行]** をクリックします。

    ![クラウドの [発行] ダイアログ](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. 発行操作の進捗状況は、コンソール ウィンドウから確認できます。
5. アプリケーションが実行されていることを確認するには、ブラウザーのウィンドウで Azure クラスターの Service Fabric Explorer を開きます。 上記の例では、これは `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer` のようになります。 **Applications** ノードを展開し、アプリケーションが実行されていることを確認します。 

セキュリティで保護された Linux クラスターの場合、アプリケーションに Reliable Services サービスが含まれている場合は、Service Fabric runtime API を呼び出すためにサービスが使用できる証明書を構成する必要もあります。 詳しくは、「[Reliable Services アプリを Linux クラスター上で実行するように構成する](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)」をご覧ください。

セキュリティで保護された Linux クラスターに Java で記述された Service Fabric Reliable Services アプリケーションをデプロイする方法については、「[クイック スタート: Service Fabric に Java Reliable Services アプリケーションをデプロイする](./service-fabric-quickstart-java-reliable-services.md)」を参照してください。

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Eclipse 実行構成を使用して Service Fabric アプリケーションをデプロイする

Eclipse 実行構成を使用して Service Fabric アプリケーションをデプロイするという方法もあります。

1. Eclipse で、 **[Run (実行)]**  >  **[Run Configurations (構成の実行)]** の順に移動します。
2. **[Gradle Project (Gradle プロジェクト)]** で **[ServiceFabricDeployer]** 実行構成を選択します。
3. 右側のウィンドウの **[引数]** タブで、 **[ip]** 、 **[port]** 、 **[clientCert]** 、および **[clientKey]** パラメーターがデプロイに適切に設定されていることを確認します。 既定では、パラメーターは、次のスクリーン ショットのように、ローカル クラスターにデプロイするよう設定されています。 アプリを Azure に発行するには、Azure クラスターのエンドポイントの詳細とセキュリティ資格情報を含めるようパラメーターを変更することができます。 詳細については、前のセクションの「[Eclipse を使用して Azure Service Fabric アプリケーションを発行する](#publish-your-service-fabric-application-to-azure-with-eclipse)」を参照してください。

    ![ローカルの [実行構成] ダイアログ](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. **[作業ディレクトリ]** が、デプロイするアプリケーションを指していることを確認します。 アプリケーションを変更するには、 **[Workspace (ワークスペース)]** ボタンをクリックし、目的のアプリケーションを選択します。
6. **[Apply (適用)]** をクリックし、 **[Run (実行)]** をクリックします。

すぐにアプリケーションのビルドとデプロイが行われます。 Service Fabric Explorer でデプロイの状態を監視できます。  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric アプリケーションへの Service Fabric サービスの追加

既存の Service Fabric アプリケーションに Service Fabric サービスを追加するには、次の手順を実行します。

1.  サービスの追加先となるプロジェクトを右クリックし、 **[Service Fabric]** をクリックします。

    ![Service Fabric のサービス追加のページ 1][add-service/p1]

2.  **[Add Service Fabric Service (Service Fabric サービスの追加)]** をクリックし、一連の手順を完了してサービスをプロジェクトに追加します。
3.  プロジェクトに追加するサービス テンプレートを選択して、 **[Next (次へ)]** をクリックします。

    ![Service Fabric のサービス追加のページ 2][add-service/p2]

4.  (必要に応じて他の詳細と共に) サービス名を入力し、 **[Add Service (サービスの追加)]** ボタンをクリックします。  

    ![Service Fabric のサービス追加のページ 3][add-service/p3]

5.  サービスの追加後、プロジェクト全体の構造は次のようになります。

    ![Service Fabric のサービス追加のページ 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Service Fabric Java アプリケーションのマニフェスト バージョンの編集

マニフェスト バージョンを編集するには、プロジェクトを右クリックし、 **[Service Fabric]** に移動して、メニューのドロップダウンから **[マニフェスト バージョンの編集]** を選択します。 ウィザードで、アプリケーション マニフェストとサービス マニフェストのバージョン、さらに **Code**、**Config**、**Data** パッケージのバージョンを更新することができます。

**[アプリケーションとサービスのバージョンを自動的に更新する]** チェック ボックスをオンにし、バージョンを更新すると、マニフェストのバージョンが自動的に更新されます。 たとえば、最初にこのチェック ボックスをオンにしたうえで、**Code** のバージョンを 0.0.0 から 0.0.1 に更新し、 **[完了]** をクリックすると、サービス マニフェストのバージョンとアプリケーション マニフェストのバージョンが自動的に 0.0.1 に更新されます。

## <a name="upgrade-your-service-fabric-java-application"></a>Service Fabric Java アプリケーションのアップグレード

アップグレード シナリオの一例を挙げます。たとえば、Eclipse で Service Fabric プラグインを使用して **App1** プロジェクトを作成したとします。 また、そのプラグインを使用してプロジェクトをデプロイし、**fabric:/App1Application** というアプリケーションを作成してあります。 アプリケーションの種類は **App1AppicationType**、アプリケーションのバージョンは 1.0 です。 この場合に、可用性を維持しつつアプリケーションをアップグレードする必要が生じたとしましょう。

最初にアプリケーションに変更を加え、変更されたサービスをリビルドします。 変更されたサービスのマニフェスト ファイル (ServiceManifest.xml) を、サービスの更新されたバージョン (と関連する Code、Config、Data) で更新します。 さらに、アプリケーションのマニフェスト (ApplicationManifest.xml) を、アプリケーションの更新されたバージョン番号と変更されたサービスで変更します。  

Eclipse を使用してアプリケーションをアップグレードするには、実行構成プロファイルの複製を作成します。 次に、この構成を使用して、必要に応じてアプリケーションをアップグレードします。

1.  **[Run (実行)]**  >  **[Run Configurations (構成の実行)]** の順に移動します。 左側のウィンドウにある **[Gradle Project (Gradle プロジェクト)]** の左側の小さな矢印をクリックします。
2.  **[ServiceFabricDeployer]** を右クリックし、 **[Duplicate (複製)]** を選択します。 この構成に新しい名前を付けます (**ServiceFabricUpgrader** など)。
3.  右側のパネルの **[Arguments (引数)]** タブで、 **-Pconfig='deploy'** を **-Pconfig='upgrade'** に変更し、 **[Apply (適用)]** をクリックします。

この手順を行うと、いつでもアプリケーションのアップグレードに使用できる実行構成プロファイルが作成されて保存されます。 これを使用すると、アプリケーション マニフェスト ファイルから、アプリケーションの種類の更新後の最新バージョンも取得されます。

アプリケーションのアップグレードには数分かかります。 アプリケーションのアップグレードは Service Fabric Explorer で監視できます。

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>以前の Service Fabric Java アプリケーションを移行して Maven で使用する
最近、Service Fabric Java ライブラリが Service Fabric Java SDK から Maven リポジトリに移行されました。 Eclipse を使って生成する新しいアプリケーションでは、(Maven で使用可能な) 最新のプロジェクトが生成されますが、(これまで Service Fabric Java SDK を使っていた) 既存の Service Fabric のステートレスまたはアクター Java アプリケーションは、Maven からの Service Fabric Java 依存関係を使うように更新することができます。 以前のアプリケーションを Maven で使用するには、[こちら](service-fabric-migrate-old-javaapp-to-use-maven.md)に記載した手順に従ってください。

## <a name="next-steps"></a>次のステップ

- Java Reliable サービス アプリケーションを構築し、それをローカルおよび Azure にデプロイする手順の概要については、「[クイック スタート: Service Fabric に Java Reliable Services アプリケーションをデプロイする](./service-fabric-quickstart-java-reliable-services.md)」を参照してください。
- ローカル クラスター上の Java アプリケーションをデバッグする方法については、[Eclipse での Java サービスのデバッグ](./service-fabric-debugging-your-application-java.md)に関する記事を参照してください。
- Service Fabric アプリケーションを監視し診断する方法については、「[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)」を参照してください。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

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
