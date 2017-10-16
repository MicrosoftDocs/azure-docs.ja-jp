---
title: "Azure Machine Learning モデル管理の概念の概要 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning のモデル管理の概念について説明します。"
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 31b859d86e82c92839462280721c5f84f1d923cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management"></a>Azure Machine Learning のモデル管理

Azure Machine Learning モデル管理を使うと、機械学習のワークフローとモデルを管理してデプロイできます。 

モデル管理は次の機能を提供します。
- モデルのバージョン管理
- 運用環境でのモデルの追跡
- [Azure Container Service](https://azure.microsoft.com/services/container-service/) と [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) による AzureML コンピューティング環境から運用環境へのモデルのデプロイ
- モデルでの Docker コンテナーの作成と、そのローカルでのテスト
- 自動化されたモデルの再トレーニング
- アクションにつながる洞察のためのモデルのテレメトリのキャプチャ 

Azure Machine Learning モデル管理は、モデルのバージョンのレジストリを提供します。 また、Machine Learning のコンテナーを REST API としてパッケージ化してデプロイするための、自動化されたワークフローも提供します。 モデルとその実行時の依存関係は、予測 API と共に Linux ベースの Docker コンテナーにパッケージ化されます。 

Azure Machine Learning のコンピューティング環境は、モデルをホストするためのスケーラブルなクラスターをセットアップして管理するのに役立ちます。 コンピューティング環境は Azure Container Service に基づいています。 Azure Container Service は、Machine Learning API を REST API エンドポイントとして自動的に公開し、次の機能を備えています。

- 認証
- 負荷分散
- 自動スケールアウト
- 暗号化

Azure Machine Learning モデル管理では、これらの機能が CLI、API、および Azure Portal によって提供されます。 

Azure Machine Learning のモデル管理は次の情報を使います。

 - モデル ファイルまたはモデル ファイルのディレクトリ
 - モデル スコア付け機能を実装しているユーザーが作成した Python ファイル
 - 実行時の依存関係の一覧を含む Conda 依存関係ファイル
 - ランタイム環境の選択 
 - API パラメーターのスキーマ ファイル 

この情報は、次のアクションを実行するときに使われます。

- モデルの登録
- コンテナーの作成時に使われるマニフェストの作成
- Docker コンテナー イメージの構築
- Azure Container Service へのコンテナーのデプロイ
 
次の図では、モデルがクラスターにどのように登録およびデプロイされるかを示します。 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>モデルを作成して管理する 
モデルを Azure Machine Learning モデル管理に登録し、運用環境でのモデルのバージョンを追跡できます。 再現とガバナンスを容易にするため、サービスはすべての依存関係および関連付けられている情報をキャプチャします。 パフォーマンスをさらに深く洞察するため、提供されている SDK を使ってモデルのテレメトリをキャプチャできます。 モデルのテレメトリは、ユーザー指定のストレージにアーカイブされます。 後でモデルのテレメトリを使って、モデルのパフォーマンスの分析、再トレーニング、ビジネスの洞察の取得を行うことができます。

## <a name="create-and-manage-manifests"></a>マニフェストを作成して管理する 
モデルでは、追加のアーティファクトを運用環境にデプロイする必要があります。 システムでは、モデル、依存関係、推論スクリプト (別名スコア付けスクリプト)、サンプル データ、スキーマなどが収められたマニフェストを作成する機能が提供されています。このマニフェストは、Docker コンテナー イメージを作成するためのレシピとして機能します。 マニフェストを自動生成し、異なるバージョンを作成し、管理することができます。 

## <a name="create-and-manage-docker-container-images"></a>Docker コンテナー イメージを作成して管理する 
前の手順のマニフェストを使って、それぞれの環境に Docker ベースのコンテナー イメージを構築できます。 コンテナー化された Docker ベースのイメージを使うと、次のコンピューティング環境でこれらのイメージを柔軟に実行できます。

- [Kubernetes ベースの Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- オンプレミスのコンテナー サービス
- 開発環境
- IoT デバイス

これらの Docker ベースのコンテナー イメージには、予測の生成に必要なすべての依存関係が含まれています。 

## <a name="deploy-docker-container-images"></a>Docker コンテナー イメージをデプロイする 
Azure Machine Learning モデル管理では、Docker ベースのコンテナー イメージを、1 つのコマンドで、ML コンピューティング環境によって管理される Azure Container Service にデプロイできます。 これらのデプロイは、次の機能を提供するフロントエンド サーバーで作成されます。

- 大規模な低待機時間の予測
- 負荷分散
- ML エンドポイントの自動スケーリング
- API キーの承認
- API の Swagger ドキュメント

次の構成設定により、デプロイのスケールとテレメトリを制御できます。

- 各 Web サービス レベルのシステム ログとモデルのテレメトリ。 有効な場合、すべての標準出力ログは [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) にストリーミングされます。 モデルのテレメトリは、ユーザー指定のストレージにアーカイブされます。 
- 自動スケールと同時実行の制限。 これらの設定は、既存のクラスター内での負荷に基づいて、デプロイされるコンテナーの数を自動的に増やします。 予測の待機時間のスループットと一貫性も制御します。

## <a name="consumption"></a>使用量 
Azure Machine Learning モデル管理は、デプロイされたモデルの REST API と共に Swagger ドキュメントを作成します。 API キーとモデル入力を使って REST API を呼び出すことによりデプロイされたモデルを使用し、基幹業務アプリケーションの一部として予測を取得できます。 REST API の呼び出しについては、Java、[Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)、C# の各言語のサンプル コードを GitHub で入手できます。 Azure Machine Learning モデル管理の CLI では、これらの REST API を使う簡単な方法が提供されます。 1 つの CLI コマンド、Swagger 対応のアプリケーション、または curl を使って、API を使うことができます。 

## <a name="retraining"></a>再トレーニング 
Azure Machine Learning モデル管理で提供されている API を使って、モデルを再トレーニングできます。 また、この API を使うと、モデルの更新バージョンで既存のデプロイを更新することもできます。 データ サイエンス ワークフローの一環として、実験環境でモデルを再作成します。 次に、モデル管理にモデルを登録し、既存のデプロイを更新します。 更新は、1 つの UPDATE CLI コマンドを使って実行されます。 UPDATE コマンドは、API URL またはキーを変更することなく、既存のデプロイを更新します。 モデルを使っているアプリケーションは、コード変更なしで引き続き動作し、新しいモデルを使って向上した予測を取得するようになります。

これらの概念がわかる完全なワークフローを次の図に示します。

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ) 
- サポートされているデータ型は何ですか。 Web サービスへの入力として NumPy 配列を直接渡すことができますか。

   generate_schema SDK を使って作成されたスキーマ ファイルを提供している場合は、NumPy および Pandas DF を渡すことができます。 JSON のシリアル化可能な入力を渡すこともできます。 バイナリ エンコードされた文字列としてイメージを渡すこともできます。

- Web サービスは、複数の入力をサポートしますか。または、異なる入力を解析しますか。 

   はい、1 つの JSON 要求にパッケージ化された複数の入力を辞書として取得できます。 各入力は、1 つの一意な辞書キーに対応します。

- Web サービスに対する要求によってアクティブ化される呼び出しは、ブロック呼び出しですか、それとも非同期呼び出しですか。

   サービスが CLI または API の一部としてリアルタイム オプションを使って作成された場合は、ブロック/同期呼び出しです。 リアルタイムで高速なことが期待されます。 ただし、クライアント側では、非同期 HTTP ライブラリを使って呼び出し、クライアント スレッドのブロックを回避できます。

- Web サービスは何個の要求を同時に処理できますか。

   クラスターと Web サービスの規模によって異なります。 サービスのレプリカを 100 個までスケールアウトでき、そうすれば多くの要求を同時に処理できます。 また、レプリカあたりの最大同時要求数を構成して、サービスのスループットを上げることもできます。

- Web サービスは何個の要求をキューに格納できますか。

   構成可能です。 既定では、1 つのレプリカごとに最大 10 に設定されていますが、アプリケーションの要件に合わせて増減できます。 通常、キューの要求の数を増やすとサービスのスループットが向上しますが、高いパーセンタイルでは待機時間が悪化します。 遅延時間を一定にするには、キューを低い値 (1 ～ 5) に設定して、スループットを処理するレプリカの数を増やします。 また、自動スケールをオンにして、レプリカの数を負荷に基づいて自動的に調整することもできます。 

- 同じコンピューターまたはクラスターを複数の Web サービス エンドポイントに使うことはできますか。

   そして、 同じクラスターで 100 個のサービス/エンドポイントを実行できます。 

## <a name="next-steps"></a>次のステップ
モデル管理を使い始めるには、「[モデル管理のセットアップ](model-management-configuration.md)」をご覧ください。
