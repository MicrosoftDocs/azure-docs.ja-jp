---
title: Azure Machine Learning 環境について
titleSuffix: Azure Machine Learning
description: さまざまなコンピューティング先での再現可能、監査可能、移植可能な機械学習の依存関係定義を有効にする機械学習環境について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508471"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境とは?

Azure Machine Learning 環境は、機械学習トレーニングが行われる環境をカプセル化したものです。 そこでは、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、環境変数、およびソフトウェア設定を指定します。 また、ランタイム (Python、Spark、または Docker) も指定します。 環境は、Machine Learning ワークスペース内で管理およびバージョン管理されるエンティティであり、さまざまなコンピューティング先で再現、監査、移植が可能な機械学習ワークフローを実現します。

ローカル コンピューティングで `Environment` オブジェクトを使用して、次のことを行うことができます。
* トレーニング スクリプトを開発します。
* 大規模なモデルのトレーニング用に Azure Machine Learning コンピューティングで同じ環境を再利用します。
* その同じ環境を使ってモデルをデプロイします。
* 既存のモデルがトレーニングされた環境を見直します。

次の図は、実行構成 (トレーニング用) と推論およびデプロイ構成 (Web サービス デプロイ用) の両方で、どのように 1 つの `Environment` オブジェクトを使用できるかを示しています。

![機械学習ワークフローでの環境の図](./media/concept-environments/ml-environment.png)

環境、コンピューティング ターゲット、およびトレーニング スクリプトが一体となって、トレーニング実行の完全な仕様である実行構成を形成します。

## <a name="types-of-environments"></a>環境の種類

環境は、*キュレーション*、*ユーザー管理*、*システム管理* の 3 つのカテゴリに大別できます。

キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 これらには、現状のまま使用する目的で、Python のパッケージと設定のコレクションが含まれていて、さまざまな機械学習フレームワークの使用を開始する助けとなります。 これらの事前に作成された環境を利用すると、デプロイ時間の短縮も可能です。 完全な一覧については、[キュレーションされた環境に関する記事](resource-curated-environments.md)を参照してください。

ユーザー管理環境では、ユーザーが環境を設定し、トレーニング スクリプトで必要なすべてのパッケージをコンピューティング先にインストールする必要があります。 Conda では環境やインストールの確認が自動的には行われません。 独自の環境を定義する場合、pip 依存関係としてバージョン `>= 1.0.45` の `azureml-defaults` を指定する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

