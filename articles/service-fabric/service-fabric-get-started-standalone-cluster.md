---
title: "スタンドアロン Azure Service Fabric クラスターのセットアップ | Microsoft Docs"
description: "同じコンピューター上で動作する 3 つのノードで開発用のスタンドアロン クラスターを作成します。 このセットアップを終えれば、いつでも複数マシンのクラスターを作成することができます。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>初めての Service Fabric スタンドアロン クラスターの作成
Service Fabric スタンドアロン クラスターは、Windows Server 2012 R2 または Windows Server 2016 が実行されていれば、どの仮想マシンまたはコンピューターにでも作成できます。場所は、オンプレミスでもクラウドでもかまいません。 このクイック スタートを読むと、開発用のスタンドアロン クラスターを数分で作成することができます。  最後まで読み進めていけば、1 台のコンピューター上で動作する 3 ノード クラスターが完成します。開発したアプリをそこにデプロイすることができます。

## <a name="before-you-begin"></a>開始する前に
Service Fabric には、Service Fabric スタンドアロン クラスターを作成するためのセットアップ パッケージが用意されています。  その[セットアップ パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)してください。  開発クラスターをセットアップしているコンピューターまたは仮想マシン上のフォルダーに、セットアップ パッケージを解凍します。  セットアップ パッケージの内容については、[こちら](service-fabric-cluster-standalone-package-contents.md)に詳しい説明があります。

クラスターのデプロイと構成を行うクラスター管理者には、コンピューターに対する管理特権が必要です。 ドメイン コントローラーに Service Fabric をインストールすることはできません。

## <a name="validate-the-environment"></a>環境の検証
スタンドアロン パッケージの *TestConfiguration.ps1* スクリプトをベスト プラクティス アナライザーとして使って、特定の環境にクラスターをデプロイできるかどうかを検証します。 前提条件と環境の要件については、[デプロイの準備](service-fabric-cluster-standalone-deployment-preparation.md)のページを参照してください。 スクリプトを実行して、開発クラスターを作成できるかどうかを検証します。

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>クラスターを作成する
セットアップ パッケージと共に、いくつかのサンプル クラスター構成ファイルがインストールされます。 *ClusterConfig.Unsecure.DevCluster.json* は最も単純なクラスター構成です。1 台のコンピューターで動作する 3 ノード クラスターとなっており、セキュリティ対策は施されていません。  その他の構成ファイルには、X.509 証明書または Windows セキュリティによって安全性が確保された 1 台または複数台のコンピューターから成るクラスターが記述されています。  このチュートリアルのために既定の構成設定を変更する必要はありませんが、構成ファイルに目を通して、設定の概要を把握しておいてください。  **nodes** セクションでは、クラスター内の 3 つのノードの name、IP address、[node type、fault domain、および upgrade domain](service-fabric-cluster-manifest.md#nodes-on-the-cluster) が記述されています。  **properties** セクションでは、クラスターの[セキュリティ、信頼性レベル、診断コレクション、およびノードの種類](service-fabric-cluster-manifest.md#cluster-properties)が定義されています。

このクラスターは、安全ではありません。  だれでも匿名で接続し、管理操作を実行することができるため、運用クラスターは常に X.509 証明書または Windows セキュリティを使用して保護する必要があります。  セキュリティを構成できるのはクラスターを作成するときだけであり、クラスターの作成後はセキュリティを有効にすることはできません。  Service Fabric クラスターのセキュリティについて詳しくは、[クラスターのセキュリティ保護](service-fabric-cluster-security.md)についてのページを参照してください。  

開発用の 3 ノード クラスターを作成するには、管理特権の PowerShell セッションから *CreateServiceFabricCluster.ps1* スクリプトを実行します。

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Service Fabric ランタイム パッケージは、クラスターの作成時に自動でダウンロードされてインストールされます。

## <a name="connect-to-the-cluster"></a>クラスターへの接続
これで開発用の 3 ノード クラスターが稼働状態となりました。 Service Fabric ランタイムをインストールすると、ServiceFabric PowerShell モジュールがインストールされます。  クラスターが同じコンピューターから実行されているか、リモート コンピューターから実行されているかは、この Service Fabric ランタイムを使って確認できます。  クラスターへの接続は、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) コマンドレットで確立します。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
その他の方法でクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。 クラスターに接続した後、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) コマンドレットを使って、クラスターに含まれている一連のノードとノードごとの状態情報を表示します。 すべてのノードで **HealthState** が *OK* になっている必要があります。

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer を使用したクラスターの視覚化
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、クラスターを視覚化してアプリケーションを管理するための最適なツールです。  Service Fabric Explorer はクラスターで動作するサービスであり、ブラウザーから [http://localhost:19080/Explorer](http://localhost:19080/Explorer) でアクセスすることができます。 

クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。 ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>クラスターの削除
クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* PowerShell スクリプトを実行し、JSON 構成ファイルのパスを渡してください。 必要に応じて、削除対象のログの場所を指定することもできます。

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Service Fabric ランタイムをコンピューターから削除するには、次の PowerShell スクリプトをパッケージ フォルダーから実行します。

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>次のステップ
開発用のスタンドアロン クラスターをセットアップできたので、以下の作業に挑戦してみてください。
* [複数マシンのスタンドアロン クラスターをセットアップ](service-fabric-cluster-creation-for-windows-server.md)してセキュリティを確保します。
* [PowerShell を使ってアプリをデプロイ](service-fabric-deploy-remove-applications.md)します。

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
