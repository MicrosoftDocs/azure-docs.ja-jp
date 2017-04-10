---
title: "Visual Studio を使用したリモート クラスターへのアプリの発行 |Microsoft Docs"
description: "Visual Studio を使用してリモートの Service Fabric クラスターにアプリケーションを発行する方法について説明します。"
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e7bd4398503566684187831bde1e82f334a59f32


---
# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Visual Studio を使用してリモート クラスターにアプリケーションを発行する
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Visual Studio の Azure Service Fabric 拡張機能を使用すると、Service Fabric クラスターへのアプリケーションの発行を反復可能な方法で簡単に実行できるうえに、スクリプト化することもできます。

## <a name="the-artifacts-required-for-publishing"></a>発行に必要なアーティファクトの概要
### <a name="deploy-fabricapplicationps1"></a>Deploy-FabricApplication.ps1
これは、Service Fabric アプリケーションを発行するためのパラメーターとして発行プロファイルのパスを使用する PowerShell スクリプトです。 このスクリプトはアプリケーションの一部であるため、アプリケーションに必要であれば修正できます。

### <a name="publish-profiles"></a>発行プロファイル
" **PublishProfiles** " という名前の Service Fabric アプリケーション プロジェクトのフォルダーには、次のようなアプリケーションを発行するための重要な情報を格納する XML ファイルが含まれています。

* Service Fabric クラスターの接続パラメーター
* アプリケーション パラメーター ファイルへのパス
* アップグレード設定

既定では、アプリケーションには、Local.xml と Cloud.xml という 2 つの発行プロファイルが含まれています。 既定のファイルの 1 つをコピーして貼り付けることで、プロファイルを追加できます。

