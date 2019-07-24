---
title: Service Fabric スタンドアロン クラスターをクリーンアップするチュートリアル - Azure Service Fabric | Microsoft Docs
description: このチュートリアルでは、スタンドアロン クラスターをクリーンアップする方法について説明します。
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
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 28cc2bf4794620641fb6af46bd4017d74f87e955
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666998"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>チュートリアル:スタンドアロン クラスターのクリーンアップ

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、AWS をホストとするスタンドアロン クラスターを作成し、そこにアプリケーションをインストールします。

このチュートリアルは、シリーズの第 4 部です。 このチュートリアル パートでは、Service Fabric クラスターのホストとして作成した AWS リソースをクリーンアップする方法を紹介します。

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * Service Fabric クラスターのクリーンアップ
> * AWS リソースのクリーンアップ

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric クラスターのクリーンアップ

* Service Fabric のインストールに使用した EC2 インスタンスに RDP で接続します。
* PowerShell を開きます
* 2 番目のチュートリアルで展開したフォルダーにディレクトリを変更します。
* 次のコマンドを実行して Service Fabric クラスターを削除します。

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* 確認を求められたら `Y` を選択します。成功した場合、次のような出力結果が、実際の IP アドレスで表示されます。

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>AWS リソースのクリーンアップ

* AWS アカウントにログインします。
* EC2 コンソールに移動します。
* このチュートリアルの第 1 部で作成した 3 つのノードを選択します。
* **[操作]** > **[インスタンスの状態]** > **[終了]** をクリックします。

## <a name="next-steps"></a>次の手順

シリーズの第 4 部では、前の手順で作成したリソースをクリーンアップする方法について説明しました。

> [!div class="checklist"]
> * リソースをクリーンアップする

> [!div class="nextstepaction"]
> [先頭に戻る](service-fabric-tutorial-standalone-create-infrastructure.md)