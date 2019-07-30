---
title: Service Fabric スタンドアロン クライアントをインストールするチュートリアル - Azure Service Fabric | Microsoft Docs
description: このチュートリアルでは、前のチュートリアル記事で作成したクラスターに Service Fabric スタンドアロン クライアントをインストールする方法について説明します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 82358f4d5548464f1304d77a846d71863ed9efa1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385010"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>チュートリアル:Service Fabric クラスターのインストールと作成

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、AWS または Azure 上でホストされるスタンドアロンのクラスターを作成し、そこにアプリケーションをインストールします。

このチュートリアルは、シリーズの第 2 部です。 このチュートリアルでは、Service Fabric スタンドアロン クラスターの作成手順について説明します。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Service Fabric スタンドアロン パッケージをダウンロードしてインストールする
> * Service Fabric クラスターを作成する
> * Service Fabric クラスターに接続する

## <a name="download-the-service-fabric-for-windows-server-package"></a>Windows Server 用 Service Fabric パッケージをダウンロードする

Service Fabric には、Service Fabric スタンドアロン クラスターを作成するためのセットアップ パッケージが用意されています。  ローカル コンピューターで[セットアップ パッケージ](https://go.microsoft.com/fwlink/?LinkId=730690)をダウンロードします。  正常にダウンロードされたら、RDP 接続を介して VM にコピーし、デスクトップに貼り付けます。

zip ファイルを選択し、コンテキスト メニューを開いて、 **[すべて展開]**  >  **[展開]** を選択します。  ファイルを展開すると、zip ファイルと同じ名前のフォルダーがデスクトップに生成されます。

さらに詳しい情報が必要な場合は、[セットアップ パッケージの内容](service-fabric-cluster-standalone-package-contents.md)をご覧ください。

## <a name="set-up-your-configuration-file"></a>構成ファイルを設定する

ここでは、3 ノードの Windows クラスターを作成します。そこで、`ClusterConfig.Unsecure.MultiMachine.json` ファイルに変更を加える必要があります。

次に、ファイルの 8 行目、15 行目、22 行目に出現する 3 つの ipAddress 行を、各インスタンスの IP アドレスに更新します。

ノードを更新した後の内容は、次のとおりです。

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

次に、いくつかのプロパティを更新する必要があります。  34 行目で、診断ストアの接続文字列に変更を加える必要があります。変更後は、`"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"` のようになります。

最後に、この構成の `nodeTypes` セクションに、Windows が使用することになるエフェメラル ポートをマッピングする新しいセクションを追加します。  構成ファイルは、次のようになっている必要があります。

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>環境の検証

スタンドアロン パッケージの *TestConfiguration.ps1* スクリプトをベスト プラクティス アナライザーとして使って、特定の環境にクラスターをデプロイできるかどうかを検証します。 前提条件と環境の要件については、[デプロイの準備](service-fabric-cluster-standalone-deployment-preparation.md)のページを参照してください。 スクリプトを実行して、開発クラスターを作成できるかどうかを検証します。

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

次のような出力結果が表示されます。 一番下の "Passed" フィールドで `True` が返された場合、サニティ チェックは成功であり、クラスターは入力した構成を基にデプロイ可能と考えられます。

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>クラスターを作成する

クラスターの構成に問題がないことを確認したら、*CreateServiceFabricCluster.ps1* スクリプトを実行して、構成ファイル内の仮想マシンに Service Fabric クラスターをデプロイします。

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

すべて正常に実行されれば、次のような出力結果が得られます。

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> デプロイのトレースは、CreateServiceFabricCluster.ps1 PowerShell スクリプトを実行した VM/マシンに書き込まれます。 このトレースは、スクリプトを実行したディレクトリの DeploymentTraces サブフォルダーにあります。 Service Fabric がマシンに正常にデプロイされたかどうかを確かめるには、FabricDataRoot ディレクトリにインストールされたファイルを確認します。このディレクトリはクラスター構成ファイルの FabricSettings セクションで指定した場所にあります (既定では c:\ProgramData\SF)。 また、タスク マネージャーで FabricHost.exe と Fabric.exe の各プロセスが実行されていることも確認します。
>
>

### <a name="bring-up-service-fabric-explorer"></a>Service Fabric Explorer を起動する

これで、Service Fabric Explorer を使用してクラスターに接続できるようになりました。http:\//localhost:19080/Explorer/index.html を使っていずれかのマシンから直接接続するか、http:\//<*IPAddressofaMachine*>:19080/Explorer/index.html を使ってリモートで接続することができます。

## <a name="add-and-remove-nodes"></a>ノードの追加と削除

ビジネス ニーズの変化に応じて、スタンドアロン Service Fabric クラスターでノードを追加または削除できます。 詳細の手順については、[「Service Fabric スタンドアロン クラスターでノードを追加または削除する」](service-fabric-cluster-windows-server-add-remove-nodes.md) をお読みください。

## <a name="next-steps"></a>次の手順

シリーズの第 2 部では、次の手順をはじめ、ストレージ アカウントに大量のランダム データを並行でアップロードする方法について学びました。

> [!div class="checklist"]
> * 接続文字列の構成
> * アプリケーションのビルド
> * アプリケーションの実行
> * 接続数の検証

シリーズの第 3 部に進んで、作成したクラスターにアプリケーションをインストールしましょう。

> [!div class="nextstepaction"]
> [Service Fabric クラスターにアプリケーションをインストールする](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
