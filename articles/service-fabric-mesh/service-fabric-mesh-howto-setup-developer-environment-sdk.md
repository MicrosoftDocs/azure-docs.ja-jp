---
title: Service Fabric Mesh 用の Windows 開発環境を設定する
description: Windows 開発環境を設定して、Service Fabric Mesh アプリケーションを作成し、Azure Service Fabric Mesh にデプロイします。
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625737"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Service Fabric Mesh アプリをビルドするための Windows 開発環境の設定

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Windows 開発コンピューターで Azure Service Fabric Mesh アプリケーションをビルドして実行するには、次のものが必要です。

* Docker
* Visual Studio 2017 以降
* Service Fabric Mesh ランタイム
* Service Fabric Mesh SDK とツール。

下記いずれかのバージョンの Windows:

* Windows 10 (Enterprise、Professional、Education) バージョン 1709 (Fall Creators Update) または 1803 (Windows 10 April 2018 Update)
* Windows Server バージョン 1709
* Windows Server バージョン 1803

以下の説明は、実行している Windows のバージョンに基づいて、すべてのものをインストールするのに役立ちます。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Service Fabric Mesh アプリケーションをデプロイするには、Visual Studio 2017 以降が必要です。 [バージョン 15.6.0 以上をインストール][download-visual-studio]し、次のワークロードを有効にします。

* ASP.NET および Web の開発
* Azure 開発

## <a name="install-docker"></a>Docker をインストールする

Docker を既にインストールしてある場合は、最新のバージョンであることを確認します。 Docker では新しいバージョンが公開されるとメッセージが表示されることがありますが、手動でチェックして最新バージョンがあることを確認します。

#### <a name="install-docker-on-windows-10"></a>Windows 10 に Docker をインストールする

Service Fabric Mesh が使用するコンテナー化 Service Fabric アプリをサポートするために、最新バージョンの [Docker Community Edition for Windows][download-docker] をダウンロードしてインストールします。

インストール中に求められた場合は、 **[Use Windows containers instead of Linux containers]\(Linux コンテナーの代わりに Windows コンテナーを使用する\)** を選択します。

マシン上で Hyper-V が有効になっていない場合、Docker のインストーラーによってそれを有効にするように提案されます。 求められたら **[OK]** をクリックします。

#### <a name="install-docker-on-windows-server-2016"></a>Windows Server 2016 に Docker をインストールする

Hyper-V ロールを有効にしていない場合は、管理者として PowerShell を開き、次のコマンドを実行して Hyper-V を有効にし、コンピューターを再起動します。 詳細については、[Docker Enterprise Edition for Windows Server][download-docker-server] に関するドキュメントを参照してください。

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

コンピューターを再起動します。

管理者として PowerShell を開き、次のコマンドを実行して Docker をインストールします。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK とツール

Service Fabric Mesh ランタイム、SDK、ツールを次の順序でインストールします。

1. Web Platform Installer を使用して [Service Fabric Mesh SDK][download-sdkmesh] をインストールします。 Microsoft Azure Service Fabric の SDK とランタイムもインストールされます。
2. Visual Studio Marketplace から [Visual Studio Service Fabric Mesh Tools (プレビュー) の拡張機能][download-tools]をインストールします。

## <a name="build-a-cluster"></a>クラスターの構築

> [!IMPORTANT]
> Docker は、クラスターを構築する前に実行しておく **必要があります**。
> Docker が実行中であるかどうかをテストするには、ターミナル ウィンドウを開いて `docker ps` を実行し、エラーが発生するかどうか確認します。 応答でエラーが表示されない場合は、Docker は実行中になっており、クラスターを構築する準備ができています。

> [!Note]
> Windows Fall Creators Update (バージョン 1709) のマシンで開発を行っている場合は、Windows バージョン 1709 の Docker イメージのみを使用できます。
> Windows 10 April 2018 Update (バージョン 1803) のコンピューターで開発を行っている場合は、Windows バージョン 1709 または 1803 の Docker イメージを使用できます。

ローカル クラスターがない場合、Visual Studio によって、ローカル クラスターが自動的に作成されるため、Visual Studio を使用している場合は、このセクションをスキップできます。

一度に 1 つの Service Fabric アプリを作成および実行するときのデバッグのパフォーマンスを最大限に高めるには、単一ノードのローカル開発クラスターを作成します。 一度に複数のアプリケーションを実行する場合は、5 ノードのローカル開発クラスターを作成します。 Service Fabric Mesh プロジェクトをデプロイまたはデバッグするときは常に、クラスターを実行しておく必要があります。

ランタイム、SDK、Visual Studio Tools、Docker をインストールし、Docker を実行したら、開発クラスターを作成します。

1. PowerShell ウィンドウを閉じます。
2. 管理者として、管理者特権で新しく PowerShell ウィンドウを開きます。 この手順は、最近インストールされた Service Fabric モジュールを読み込むために必要です。
3. 次の PowerShell コマンドを実行して、開発クラスターを作成します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. ローカルのクラスター マネージャー ツールを起動するには、次の PowerShell コマンドを実行します。

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. サービス クラスター マネージャー ツールが実行されたら (これはシステム トレイに表示されます)、それを右クリックし、 **[Start Local Cluster]\(ローカル クラスターを起動する\)** をクリックします。

![図 1 - ローカル クラスターを起動する](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

これで、Service Fabric Mesh アプリケーションを作成する準備ができました。

## <a name="next-steps"></a>次のステップ

[Azure Service Fabric アプリの作成](service-fabric-mesh-tutorial-create-dotnetcore.md)に関するチュートリアルを参照してください。

[よく寄せられる質問と既知の問題](service-fabric-mesh-faq.md)に対する回答を確認します。

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
