---
title: Azure Kubernetes のデプロイ センター
description: Azure DevOps のデプロイ センターを利用すると、アプリケーションの信頼性の高い Azure DevOps パイプラインを容易に設定できます
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932219"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Azure Kubernetes のデプロイ センター

Azure DevOps のデプロイ センターを利用すると、アプリケーションの信頼性の高い Azure DevOps パイプラインを容易に設定できます。 デプロイ センターでは、既定で、アプリケーションの更新プログラムが Kubernetes クラスターにデプロイされるように Azure DevOps パイプラインが構成されます。 構成済みの既定の Azure DevOps パイプラインを拡張し、デプロイ前の承認の取得、追加の Azure リソースのプロビジョニング、スクリプトの実行、アプリケーションのアップグレード、さらに追加の検証テストの実行など、より高度な機能を追加できます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーションの更新プログラムが Kubernetes クラスターにデプロイされるように Azure DevOps パイプラインを構成する。
> * 継続的インテグレーション (CI) パイプラインを確認する。
> * 継続的デリバリー (CD) パイプラインを確認する。
> * リソースをクリーンアップする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

* Azure Kubernetes Service (AKS) クラスター。

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure portal のメニュー バーの右側にある [[Cloud Shell]](../cloud-shell/overview.md) を選択します。

1. AKS クラスターを作成するには、以下のコマンドを実行します。

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>アプリケーションの更新プログラムを Kubernetes クラスターにデプロイする

1. 前のセクションで作成したリソース グループに移動します。

1. AKS クラスターを選択し、左側のブレードの **[デプロイ センター (プレビュー)]** を選択します。 **[Get started]\(作業を開始する\)** を選択します。

   ![デプロイ センターを指す矢印が記載された Azure portal を示すスクリーンショット。](media/deployment-center-launcher/settings.png)

1. コードの場所を選択し、 **[次へ]** を選択します。 次に、現在サポートされているリポジトリのいずれかを選択します: **[Azure Repos](/azure/devops/repos/index)** または **GitHub**。

    Azure Repos は、コードを管理するのに役立つバージョン管理ツールのセットです。 ソフトウェア プロジェクトが大きいか小さいかにかかわらず、できるだけ早期にバージョン管理を使用することをお勧めします。

    - **Azure Repos**: 既存のプロジェクトと組織からリポジトリを選択します。

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: GitHub アカウントのリポジトリを承認して選択します。

        ![GitHub をソースとして選択し、リポジトリを選択する GitHub のプロセスを示すアニメーション。](media/deployment-center-launcher/github.gif)


1. デプロイ センターによってリポジトリが分析され、対象の Dockerfile が検出されます。 Dockerfile を更新する場合は、識別されたポート番号を編集します。

    ![アプリケーションの設定](media/deployment-center-launcher/application-settings.png)

    リポジトリに Dockerfile が含まれていない場合は、コミットするようにメッセージが表示されます。

    ![リポジトリで Dockerfile が見つからなかったというメッセージが表示されたデプロイ センターを示すスクリーンショット。](media/deployment-center-launcher/dockerfile.png)

1. 既存のコンテナー レジストリを選択するか作成し、 **[完了]** を選択します。 パイプラインが自動的に作成され、[Azure Pipelines](/azure/devops/pipelines/index) のキューにビルドが配置されます。

    Azure Pipelines は、コード プロジェクトを自動的にビルドしてテストし、他のユーザーが使用できるようにするクラウド サービスです。 Azure Pipelines では、継続的インテグレーションと継続的デリバリーが組み合わされ、コードが継続的に一貫してテストおよびビルドされて、任意のターゲットに出荷されます。

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. リンクを選択して、進行中のパイプラインを表示します。

1. デプロイが完了すると、成功ログが表示されます。

    ![緑色のチェック マーク アイコンが付いたリリース 1 のデプロイ センターを示すスクリーンショット。](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

デプロイ センターでは、Azure DevOps 組織の CI/CD パイプラインが自動的に構成されます。 パイプラインは、調査およびカスタマイズすることができます。

1. デプロイ センターのダッシュボードに移動します。  

1. 成功ログの一覧からビルド番号を選択して、プロジェクトのビルド パイプラインを表示します。

1. 右上隅にある省略記号 [...] を選択します。 メニューには、新しいビルドのキュー登録、ビルドの保持、ビルド パイプラインの編集など、いくつかのオプションが表示されます。 **[パイプラインを編集する]** を選択します。 

1. このウィンドウでは、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースの収集、イメージの作成、コンテナー レジストリへのイメージのプッシュ、デプロイに使用される出力の発行など、さまざまなタスクが実行されます。

1. パイプラインの先頭で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ビルド パイプラインの下の **[履歴]** を選択します。 このウィンドウには、最近のビルド変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって監視され、バージョンを比較できます。

1. **[トリガー]** を選択します。 CI プロセスにブランチを含めることも、CI プロセスからブランチを除外することもできます。

1. **[保持]** を選択します。 シナリオに応じて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-cd-pipeline"></a>CD パイプライン を確認する

デプロイ センターでは、Azure DevOps 組織と Azure サブスクリプションの間の関係が自動的に作成され、構成されます。 必要な手順には、Azure サブスクリプションを Azure DevOps で認証するための Azure サービス接続の設定が含まれます。 自動化プロセスによってリリース パイプラインも作成され、このパイプラインによって Azure への継続的デリバリーが提供されます。

1. **[パイプライン]** を選択し、 **[リリース]** を選択します。

1. リリース パイプラインを編集するには、 **[編集]** を選択します。

1. **[成果物]** ボックスの一覧から **[削除]** を選択します。 前の手順で調べた構築パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** の右側にある **[継続的デプロイ]** トリガーを選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 また、トリガーを無効にして、デプロイの手動実行が必要になるようにすることもできます。

1. パイプラインのすべてのタスクを調べるには、 **[タスク]** を選択します。 リリースでは、Tiller 環境が設定され、`imagePullSecrets` パラメーターが構成され、Helm ツールがインストールされて、Kubernetes クラスターに Helm チャートがデプロイされます。

1. リリース履歴を表示するには、 **[リリースの表示]** を選択します。

1. 概要を表示するには、 **[リリース]** を選択します。 リリース概要、関連付けられた作業項目、テストなど、任意のステージを選択して、複数のメニューを調べることができます。 

1. **[コミット]** を選択します。 このビューには、このデプロイに関連するコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログ]** を選択します。 ログには、有用なデプロイ情報が含まれています。デプロイ中でもデプロイ後も、それらを見ることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した関連リソースが必要なくなったら、削除してかまいません。 DevOps Projects ダッシュボードで削除機能を使用します。

## <a name="next-steps"></a>次のステップ

チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 また、この CI/CD モデルをご自身の他のパイプラインのテンプレートとして使用することもできます。
