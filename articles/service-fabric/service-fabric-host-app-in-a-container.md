---
title: "コンテナー内の .NET アプリを Azure Service Fabric にデプロイする | Microsoft Docs"
description: "Docker コンテナー内の Visual Studio で .NET アプリをパッケージ化する方法を説明します。 次に、この新しい \"コンテナー\" アプリを、Service Fabric クラスターにデプロイします。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 484db494e7975df950543d19bf841a4df7cdd139
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする

このチュートリアルでは、Windows コンテナー内の既存の ASP.NET アプリケーションを Azure にデプロイする方法を示します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio での Docker プロジェクトの作成
> * 既存のアプリケーションのコンテナー格納
> * Visual Studio と VSTS を使用した継続的インテグレーションのセットアップ

## <a name="prerequisites"></a>前提条件

1. Windows 10 でコンテナーを実行できるように、[Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) をインストールします。
2. [Windows 10 Containers のクイックスタート][link-container-quickstart]の内容について理解を深めます。
3. [Fabrikam Fiber CallCenter][link-fabrikam-github] というサンプル アプリケーションをダウンロードします。
4. [Azure PowerShell][link-azure-powershell-install] をインストールします。
5. [Visual Studio 2017 用の継続的配信ツールの拡張機能][link-visualstudio-cd-extension]をインストールします。
6. [Azure サブスクリプション][link-azure-subscription]と [Visual Studio Team Services アカウント][link-vsts-account]を作成します。 
7. [Azure でのクラスターの作成](service-fabric-tutorial-create-cluster-azure-ps.md)

## <a name="containerize-the-application"></a>アプリケーションのコンテナー格納

これで、[Azure で Service Fabric クラスターが実行され](service-fabric-tutorial-create-cluster-azure-ps.md)、コンテナーに格納するアプリケーションを作成して、デプロイする準備ができました。 コンテナー内のアプリケーションの実行を開始するには、Visual Studio 内のプロジェクトに **Docker サポート**を追加する必要があります。 **Docker サポート**をアプリケーションに追加すると、2 つの処理が行われます。 まず、_Dockerfile_ がプロジェクトに追加されます。 この新しいファイルには、コンテナー イメージのビルド方法が記載されています。 次に、新しい _docker-compose_ プロジェクトがソリューションに追加されます。 新しいプロジェクトには、いくつかの docker-compose ファイルが含まれています。 docker-compose ファイルは、コンテナーの実行方法を説明するために使用できます。

[Visual Studio Container Tools][link-visualstudio-container-tools] の使用に関する詳細

>[!NOTE]
>お使いのコンピューターで Windows コンテナー イメージを初めて実行する場合は、Docker CE でコンテナーの基本イメージをプルする必要があります。 このチュートリアルで使用されるイメージは 14 GB です。 基本イメージをプルするために、次の端末コマンドを実行します。
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Docker サポートの追加

Visual Studio で [FabrikamFiber.CallCenter.sln][link-fabrikam-github] ファイルを開きます。

**FabrikamFiber.Web** プロジェクトを右クリック > **[追加]** > **[Docker サポート]** の順に選択します。

### <a name="add-support-for-sql"></a>SQL のサポートの追加

このアプリケーションでは、データ プロバイダーとして SQL を使用するため、アプリケーションの実行には SQL Server が必要です。 docker-compose.override.yml ファイルの SQL Server コンテナー イメージを参照します。

Visual Studio で**ソリューション エクスプローラー**を開き、**docker-compose** を見つけて **docker-compose.override.yml** というファイルを開きます。

`services:` ノードに移動し、コンテナーの SQL Server エントリを定義する `db:` という名前のノードを追加します。

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>ローカル デバッグには、ホストからアクセスできる範囲であれば好きな SQL Server を使用できます。 ただし、**localdb** は `container -> host` 通信をサポートしていません。

>[!WARNING]
>コンテナー内での SQL Server の実行では永続的データはサポートされません。 コンテナーが停止すると、データは消去されます。 運用環境には、この構成を使用しないでください。

`fabrikamfiber.web:` ノードに移動して、`depends_on:` という名前の子ノードを追加します。 これにより、確実に Web アプリケーション (fabrikamfiber.web) の前に `db` サービス (SQL Server コンテナー) を開始します。

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Web 構成の更新

**FabrikamFiber.Web** プロジェクトに戻り、**web.config** ファイル内の接続文字列を更新し、コンテナー内の SQL Server を指定します。

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Web アプリケーションのリリース ビルドを作成するときに異なる SQL Server を使用する場合は、web.release.config ファイルに別の接続文字列を追加します。

### <a name="test-your-container"></a>コンテナーのテスト

**F5** キーを押して、コンテナー内のアプリケーションを実行しデバッグします。

NAT の内部ネットワークにあるコンテナーの IP アドレス (通常は 172.x.x.x) を使用して、アプリケーションの定義済み開始ページが Edge で開きます。 Visual Studio 2017 を使用してコンテナー内のアプリケーションをデバッグする方法の詳細については、[この記事][link-debug-container]をご覧ください。

![コンテナー内の Fabrikam の例][image-web-preview]

