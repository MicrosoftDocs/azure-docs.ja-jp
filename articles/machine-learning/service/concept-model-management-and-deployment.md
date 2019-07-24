---
title: ML モデルを管理、登録、再デプロイ、および監視する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して、モデルをデプロイ、管理、および監視して、継続的に向上させる方法を説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning service を使用してデプロイできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59275444"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Azure Machine Learning service でモデルを管理、デプロイ、および監視する

この記事では、Azure Machine Learning service を使用して、お使いのモデルをデプロイ、管理、および監視して、継続的に向上させる方法を説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning を使用してデプロイできます。 

次の図は、完全なデプロイ ワークフローを示しています。[![Azure Machine Learning のデプロイ ワークフロー](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

デプロイ ワークフローには次の手順が含まれます。
1. **モデルを登録する**。お使いの Azure Machine Learning service ワークスペースでホストされているレジストリに登録します
1. **イメージを登録する**。このイメージにより、ポータブル コンテナー内で、モデルと、スコアリング スクリプトおよび依存関係がペアリングされます 
1. **デプロイする**。イメージを Web サービスとしてクラウドまたはエッジ デバイスにデプロイします
1. **データを監視して収集する**
1. 新しいイメージを使用するようにデプロイを**更新します**。

各手順を個別に、または 1 つのデプロイ コマンドの一部として実行できます。 さらに、次の図に示すように、デプロイを **CI/CD ワークフロー**に統合することもできます。

[!["Azure Machine Learning の継続的インテグレーション/継続的デプロイ (CI/CD) サイクル"](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>手順 1:モデルの登録

モデルの登録によって、モデルを Azure クラウドに格納し、ワークスペースでバージョンを管理できます。 モデルのレジストリでは、トレーニングしたモデルの整理と追跡が容易に行えます。
 
登録されたモデルは、名前とバージョンによって識別されます。 モデルを登録するたびに、既存のモデルと同じ名前で登録され、レジストリによってバージョンがインクリメントされます。 モデルの検索時に使用できる追加のメタデータ タグを、登録中に指定することもできます。 Azure Machine Learning service は、Python 3 を使用して読み込むことができる任意のモデルをサポートしています。 

イメージによって使用されているモデルを削除することはできません。

詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#registermodel)に関するページの、モデルの登録のセクションを参照してください。

pickle 形式で格納されているモデルの登録の例については、「[チュートリアル: 画像分類モデルをトレーニングする](tutorial-deploy-models-with-aml.md)」を参照してください。

ONNX モデルの使用については、[ONNX と Azure Machine Learning](how-to-build-deploy-onnx.md) に関するドキュメントを参照してください。

## <a name="step-2-register-image"></a>手順 2:イメージを登録する

イメージによって信頼性の高いモデル デプロイと、そのモデルの使用に必要なすべてのコンポーネントが準備されます。 イメージには次の項目が含まれます。

* モデル
* スコアリング エンジン
* スコアリング ファイルまたはアプリケーション
* モデルをスコアを付けるときに必要なすべての依存関係

イメージには、ログ記録と監視用の SDK コンポーネントを含めることもできます。 SDK ログ データは、モデルの入出力を含め、モデルの微調整や再トレーニングに使用できます。

Azure Machine Learning では、最もよく使用されているフレームワークがサポートされていますが、一般的には、pip でインストールできる任意のフレームワークが動作します。

ワークスペースの作成時に、そのワークスペースで使用される他の Azure リソースもいくつか作成されています。
既定のイメージの作成に使用されるオブジェクトはすべて、お使いのワークスペースの Azure ストレージ アカウントに格納されています。 イメージを作成するときに、追加のメタデータ タグを指定できます。 さらにメタデータ タグは、イメージ レジストリによって格納され、イメージを検索するときにクエリできます。

Azure Container Registry にアップロードして、Azure Machine Learning サービスにより使用できる、カスタム イメージを使用することもできます。

詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#configureimage)に関するページの、イメージの構成と登録のセクションを参照してください。

## <a name="step-3-deploy-image"></a>手順 3:イメージをデプロイする

登録済みのイメージを、クラウドまたはエッジ デバイスにデプロイできます。 デプロイ プロセスでは、お使いのモデルの監視、負荷分散、および自動スケーリングに必要なリソースがすべて作成されます。 デプロイされたサービスへのアクセスを証明書ベースの認証で保護するには、デプロイ中にセキュリティ アセットを指定します。 既存のデプロイをアップグレードして、新しいイメージを使用することもできます。

Web サービスのデプロイも検索可能です。 たとえば、特定のモデルまたはイメージのすべてのデプロイを検索できます。

[![ターゲットの推論](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

ご自身のイメージを、クラウド内の次のデプロイ ターゲットにデプロイできます。

* Azure Container Instances
* Azure Kubernetes Service
* Azure FPGA マシン
* Azure IoT Edge デバイス

ご自身のサービスがデプロイされると、推論要求が自動的に負荷分散され、クラスターは、オンデマンドでスパイクに対応できるようにスケーリングされます。 お使いのワークスペースに関連付けられている Azure Application Insights サービスに、[お使いのサービスに関するテレメトリをキャプチャできます](how-to-enable-app-insights.md)。

詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#deploy)に関するページの、デプロイのセクションを参照してください。

## <a name="step-4-monitor-models-and-collect-data"></a>手順 4:モデルを監視して、データを収集する

お使いのモデルからの入力、出力、およびその他の関連データを監視できるように、モデルのログ記録とデータ キャプチャ用の SDK を利用できます。 データは、ご自身のワークスペースの Azure Storage アカウントに BLOB として格納されています。

お使いのモデルで SDK を使用するには、その SDK をスコアリング スクリプトまたはアプリケーションにインポートします。 その後、SDK を使用して、パラメーター、結果、入力の詳細などのデータをログに記録します。

イメージをデプロイするたびにモデル データ収集を有効にするように設定すると、ご自身の個人 BLOB ストアに対する資格情報など、データのキャプチャに必要な詳細情報が自動的にプロビジョニングされます。

> [!Important]
> モデルから収集したデータは Microsoft には表示されません。 データは、ご自身のワークスペースの Azure ストレージ アカウントに直接送信されます。

詳細については、[モデル データ収集を有効にする方法](how-to-enable-data-collection.md)に関するページを参照してください。

## <a name="step-5-update-the-deployment"></a>手順 5:デプロイを更新する

モデルに対する更新プログラムは自動で登録されません。 同様に、新しいイメージを登録しても、以前のバージョンのイメージから作成されたデプロイ自動的に更新されません。 代わりに、手動でモデルの登録、イメージの登録、およびモデルの更新を行う必要があります。 詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#update)に関するページの、更新のセクションを参照してください。

## <a name="next-steps"></a>次の手順

Azure Machine Learning サービスを使用して[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。 デプロイの例については、「[チュートリアル: Azure Container Instances に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」を参照してください。

[デプロイされたモデルを Web サービスとして使用する](how-to-consume-web-service.md)クライアント アプリケーションとサービスの作成方法を確認します。
