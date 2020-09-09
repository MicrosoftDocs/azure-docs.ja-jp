---
title: クイックスタート - Piggy Metrics サンプル アプリの概要 - Azure Spring Cloud
description: Azure Spring Cloud のデプロイで使用される Piggy Metrics サンプル アプリについて説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046784"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Piggy Metrics サンプル アプリの概要

このクイックスタートでは、Piggy Metrics と呼ばれる個人財務サンプルを使用して、Azure Spring Cloud サービスにアプリをデプロイする方法を示します。 Piggy Metrics は、マイクロサービス アーキテクチャ パターンを示し、サービスの内訳を強調します。 サービス検出、構成サーバー、ログ、メトリック、分散トレースなどの強力な Azure Spring Cloud 機能を使用すると、どのように Azure にデプロイされているかがわかります。

Azure Spring Cloud のデプロイの例を使用するには、ソース コードの場所だけが必要です。これは、必要に応じて提供されます。

## <a name="functional-services"></a>機能サービス
Piggy Metrics は、3 つのコア マイクロサービスに分解されます。 これらのすべては、個別にデプロイできるアプリケーションであり、ビジネス ドメイン別に整理されています。

* **アカウント サービス (デプロイされる)** : 一般的なユーザー入力ロジックと検証が含まれます。収入と支出の項目、貯蓄、アカウント設定などです。
* **統計サービス (このクイックスタートでは使用しません)** : 主要な統計パラメーターを計算し、各アカウントの時系列をキャプチャします。 データポイントには、基本通貨と期間に正規化された値が含まれます。 このデータは、アカウントの有効期間中のキャッシュ フロー ダイナミクスを追跡するために使用されます。
* **通知サービス (このクイックスタートでは使用しません)** : ユーザーの連絡先情報と通知設定 (リマインダーやバックアップの頻度など) を格納します。 スケジュールされたワーカーは、他のサービスから必要な情報を収集し、サブスクライブした顧客に電子メール メッセージを送信します。

## <a name="infrastructure-services"></a>インフラストラクチャ サービス
コア サービスの動作を支援するために、分散システムにはいくつかの一般的なパターンがあります。 Azure Spring Cloud には、Spring Boot アプリケーションの動作を拡張してこれらのパターンを実装する、強力なツールが用意されています。 

* **構成サービス (Azure Spring Cloud によってホストされます)** : Azure Spring Cloud Config は、水平方向にスケーラブルな、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリを使用します。
* **サービス検出 (Azure Spring Cloud によってホストされます)** : これにより、自動スケーリング、障害、およびアップグレードのために動的にアドレスを割り当てられた可能性があるサービス インスタンスのネットワークの場所を自動的に検出できます。
* **承認サービス (デプロイされます)** : 承認責任は、別のサーバーに完全に抽出されます。そのサーバーによって、バックエンド リソース サービスに OAuth2 トークンが付与されます。 承認サーバーはユーザーの承認を行い、境界内のマシン間の通信をセキュリティで保護します。
* **API ゲートウェイ (デプロイされます)** : 3 つのコア サービスは、外部 API をクライアントに公開します。 実際のシステムでは、システムの複雑さによって関数の数が急速に増加する可能性があります。 1 つの複雑な Web ページのレンダリングには、数百のサービスが関係していることがあります。 API ゲートウェイは、システムへの単一のエントリ ポイントです。要求を処理し、適切なバックエンド サービスにルーティングしたり、複数のバックエンド サービスを呼び出したりするために使用され、結果を集計します。 

## <a name="sample-usage-of-piggy-metrics"></a>Piggy Metrics の使用例
実装の詳細については、「[Piggy Metrics](https://github.com/Azure-Samples/piggymetrics)」を参照してください。 このサンプルでは、必要に応じてソース コードを参照します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure Spring Cloud インスタンスをプロビジョニングする](spring-cloud-quickstart-provision-service-instance.md)
