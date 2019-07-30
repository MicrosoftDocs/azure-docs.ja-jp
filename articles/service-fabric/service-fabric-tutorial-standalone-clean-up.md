---
title: Service Fabric スタンドアロン クラスターをクリーンアップするチュートリアル - Azure Service Fabric | Microsoft Docs
description: このチュートリアルでは、スタンドアロンのクラスターをクリーンアップする方法について説明します
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
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385163"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>チュートリアル:スタンドアロン クラスターのクリーンアップ

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、AWS または Azure 上でホストされるスタンドアロンのクラスターを作成し、そこにアプリケーションをインストールします。

このチュートリアルは、シリーズの第 4 部です。 このチュートリアル パートでは、Service Fabric クラスターのホストとして作成した AWS または Azure リソースをクリーンアップする方法を紹介します。

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * Service Fabric クラスターのクリーンアップ
> * AWS または Azure リソースのクリーンアップ

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric クラスターのクリーンアップ

1. Service Fabric のインストールに使用した VM に RDP で接続します。
2. PowerShell を開きます。
3. 2 番目のチュートリアルで展開したフォルダーにディレクトリを変更します。
4. 次のコマンドを実行して Service Fabric クラスターを削除します。

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. 確認を求められたら、「`Y`」を入力します。成功した場合、次のような出力結果が、実際の IP アドレスで表示されます。

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

1. AWS アカウントにサインインします。
2. EC2 コンソールに移動します。
3. このチュートリアルの第 1 部で作成した 3 つのノードを選択します。
4. **[Actions]\(操作\)**  >  **[Instance State]\(インスタンスの状態\)**  >  **[Terminate]\(終了\)** をクリックします。

## <a name="clean-up-azure-resources"></a>Azure リソースをクリーンアップする

1. Azure ポータルにサインインします。
2. **[仮想マシン]** セクションに移動します。
3. このチュートリアルの第 1 部で作成した 3 つのノードのチェック ボックスをオンにします。
4. **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

シリーズの第 4 部では、前の手順で作成したリソースをクリーンアップする方法について説明しました。

> [!div class="checklist"]
> * リソースをクリーンアップする

> [!div class="nextstepaction"]
> [先頭に戻る](service-fabric-tutorial-standalone-create-infrastructure.md)
