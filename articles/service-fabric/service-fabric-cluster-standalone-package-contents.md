---
title: "Windows Server 用の Service Fabric スタンドアロン パッケージ | Microsoft Docs"
description: "Windows Server 用の Service Fabric スタンドアロン パッケージの説明と内容。"
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/15/2017
ms.author: chackdan;maburlik
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: a09ee1955717d7e042c1df3382c4cecd40069e3a
ms.lasthandoff: 03/29/2017


---

# <a name="package-contents-of-service-fabric-standalone-package-for-windows-server"></a>Windows Server 用の Service Fabric スタンドアロン パッケージの内容
[ダウンロードした](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric スタンドアロン パッケージには、次のファイルが含まれています。

| **ファイル名** | **簡単な説明** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |ClusterConfig.json ファイル内の設定を使用してクラスターを作成する PowerShell スクリプト。 |
| RemoveServiceFabricCluster.ps1 |ClusterConfig.json ファイル内の設定を使用してクラスターを削除する PowerShell スクリプト。 |
| AddNode.ps1 |使用中のコンピューターにデプロイされている既存のクラスターにノードを追加するための PowerShell スクリプト。 |
| RemoveNode.ps1 |使用中のコンピューターにデプロイされている既存のクラスターからノードを削除するための PowerShell スクリプト。 |
| CleanFabric.ps1 |使用中のコンピューターからスタンドアロンの Service Fabric をアンインストールするための PowerShell スクリプト。 以前の MSI インストールについては、それに対応する独自のアンインストーラーで削除する必要があります。 |
| TestConfiguration.ps1 |Cluster.json で指定されたインフラストラクチャを分析するための PowerShell スクリプト。 |
| DownloadServiceFabricRuntimePackage.ps1 |デプロイするコンピューターがインターネットに接続されていない場合に、帯域外の最新のランタイム パッケージをダウンロードするために使用される PowerShell スクリプト。 |
| DeploymentComponentsAutoextractor.exe |スタンドアロン パッケージ スクリプトによって使用されるデプロイ コンポーネントが格納されている自己解凍アーカイブ。 |
| EULA_ENU.txt |Microsoft Azure Service Fabric のスタンドアロン Windows Server パッケージの使用に関するライセンス条項。 [使用許諾契約書のコピーのダウンロード](http://go.microsoft.com/fwlink/?LinkID=733084) ができるようになりました。 |
| Readme.txt |リリース ノートや基本的なインストール手順へのリンク。 このドキュメントに記載の説明も一部含まれています。 |
| ThirdPartyNotice.rtf |パッケージに同梱されるサードパーティ製ソフトウェアに関する注意。 |

**テンプレート** 
| **ファイル名** | **簡単な説明** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティで保護されていない開発クラスターの設定 (クラスター内の各ノードの情報を含む) が含まれています。 |
| ClusterConfig.Unsecure.MultiMachine.json |クラスター構成サンプル ファイル。複数のマシン (または仮想マシン) を持つ、セキュリティで保護されていないクラスターの設定 (クラスター内の各マシンの情報を含む) が含まれています。 |
| ClusterConfig.Windows.DevCluster.json |クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスターの各ノードの情報を含む) が含まれています。 クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。 |
| ClusterConfig.Windows.MultiMachine.json |クラスター構成サンプル ファイル。複数のマシン (または仮想マシン) を持つ、Windows セキュリティを使用するセキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各マシンの情報を含む) が含まれています。 クラスターは [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx) を使用して保護されます。 |
| ClusterConfig.x509.DevCluster.json |クラスター構成サンプル ファイル。3 つのノードと 1 つのマシン (または仮想マシン) を持つ、セキュリティ保護された開発クラスターのすべての設定 (クラスターの各ノードの情報を含む) が含まれています。 クラスターは x509 証明書を使用して保護されます。 |
| ClusterConfig.x509.MultiMachine.json |クラスター構成サンプル ファイル。複数のマシン (仮想マシン) を持つ、セキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各ノードの情報を含む) が含まれています。 クラスターは x509 証明書を使用して保護されます。 |
| ClusterConfig.gMSA.Windows.MultiMachine.json |クラスター構成サンプル ファイル。複数のマシン (仮想マシン) を持つ、セキュリティ保護されたクラスターのすべての設定 (セキュリティ保護されたクラスター内の各ノードの情報を含む) が含まれています。 クラスターは、[グループ管理サービス アカウント](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx)を使用して保護されます。 |

# <a name="cluster-configuration-samples"></a>クラスター構成の例
クラスター構成テンプレートの最新バージョンは次の GitHub ページにあります。[スタンドアロン クラスター構成のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

## <a name="related"></a>関連項目
* [スタンドアロン Azure Service Fabric クラスターを作成する](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric クラスターのセキュリティに関するシナリオ](service-fabric-windows-cluster-windows-security.md)