### <a name="application-parameter-files"></a>アプリケーション パラメーター ファイル
**ApplicationParameters** という Service Fabric アプリケーション プロジェクト内のフォルダーに、ユーザーが指定したアプリケーション マニフェストのパラメーター値用の XML ファイルが含まれています。 デプロイ設定に複数の値を使用できるように、アプリケーション マニフェスト ファイルをパラメーター化できます。 アプリケーションのパラメーター化に関する詳細については、「 [Service Fabric で複数の環境を管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

> [!NOTE]
> アクター サービスの場合、エディターや発行ダイアログ ボックスでファイルを編集する前に、プロジェクトをビルドする必要があります。 これは、マニフェスト ファイルの一部がビルド時に生成されるためです。
> 
> 

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>[Service Fabric アプリケーションの発行] ダイアログ ボックスを使用してアプリケーションを発行する
次の手順は、Visual Studio Service Fabric Tools によって提供される **[Service Fabric アプリケーションの発行]** ダイアログ ボックスを使用してアプリケーションを発行する方法を示しています。

1. Service Fabric アプリケーション プロジェクトのショートカット メニューで **[発行...]** を選択すると、 **[Service Fabric アプリケーションの発行]** ダイアログ ボックスが表示されます。
   
    ![**[Service Fabric アプリケーションの発行]** ダイアログ ボックス][0]
   
    **[ターゲット プロファイル]** ボックスの一覧で選択したファイルには、**マニフェスト バージョン**を除くすべての設定が保存されます。 既存のプロファイルを再利用するか、**[ターゲット プロファイル]** ボックスの一覧で **[プロファイルの管理]** を選択して新しいプロファイルを作成できます。 発行プロファイルを選択すると、その内容がダイアログ ボックスの対応するフィールドに表示されます。 **[プロファイルの保存]** リンクを選択すると、いつでも変更を保存できます。    
2. **[接続エンドポイント]** セクションで、ローカルまたはリモートの Service Fabric クラスターの発行エンドポイントを指定します。 接続エンドポイントを追加または変更するには、 **[接続のエンドポイント]** ボックスをクリックします。 ご利用の Azure サブスクリプションに基づき、発行先として使用できる Service Fabric クラスター接続エンドポイントが表示されます。 Visual Studio にまだログインしていない場合、ログインするように求められます。
   
    クラスターの選択ダイアログ ボックスで、利用可能なサブスクリプションとクラスターのセットから選択します。
   
    ![**[Service Fabric クラスターの選択]** ダイアログ ボックス][1]
   
   > [!NOTE]
   > 任意のエンドポイント (パーティ クラスターなど) に発行する場合、下の「 **任意のクラスター エンドポイントに発行する** 」を参照してください。
   > 
   > 
   
    エンドポイントを選択すると、選択した Service Fabric クラスターへの接続が Visual Studio によって検証されます。 クラスターがセキュリティで保護されていない場合、Visual Studio はすぐにエンドポイントに接続できます。 しかし、クラスターがセキュリティで保護されている場合は、続行する前にローカル コンピューターに証明書をインストールする必要があります。 詳細については、 [セキュリティで保護された接続を構成する方法に関するページ](service-fabric-visualstudio-configure-secure-connections.md) を参照してください。 終了したら、 **[OK]** をクリックします。 選択したクラスターが、 **[Service Fabric アプリケーションの発行]** ダイアログ ボックスに表示されます。
3. **[アプリケーション パラメーター ファイル]** ボックスで、アプリケーション パラメーター ファイルに移動します。 アプリケーション パラメーター ファイルには、アプリケーション マニフェスト ファイル内のパラメーターにユーザーが指定した値が保持されています。 パラメーターを追加または変更するには、 **[編集]** をクリックします。 **[パラメーター]** グリッドでパラメーターの値を入力または変更します。 終了したら、 **[保存]** を選択します。
   
    ![**[パラメーターの編集]** ダイアログ ボックス][2]
4. **[アプリケーションをアップグレードする]** チェック ボックスを使用して、この発行操作がアップグレードであるかどうかを指定します。 アップグレードの発行操作は、通常の発行操作とは異なります。 「 [Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md) 」を参照してください。 アップグレードの設定を構成するには、 **[アップグレード設定の構成]** リンクを選択します。 アップグレード パラメーター エディターが表示されます。 アップグレード パラメーターの詳細については、「 [Service Fabric アプリケーションのアップグレードの構成](service-fabric-visualstudio-configure-upgrade.md) 」を参照してください。
5. **[マニフェスト バージョン...]** ボタンをクリックすると、**[バージョンの編集]** ダイアログ ボックスが表示されます。 更新を実行するには、アプリケーションとサービスのバージョンをアップグレードする必要があります。 アプリケーションとサービス マニフェストのバージョンがアップグレード プロセスに及ぼす影響の詳細については、 [Service Fabric アプリケーションのアップグレード チュートリアル](service-fabric-application-upgrade-tutorial.md) に関するページを参照してください。
   
    ![**[バージョンの編集]** ダイアログ ボックス][3]
   
    アプリケーションとサービスのバージョンに 1.0.0 のようなセマンティック バージョンか 1.0.0.0 の形式の数値が使用されている場合は、 **[アプリケーションとサービスのバージョンを自動的に更新する]** オプションをオンにします。 このオプションをオンにすると、コード、構成、またはデータのパッケージ バージョンが更新されたときに、サービスとアプリケーションのバージョン番号が自動的に更新されます。 バージョンを手動で編集する場合は、チェック ボックスをオフにして、この機能を無効にします。
   
   > [!NOTE]
   > アクター プロジェクトにすべてのパッケージ エントリを表示するには、最初にプロジェクトをビルドして、サービス マニフェスト ファイルにエントリを生成します。
   > 
   > 
6. 必要な設定をすべて指定し終えたら、 **[発行]** をクリックし、選択した Service Fabric クラスターにアプリケーションを発行します。 指定した設定が、発行プロセスに適用されます。

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>(パーティ クラスターを含む) 任意のクラスター エンドポイントに発行する
Visual Studio の発行機能は、ご利用の Azure サブスクリプションの 1 つに関連付けられているリモート クラスターに発行するために最適化されています。 ただし、発行プロファイル XML を直接編集し、任意のエンドポイント (Service Fabric パーティ クラスターなど) に発行できます。 前述のように、**Local.xml** と **Cloud.xml** という 2 つの発行プロファイルが既定で提供されますが、別の環境向けに追加プロファイルを作成することもできます。 たとえば、パーティ クラスターに発行するために「 **Party.xml**」という名前のプロファイルを作成します。

保護されていないクラスターに接続する場合、必要なものは、 `partycluster1.eastus.cloudapp.azure.com:19000`など、クラスター接続エンドポイントだけです。 その場合、発行プロファイルの接続エンドポイントは次のようになります。

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  保護されているクラスターに接続する場合、ローカル ストアからクライアント証明書の詳細を提供し、認証に使用する必要があります。 詳細については、 [Service Fabric クラスターにセキュリティで保護された接続を構成する](service-fabric-visualstudio-configure-secure-connections.md)方法に関するページを参照してください。

  発行プロファイルが設定されたら、下の図のように発行ダイアログ ボックスで参照できます。

  ![発行ダイアログ ボックスの新しい発行プロファイル][4]

  この場合、新しい発行プロファイルは既定のアプリケーション パラメーター ファイルの 1 つを指すことに注意してください。 同じアプリケーション構成を複数の環境に発行する場合、これは適切です。 一方、発行する環境ごとに異なる構成を用意する場合、それぞれ対応するアプリケーション パラメーター ファイルを作成すると合理的です。

## <a name="next-steps"></a>次のステップ
継続的な統合環境で発行プロセスを自動化する方法については、 [Service Fabric の継続的な統合のセットアップ](service-fabric-set-up-continuous-integration.md)に関するページを参照してください。

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png



<!--HONumber=Dec16_HO2-->


