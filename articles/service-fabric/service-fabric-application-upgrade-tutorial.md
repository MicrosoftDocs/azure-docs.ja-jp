---
title: Service Fabric アプリのアップグレード チュートリアル
description: この記事では、Visual Studio による Service Fabric アプリケーションのデプロイ、コードの変更、アップグレードのロールアウトを段階的に説明します。
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996238"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Visual Studio による Service Fabric アプリケーションのアップグレード チュートリアル
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric では、変更されたサービスのみをアップグレードし、アップグレード プロセス全体を通じてそのアプリケーションの正常性を監視することで、クラウド アプリケーションのアップグレードのプロセスを簡略化します。 また、問題が発生した場合は、アプリケーションが自動的に以前のバージョンにロールバックされます。 Service Fabric アプリケーションのアップグレードは、"*ゼロ ダウンタイム*"です。アプリケーションをダウンタイムなしでアップグレードできます。 このチュートリアルでは、Visual Studio からローリング アップグレードを行う方法について説明します。

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>手順 1: ビジュアル オブジェクト サンプルのビルドと発行
まず、GitHub から [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) アプリケーションをダウンロードします。 次に、**VisualObjects** アプリケーション プロジェクトを右クリックし、[Service Fabric] メニュー項目の **[発行]** コマンドを選択して、アプリケーションをビルドし、発行します。

![Context menu for a Service Fabric application][image1]

**[発行]** を選択してポップアップが表示されたら、**[ターゲット プロファイル]** を **PublishProfiles\Local.xml** に設定します。 **[発行]** をクリックする前に、ウィンドウを次のように設定します。

![Publishing a Service Fabric application][image2]

これで、ダイアログ ボックスの **[発行]** をクリックできるようになりました。 [クラスターおよびアプリケーションを表示する Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)を使用できます。 Visual Objects アプリケーションには、ブラウザーのアドレス バーに「`http://localhost:8081/visualobjects/`」と入力すると移動できる Web サービスがあります。  画面上を動く 10 個のフローティング ビジュアル オブジェクトが表示されます。

**注:**`Cloud.xml` プロファイル (Azure Service Fabric) にデプロイする場合、アプリケーションは **http://{ServiceFabricName}.{Region}.cloudapp.azure.com:8081/visualobjects/** から入手できます。 ロード バランサーで `8081/TCP` が構成されていることを確認してください (ロード バランサーは Service Fabric インスタンスと同じリソース グループで検索します)。

## <a name="step-2-update-the-visual-objects-sample"></a>手順 2: ビジュアル オブジェクト サンプルの更新
手順 1. でデプロイされたバージョンでは、ビジュアル オブジェクトが回転しないことに気付くかもしれません。 アプリケーションをアップグレードして、ビジュアル オブジェクトも回転させてみましょう。

VisualObjects ソリューション内の VisualObjects.ActorService プロジェクトを選択し、**VisualObjectActor.cs** ファイルを開きます。 そのファイル内で `MoveObject` メソッドに移動して、`visualObject.Move(false)` をコメント アウトし、`visualObject.Move(true)` をコメント解除します。 このコードによって、サービスのアップグレード後、オブジェクトが回転します。  **これで、ソリューションを (リビルドではなく) ビルドできるようになりました**。これによって、変更したプロジェクトがビルドされます。 *[すべてリビルド]* を選択した場合は、すべてのプロジェクトのバージョンを更新する必要があります。

アプリケーションのバージョン管理も行う必要があります。 バージョンを変更するには、**VisualObjects** プロジェクトを右クリックしてから、Visual Studio の **[マニフェスト バージョンの編集]** オプションを使用します。 このオプションを選択すると、次のようなバージョン編集用のダイアログ ボックスが表示されます。

![Versioning dialog box][image3]

アプリケーションと、編集したプロジェクトおよびそのコード パッケージのバージョンを 2.0.0 に更新します。 変更後、マニフェストは次のようになります (太字部分は変更個所を示します)。

![Updating versions][image4]

**[アプリケーションとサービスのバージョンを自動的に更新する]** を選択すると、Visual Studio ツールでバージョンの自動ロールアップを実行できます。 [SemVer](http://www.semver.org)を使用する場合、このオプションが選択されていると、コードまたは構成パッケージ (あるいはその両方) のバージョンのみを更新する必要があります。

変更を保存し、 **[アプリケーションのアップグレード]** ボックスをオンにします。

## <a name="step-3--upgrade-your-application"></a>手順 3: アプリケーションのアップグレード
[アプリケーション アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)と[アップグレード プロセス](service-fabric-application-upgrade.md)に詳しくなって、アップグレードのさまざまなパラメーター、タイムアウト、および適用できる正常性条件に対する理解を深めます。 このチュートリアルでは、サービスの正常性評価の条件は、既定値 (監視なしのモード) に設定されています。 この設定を構成するには、 **[アップグレードの設定の構成]** を選択し、必要に応じてパラメーターを変更します。

アプリケーションのアップグレードを開始する準備がすべて完了しました。開始するには **[発行]** を選択します。 このオプションによりアプリケーションがバージョン 2.0.0 にアップグレードされ、オブジェクトが回転するようになります。 Service Fabric では更新ドメインが 1 つずつアップグレードされます (オブジェクトがいくつか最初に更新された後、他のオブジェクトが続きます)。サービスは、アップグレード中も利用できます。 サービスへのアクセスをチェックするには、クライアント (ブラウザー) を使用します。  

アプリケーションのアップグレードが進むと、Service Fabric Explorer で、アプリケーションの下にある **[Upgrades in Progress (進行中のアップグレード)]** タブを使用して監視できます。

数分後に、すべての更新ドメインがアップグレードされ (完了し)、Visual Studio の出力ウィンドウにもアップグレードの完了が表示されます。 さらに、ブラウザー ウィンドウの " *すべての* " ビジュアル オブジェクトが回転しているはずです。

練習のために、バージョンをバージョン 2.0.0 からバージョン 3.0.0 へ、または、バージョン 2.0.0 からバージョン 1.0.0 に変更および移行してみることもできます。 タイムアウトと正常性ポリシーをいろいろ試して、これらに精通してください。 ローカル クラスターではなく Azure クラスターにデプロイする場合、使用するパラメーターは異なる場合があります。 タイムアウトは控えめに設定することをお勧めします。

## <a name="next-steps"></a>次のステップ
[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の使用方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

[高度なトピック](service-fabric-application-upgrade-advanced.md)を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「 [アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