これで、コンテナーを Service Fabric アプリケーションにビルドしてパッケージ化する準備ができました。 コンピューター上にコンテナー イメージをビルドすると、コンテナー レジストリにプッシュしたり、ホストにプルして実行したりできるようになります。

## <a name="get-the-application-ready-for-the-cloud"></a>クラウド用のアプリケーションの準備

Azure の Service Fabric で実行するためにアプリケーションを準備するには、2 つの手順を実行する必要があります。

1. Service Fabric クラスター内の Web アプリケーションにアクセスできるようにするポートを公開します。
2. このアプリケーションに運用対応 SQL データベースを提供します。

### <a name="expose-the-port-for-the-app"></a>アプリのポートの公開
構成した Service Fabric クラスターでは、クラスターへの着信トラフィックを分散する Azure Load Balancer で既定でポート *80* がオープンされます。 docker-compose.yml ファイルを使用して、このポートでコンテナーを公開できます。

Visual Studio で**ソリューション エクスプローラー**を開き、**docker-compose** を見つけて **docker-compose.override.yml** というファイルを開きます。

`fabrikamfiber.web:` ノードを変更し、`ports:` という名前の子ノードを追加します。

文字列エントリ `- "80:80"` を追加します。

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>運用 SQL データベースの使用
運用環境で実行する場合、データベースでデータを永続化する必要があります。 現在コンテナー内のデータの永続化を保証する方法はありません。そのため、コンテナー内に SQL Server の運用データを格納することはできません。

Azure SQL Database を使用することをお勧めします。 Azure で管理対象の SQL Server をセットアップして実行する場合は、[Azure SQL Database のクイック スタート][link-azure-sql]に関する記事を参照してください。

>[!NOTE]
>**FabrikamFiber.Web** プロジェクトで **web.release.config** ファイル内の接続文字列を SQL Server に変更することを忘れないでください。
>
>SQL データベースにアクセスできない場合、このアプリケーションは正常に終了します。 SQL Server を使用しないで、続行し、アプリケーションをデプロイすることもできます。

## <a name="deploy-with-visual-studio-team-services"></a>Visual Studio Team Services を使用したデプロイ

Visual Studio Team Services を使用してデプロイをセットアップするには、[Visual Studio 2017 用の継続的配信ツールの拡張機能][link-visualstudio-cd-extension]をインストールする必要があります。 この拡張機能を使用して Visual Studio Team Services を構成することで、Azure へのデプロイと Service Fabric クラスターへのアプリケーションのデプロイが簡単になります。

開始するにはまず、コードがソース管理でホストされている必要があります。 このセクションの残りの記事では、**Git** を使用しています。

### <a name="set-up-a-vsts-repo"></a>VSTS リポジトリのセットアップ
Visual Studio の右下隅にある **[ソース管理に追加]** > **[Git]** の順にクリックします (好きなオプションから選択してもかまいません)。

![ソース管理ボタンをクリックする][image-source-control]

_[Team Explorer]_ ウィンドウで、**[Git リポジトリを発行]** をクリックします。

VSTS リポジトリ名を選択して **[リポジトリ]** をクリックします。

![リポジトリを VSTS に発行する][image-publish-repo]

これでコードが VSTS のソース リポジトリと同期されたため、継続的インテグレーションと継続的配信を構成できるようになります。

### <a name="setup-continuous-delivery"></a>継続的配信のセットアップ

_ソリューション エクスプ ローラー_で、**ソリューション**を右クリック > **[継続的配信を構成する]** をクリックします。

[Azure サブスクリプション] を選択します。

**[ホストの種類]** を **[Service Fabric クラスター]** に設定します。

前のセクションで作成した Service Fabric クラスターに**ターゲット ホスト**を設定します。

コンテナーを発行する **[コンテナー レジストリ]** を選択します。

>[!TIP]
>**[編集]** でコンテナー レジストリを作成します。

**[OK]** をクリックします。

![Service Fabric の継続的インテグレーションをセットアップする][image-setup-ci]
   
   設定が完了すると、コンテナーが Service Fabric にデプロイされます。 リポジトリに更新をプッシュするたびに、新しいビルドとリリースが実行されます。
   
   >[!NOTE]
   >コンテナー イメージのビルドには、約 15 分かかります。
   >Service Fabric クラスターへの最初のデプロイによって、基本の Windows Server Core コンテナー イメージがダウンロードされます。 ダウンロードが完了するまでに、さらに 5 ～ 10 分かかります。

クラスターの URL (*http://mycluster.westeurope.cloudapp.azure.com* など) を使用して Fabrikam Call Center アプリケーションを参照します。

Fabrikam Call Center ソリューションをコンテナー化してデプロイしたため、[Azure Portal][link-azure-portal] を開いて Service Fabric でアプリケーションが実行されていることを確認できます。 アプリケーションをテストするには、Web ブラウザーを開いて Service Fabric クラスターの URL にアクセスします。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio での Docker プロジェクトの作成
> * 既存のアプリケーションのコンテナー格納
> * Visual Studio と VSTS を使用した継続的インテグレーションのセットアップ

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