Python 環境とスクリプトの依存関係を [Conda](https://conda.io/docs/) で自動的に管理したいときは、システム管理環境を使用します。 新しい Conda 環境は、Conda の依存関係オブジェクトに基づいて構築されます。 Azure Machine Learning service では、手動で構成できないリモート コンピューティング ターゲットで有用であることが理由で、既定ではこの種類の環境が想定されています。

## <a name="create-and-manage-environments"></a>環境を作成および管理する

環境は次の方法で作成できます。

* 選別された環境を使用するか、独自の依存関係を定義することにより、新しい `Environment` オブジェクトを定義する。
* 自分のワークスペースから既存の `Environment` オブジェクトを使用する。 この方法では、依存関係に関して一貫性と再現性を確保できます。
* 既存の Anaconda 環境定義からインポートする。
* Azure Machine Learning CLI を使用する
* [VS Code 拡張機能を使用する](how-to-manage-resources-vscode.md#create-environment)

特定のコード サンプルについては、[環境の使用方法](how-to-use-environments.md#create-an-environment)に関するページの「環境の作成」セクションを参照してください。 環境はワークスペースから簡単に管理することができます。 これらには次の機能が含まれます。

* 実験を送信すると、環境は自動的にワークスペースに登録されます。 また、手動で登録することもできます。
* ワークスペースから環境を取り込んで、トレーニングやデプロイに使用したり、環境定義を編集したりできます。
* バージョン管理を使用すると、環境へのこれまでの変更を確認することができるため、再現性を確保することができます。
* 環境から Docker イメージを自動的に構築できます。

コード例については、[環境の使用方法](how-to-use-environments.md#manage-environments)に関するページの「環境の管理」セクションを参照してください。

## <a name="environment-building-caching-and-reuse"></a>環境のビルド、キャッシュ、再利用

Azure Machine Learning service では、Docker イメージと Conda 環境に環境定義が組み込まれます。 また、後続のトレーニング実行とサービス エンドポイントのデプロイで再利用できるように、環境はキャッシュされます。 トレーニング スクリプトをリモートで実行するには、Docker イメージを作成する必要があります。一方、ローカル実行では、Conda 環境を直接使用できます。 

### <a name="submitting-a-run-using-an-environment"></a>環境を使用して実行を送信する

環境を使用して初めてリモート実行を送信するときには、Azure Machine Learning service によって、ワークスペースに関連付けられている Azure Container Registry (ACR) で [ACR ビルド タスク](../container-registry/container-registry-tasks-overview.md)が呼び出されます。 ビルドされた Docker イメージは、ワークスペースの ACR にキャッシュされます。 キュレーションされた環境は、グローバル ACR にキャッシュされた Docker イメージによって支援されます。 イメージは、実行の開始時に、コンピューティング先によって関連する ACR から取得されます。

ローカルでの実行の場合、環境の定義に基づいて、Docker または Conda 環境が作成されます。 スクリプトはその後、ターゲット コンピューティング (ローカルのランタイム環境またはローカルの Docker エンジン) で実行されます。

### <a name="building-environments-as-docker-images"></a>Docker イメージとしての環境のビルド

ワークスペースの ACR にまだ環境定義が存在しない場合は、新しいイメージが構築されます。 イメージのビルドは、次の 2 つの手順で構成されます。

 1. 基本イメージのダウンロードと、Docker 手順の実行
 2. 環境定義で指定されている Conda の依存関係に従った Conda 環境の構築

[ユーザーが管理する依存関係](/python/api/azureml-core/azureml.core.environment.pythonsection)が指定された場合、2 番目の手順は省略されます。 この場合、ユーザーがすべての Python パッケージをインストールする必要があります。それには、Python パッケージを基本イメージに含めるか、最初の手順でカスタム Docker 手順を指定します。 また、ユーザーは Python 実行可能ファイルの正しい場所を指定する必要もあります。 [カスタム Docker 基本イメージ](how-to-deploy-custom-docker-image.md)を使用することも可能です。

### <a name="image-caching-and-reuse"></a>イメージのキャッシュと再利用

別の実行に同じ環境定義を使用する場合、Azure Machine Learning service では、ワークスペースの ACR にキャッシュされているイメージが再利用されます。 

キャッシュされているイメージの詳細を表示するには、[Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) メソッドを使用します。

キャッシュされたイメージを再利用するか、新しいイメージを作成するかを決定するため、サービスでは、環境定義から[ハッシュ値](https://en.wikipedia.org/wiki/Hash_table)が計算されて、それが既存の環境のハッシュと比較されます。 ハッシュは以下のものが基になっています。
 
 * 基本イメージのプロパティ値
 * カスタム Docker 手順のプロパティ値
 * Conda 定義内の Python パッケージの一覧
 * Spark 定義内のパッケージの一覧 

ハッシュが環境の名前またはバージョンに依存していない、つまり、環境の名前を変更する場合や、既存のものとまったく同じプロパティとパッケージを使用して新しい環境を作成する場合は、ハッシュ値は同一のままです。 ただし、Python パッケージの追加や削除、パッケージ バージョンの変更など、環境の定義に変更があると、ハッシュ値が変化します。 環境内の依存関係またはチャネルの順序を変更すると、新しい環境になり、それによって新しいイメージのビルドが必要になります。 キュレーションされた環境に対して何らかの変更があると、ハッシュが無効になり、新しい "キュレーションされていない" 環境になることに十分注意してください。

計算されたハッシュ値は、ワークスペースとグローバル ACR (またはローカル実行のコンピューティング ターゲット) のハッシュ値と比較されます。 一致するものがある場合は、キャッシュされたイメージがプルされます。その他の場合はイメージのビルドがトリガーされます。 キャッシュされたイメージをプルする時間にはダウンロード時間が含まれますが、新しくビルドされたイメージをプルする時間には、ビルド時間とダウンロード時間の両方が含まれます。 

次の図は、3 つの環境定義を示しています。 これらのうち、2 つは名前とバージョンが異っていますが、基本イメージと Python パッケージは同一です。 しかし、これらのハッシュは同じであるため、キャッシュされた同じイメージに対応しています。 3 番目の環境は Python パッケージとバージョンが異なるため、別のキャッシュされたイメージに対応します。

![Docker イメージとしての環境のキャッシュの図](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> ```numpy``` など、固定されていないパッケージの依存関係を持つ環境を作成すると、その環境では、"_環境の作成時_" にインストールされたパッケージのバージョンが引き続き使用されます。 また、一致した定義を持つ将来の環境では、古いバージョンが使用され続けます。 

パッケージを更新するには、バージョン番号を指定してイメージのリビルドを強制します (```numpy==1.18.1``` など)。 以前の動作シナリオが破壊される可能性がある、新しい依存関係 (入れ子になった依存関係など) がインストールされます。 

> [!WARNING]
>  [Environment.build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) メソッドでは、キャッシュされたイメージがリビルドされます。それによる副作用として、固定されていないパッケージが更新され、そのキャッシュされたイメージに対応するすべての環境定義の再現性が損なわれる可能性があります。

## <a name="next-steps"></a>次のステップ

* Azure Machine Learning で[環境を作成および使用する](how-to-use-environments.md)方法を確認します。
* [環境クラス](/python/api/azureml-core/azureml.core.environment%28class%29)については、Python SDK 参照ドキュメントを参照してください。
* [環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)については、R SDK 参照ドキュメントを参照してください。