---
title: ファイアウォールを使用する
titleSuffix: Azure Machine Learning
description: Azure Firewall を使用して Azure Machine Learning ワークスペースへのアクセスを制御します。 Azure Machine Learning が正常に機能するためにファイアウォールの通過を許可する必要があるホストについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 581feff516e0f0cd820c94290d4aaa729cc4d3a4
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88889942"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>ファイアウォールの内側で Azure Machine Learning のワークスペースを使用する

この記事では、Azure Firewall を構成して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御する方法について説明します。   Azure Machine Learning のセキュリティ保護の詳細については、「[Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)」を参照してください

このドキュメントの情報は [Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) を使用した場合に基づいていますが、他のファイアウォール製品でも使用できます。 ファイアウォール経由の通信を許可する方法について不明な点がある場合は、使用しているファイアウォールのドキュメントを参照してください。

## <a name="application-rules"></a>アプリケーション ルール

ファイアウォールで、この記事のアドレスとの間で送受信されるトラフィックを許可する_アプリケーション ルール_を作成します。

> [!TIP]
> ネットワーク ルールを追加するときは、__プロトコル__ を任意に設定し、ポートを `*` に設定します。
>
> Azure Firewall の構成について詳しくは、[Azure Firewall のデプロイと構成](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)に関する記事をご覧ください。

## <a name="microsoft-hosts"></a>Microsoft のホスト

正しく構成しないと、ワークスペースを使用したときにファイアウォールが原因で問題が発生する可能性があります。 いずれも Azure Machine Learning ワークスペースで使用されるさまざまなホスト名があります。

このセクションのホストは Microsoft によって所有されており、ホストではワークスペースが適切に機能するために必要なサービスが提供されます。

| **ホスト名** | **目的** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | クラスターをトレーニングします |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Azure Machine Learning スタジオによって使用されます |
| **\*.azureml.ms** | Azure Machine Learning API によって使用されます |
| **\*.experiments.azureml.net** | Azure Machine Learning で実行される実験で使用されます |
| **\*.modelmanagement.azureml.net** | モデルを登録してデプロイするために使用されます|
| **mlworkspace.azure.ai** | ワークスペースを表示するときに Azure portal によって使用されます |
| **\*.aether.ms** | Azure Machine Learning パイプラインを実行するときに使用されます |
| **\*.instances.azureml.net** | Azure Machine Learning のコンピューティング インスタンスです |
| **\*.instances.azureml.ms** | ワークスペースで Private Link が有効な場合の Azure Machine Learning のコンピューティング インスタンスです |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | 基本 Docker イメージ用の Microsoft Container Registry |
| **your-acr-server-name.azurecr.io** | Azure Container Registry が仮想ネットワークの背後にある場合にのみ必要です。 この構成では、Microsoft 環境からサブスクリプションの ACR インスタンスへのプライベート リンクが作成されます。 Azure Machine Learning ワークスペースの ACR サーバー名を使用します。 |
| **\*.notebooks.azure.net** | Azure Machine Learning studio のノートブックで必要です。 |
| **graph.windows.net** | ノートブックに必要です |

## <a name="python-hosts"></a>Python のホスト

このセクションのホストは、Python パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。 

| **ホスト名** | **目的** |
| ---- | ---- |
| **anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
| **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
| **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |

## <a name="r-hosts"></a>R のホスト

このセクションのホストは、R パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。

> [!IMPORTANT]
> 内部的には、Azure Machine Learning 用の R SDK では Python パッケージが使用されます。 そのため、Python ホストにもファイアウォールの通過を許可する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN パッケージをインストールするときに使用されます。 |

## <a name="azure-government-region"></a>Azure Government リージョン

Azure Government リージョンに必要な URL。

| **ホスト名** | **目的** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | 米国アリゾナ リージョン |
| **usgovvirginia.api.ml.azure.us** | 米国バージニア リージョン |

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 内で Azure ML の実験と推論のジョブを安全に実行する](how-to-enable-virtual-network.md)
