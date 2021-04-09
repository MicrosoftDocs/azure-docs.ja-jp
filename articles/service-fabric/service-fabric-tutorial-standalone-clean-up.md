---
title: スタンドアロンのクラスターのクリーンアップ
description: このチュートリアルでは、スタンドアロンの Service Fabric クラスターの AWS または Azure のリソースを削除する方法について説明します。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842888"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>チュートリアル: スタンドアロン クラスターのクリーンアップ

Service Fabric スタンドアロン クラスターには、Service Fabric をホストする独自の環境を選択するためのオプションが用意されています。 このチュートリアル シリーズでは、AWS または Azure 上でホストされるスタンドアロンのクラスターを作成し、そこにアプリケーションをデプロイします。

このチュートリアルは、シリーズの第 4 部です。 このチュートリアル パートでは、Service Fabric クラスターのホストとして作成した AWS または Azure リソースを削除する方法を紹介します。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * Service Fabric クラスターを削除する
> * AWS または Azure リソースを削除する

## <a name="remove-a-service-fabric-cluster"></a>Service Fabric クラスターを削除する

1. Service Fabric のインストールに使用した VM に RDP で接続します。
2. PowerShell を開きます。
3. 2 番目のチュートリアルで展開したフォルダーにディレクトリを変更します。
4. 次のコマンドを実行して Service Fabric クラスターを削除します。

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. 確認を求められたら「`Y`」と入力します。 成功した場合、次のような出力結果が表示されます (独自の IP アドレスで)。

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

## <a name="delete-aws-resources"></a>AWS リソースの削除

1. AWS アカウントにサインインします。
2. EC2 コンソールに移動します。
3. このチュートリアルの第 1 部で作成した 3 つのノードを選択します。
4. **[Actions]\(操作\)**  >  **[Instance State]\(インスタンスの状態\)**  >  **[Terminate]\(終了\)** の順に選択します。

## <a name="delete-azure-resources"></a>Azure リソースを削除する

1. Azure portal にサインインします。
2. **[仮想マシン]** セクションに移動します。
3. このチュートリアルの第 1 部で作成した 3 つのノードのチェック ボックスをオンにします。
4. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、前の手順で作成したリソースを削除する方法について説明しました。

> [!div class="checklist"]
> * リソースをクリーンアップする

> [!div class="nextstepaction"]
> [先頭に戻る](service-fabric-tutorial-standalone-create-infrastructure.md)
