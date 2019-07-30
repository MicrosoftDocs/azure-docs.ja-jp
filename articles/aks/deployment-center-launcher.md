---
title: Azure App Service でデプロイ センターを使用する
description: Azure DevOps のデプロイ センターを利用すると、アプリケーションの信頼性の高い Azure DevOps パイプラインを容易に設定できます
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861516"
---
# <a name="deployment-center-launcher"></a>デプロイ センター ランチャー

Azure DevOps のデプロイ センターを利用すると、アプリケーションの信頼性の高い DevOps パイプラインを容易に設定できます。 既定では、アプリケーションの更新プログラムが Kubernetes クラスターにデプロイされるように DevOps パイプラインが構成されます。 構成済みの既定の DevOps パイプラインを拡張し、デプロイ前の承認、追加の Azure リソースのプロビジョニング、スクリプトの実行、アプリケーションのアップグレード、さらに追加の検証テストの実行など、より高度な DevOps 機能を追加できます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーションの更新プログラムが k8s クラスターにデプロイされるように DevOps パイプラインを構成する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます

* Azure Kubernetes Service (AKS) クラスター

## <a name="create-aks-cluster"></a>AKS クラスターの作成

1. [Azure portal](https://portal.azure.com/) にログインします

1. Azure portal の右上隅にあるメニューの [[Cloud Shell]](https://docs.microsoft.com/azure/cloud-shell/overview) ボタンを選択します。

1. AKS クラスターを作成するには、以下のコマンドを実行します。

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>アプリケーションの更新プログラムを K8s クラスターにデプロイする

1. 上で作成したリソース グループに移動します。

1. AKS クラスターを選択し、左側のブレードの設定の **[デプロイ センター (プレビュー)]** をクリックします。 **[開始]** をクリックします。

   ![settings](media/deployment-center-launcher/settings.png)

1. コードの場所を選択し、 **[次へ]** をクリックします。 現在サポートされているリポジトリは、 **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** と **GitHub** です。 リポジトリの選択に基づいて、次の手順に従うことができます。

    Azure Repos は、コードの管理に使用できるバージョン管理ツールのセットです。 ソフトウェア プロジェクトが大きいか小さいかにかかわらず、できるだけ早くバージョン管理を使用することをお勧めします。

    - **Azure Repos**: 既存のプロジェクトと組織からリポジトリを選択します。

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: GitHub アカウントのリポジトリを承認して選択します。

        ![GitHub](media/deployment-center-launcher/github.gif)


1. リポジトリが分析されて、Dockerfile が検出されます。 それを更新する場合は、識別されたポート番号を編集します。

    ![アプリケーションの設定](media/deployment-center-launcher/application-settings.png)

    リポジトリに Dockerfile が含まれていない場合は、コミットするようにメッセージが表示されます。 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. 既存のコンテナー レジストリを選択するか作成し、 **[完了]** をクリックします。 パイプラインが自動的に作成され、[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops) のキューにビルドが配置されます。

    Azure Pipelines は、コード プロジェクトを自動的にビルドしてテストし、他のユーザーが使用できるようにするクラウド サービスです。 Azure Pipelines では、継続的インテグレーション (CI) と継続的デリバリー (CD) が組み合わされ、コードが継続的に一貫してテストおよびビルドされて、ターゲットに出荷されます。

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. リンクをクリックすると、進行中のパイプラインが表示されます。

1. デプロイが完了すると、次のような成功ログが表示されます。

    ![ログ](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

デプロイ センターでは、Azure DevOps 組織の CI/CD パイプラインが自動的に構成されます。 パイプラインは、調査およびカスタマイズすることができます。 

1. デプロイ センターのダッシュボードに移動します。  

1. 成功ログの一覧からビルド番号をクリックすると、プロジェクトのビルド パイプラインが表示されます。 

1. 右上隅にある省略記号 [...] をクリックします。 メニューには、新しいビルドのキュー登録、ビルドの一時停止、ビルド パイプラインの編集など、いくつかのオプションが表示されます。 **[パイプラインの編集]** を選択します。 

1. このウィンドウでは、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースの収集、イメージの作成、コンテナー レジストリへのイメージのプッシュ、デプロイに使用される出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの先頭で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ビルド パイプラインの下の **[履歴]** を選択します。 このウィンドウには、最近のビルド変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって監視され、バージョンを比較できます。

1. **[トリガー]** を選択します。 必要に応じて、CI プロセスのブランチを含めたり、除外したりできます。

1. **[Retention]\(保持期間\)** を選択します。 シナリオに基づいてビルドの数を保持または削除するポリシーを指定できます。

## <a name="examine-the-cd-pipeline"></a>CD パイプライン を確認する

デプロイ センターでは、Azure DevOps 組織から Azure サブスクリプションへの必要な手順が自動的に作成され、構成されます。 これらの手順には、Azure サブスクリプションを Azure DevOps で認証するための Azure サービス接続の設定が含まれます。 自動化によってリリース パイプラインも作成され、このパイプラインによって CD が Azure に提供されます。

1. **[パイプライン]** を選択し、 **[リリース]** を選択します。

1. リリース パイプラインを編集するには、 **[編集]** をクリックします。

1. **[成果物]** から **[削除]** を選択します。 前の手順で調べた構築パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的デプロイ]** トリガーを選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、トリガーを無効にして、デプロイの手動実行を必要にできます。

1. パイプラインのすべてのタスクを調べるには、 **[タスク]** をクリックします。 リリースでは、Tiller 環境が設定され、imagePullSecrets が構成され、Helm ツールがインストールされて、K8s クラスターに Helm チャートがデプロイされます。

1. リリース履歴を表示するには、 **[リリースの表示]** をクリックします。 

1. 概要を表示するには、 **[リリース]** をクリックします。 リリース概要、関連付けられた作業項目、テストなど、任意のステージをクリックして、複数のメニューを調べることができます。 

1. **[コミット]** を選択します。 このビューには、このデプロイに関連するコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログ]** を選択します。 ログには、有用なデプロイ情報が含まれています。 デプロイ中でもデプロイ後も、それらを見ることができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した関連リソースが必要なくなったら、削除してかまいません。 DevOps Projects ダッシュボードで削除機能を使用します。

## <a name="next-steps"></a>次の手順

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。 このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * アプリケーションの更新プログラムが k8s クラスターにデプロイされるように DevOps パイプラインを構成する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * リソースのクリーンアップ
