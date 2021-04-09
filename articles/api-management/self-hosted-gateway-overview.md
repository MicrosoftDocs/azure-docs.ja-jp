---
title: セルフホステッド ゲートウェイの概要| Microsoft Docs
description: 組織がハイブリッド環境やマルチクラウド環境で API を管理するのに、Azure API Management のセルフホステッド ゲートウェイ機能がどのように役立つかを説明します。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989093"
---
# <a name="self-hosted-gateway-overview"></a>セルフホステッド ゲートウェイの概要

この記事では、Azure API Management のセルフホステッド ゲートウェイ機能を使ってハイブリッドおよびマルチクラウドの API 管理を実現する方法について説明します。また、そのアーキテクチャの概要を紹介し、機能を取り上げます。

## <a name="hybrid-and-multi-cloud-api-management"></a>ハイブリッドおよびマルチクラウドでの API 管理

セルフホステッド ゲートウェイ機能は、ハイブリッド環境とマルチクラウド環境に対する API Management サポートを拡張し、オンプレミスや複数のクラウドにわたってホストされている API を、組織が Azure の単一 API Management サービスから効率的かつ安全に管理できるようにします。

セルフホステッド ゲートウェイを使用すると、お客様は、コンテナー化されたバージョンの API Management ゲートウェイ コンポーネントを、API をホストしているのと同じ環境に柔軟にデプロイできるようになります。 すべてのセルフホステッド ゲートウェイは、フェデレーションされている API Management サービスから管理されます。これによりお客様には、内部と外部のすべての API にわたる可視性と統合された管理エクスペリエンスが提供されます。 ゲートウェイを API の近くに配置することで、お客様は API トラフィック フローを最適化し、セキュリティとコンプライアンスの要件に対応することができます。

それぞれの API Management サービスは、以下の主なコンポーネントで構成されています。

-   API として公開される管理プレーン。Azure portal、PowerShell、およびその他のサポートされているメカニズムによってサービスを構成するために使用されます。
-   ゲートウェイ (またはデータ プレーン) は、API 要求のプロキシ処理、ポリシーの適用、テレメトリの収集を行います
-   API を利用するための検索、学習、およびオンボードのために開発者によって使用される開発者ポータル

既定では、これらのすべてのコンポーネントが Azure にデプロイされ、API を実装しているバックエンドがホストされている場所に関係なく、すべての API トラフィック (下図では実線の黒い矢印として示されています) が Azure 経由で流れるようになります。 このモデルの運用上のシンプルさは、待機時間の増加やコンプライアンスの問題という代償によって実現されており、場合によって追加のデータ転送料金が発生します。

![セルフホステッド ゲートウェイを使用しない場合の API トラフィック フロー](media/self-hosted-gateway-overview/without-gateways.png)

セルフホステッド ゲートウェイをバックエンド API 実装がホストされているのと同じ環境にデプロイすると、API トラフィックがバックエンド API に直接流れることができます。これにより、待機時間が短縮されて、データ転送コストが最適化され、コンプライアンスを実現でき、同時に、実装がホストされている場所に関係なく、組織内のすべての API を 1 か所で管理、監視、検出するというメリットも維持されます。

![セルフホステッド ゲートウェイを使用する場合の API トラフィック フロー](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>パッケージと機能

セルフホステッド ゲートウェイは、すべての API Management サービスの一部として Azure にデプロイされるマネージド ゲートウェイと機能的に同等の、コンテナー化されたバージョンです。 セルフホステッド ゲートウェイは、Microsoft Container Registry から Linux ベースの Docker [コンテナー](https://aka.ms/apim/sputnik/dhub)として入手できます。 オンプレミスのサーバー クラスターで実行されている Docker、Kubernetes などのコンテナー オーケストレーション ソリューション、クラウド インフラストラクチャ、または評価と開発が目的の場合はパーソナル コンピューター上にデプロイできます。

マネージ ゲートウェイにある次の機能は、セルフホステッド ゲートウェイでは **使用できません**。

- Azure Monitor ログ
- アップストリーム (バックエンド側) の TLS バージョンと暗号管理
- API Management サービスにアップロードされた [CA ルート証明書](api-management-howto-ca-certificates.md)を使用した、サーバーとクライアントの証明書の検証。 詳細については、「[セルフホステッド ゲートウェイでの証明書の検証](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway)」を参照してください。
- [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) との統合
- TLS セッションの再開
- クライアント証明書の再ネゴシエーション。 これは、[クライアント証明書の認証](api-management-howto-mutual-certificates-for-clients.md)が動作するには、API コンシューマーが初期 TLS ハンドシェイクの一部として証明書を提示する必要があることを意味します。 これを保証するには、セルフホステッド ゲートウェイのカスタム ホスト名を構成するときに、クライアント証明書のネゴシエート設定を有効にします。
- ビルトイン キャッシュ。 セルフホステッド ゲートウェイで外部キャッシュを使用する方法については、こちらの[ドキュメント](api-management-howto-cache-external.md)を参照してください。

## <a name="connectivity-to-azure"></a>Azure への接続性

セルフホステッド ゲートウェイには、ポート 443 での Azure への送信 TCP/IP 接続が必要です。 各セルフホステッド ゲートウェイは、1 つの API Management サービスに関連付けられている必要があり、管理プレーンを介して構成されます。 セルフホステッド ゲートウェイは、以下の場合に Azure への接続を使用します。

-   1 分ごとのハートビート メッセージ送信による状態の報告
-   構成の更新の、定期的チェック (10 秒ごと) と、入手可能な場合は常に実行する適用
-   要求ログとメトリックの Azure Monitor への送信 (これを行うよう構成されている場合)
-   Application Insights へのイベントの送信 (これを行うよう設定されている場合)

Azure への接続性が失われると、セルフホステッド ゲートウェイは構成の更新の受信、状態の報告、テレメトリのアップロードができなくなります。

セルフホステッド ゲートウェイは、"静的に失敗する" ように設計されており、Azure への接続が一時的に失われても問題は生じません。 これは、ローカル構成のバックアップの有無にかかわらずデプロイできます。 前者の場合、セルフホステッド ゲートウェイでは定期的に、コンテナーまたはポッドに接続された永続的ボリュームに最新のダウンロードされた構成のバックアップ コピーが保存されます。

構成のバックアップがオフになっていて、Azure への接続が断たれると、以下のようになります。

-   セルフホステッド ゲートウェイの実行は、構成のメモリ内コピーを使用して機能し続けます
-   停止されていたセルフホステッド ゲートウェイは起動できなくなります

構成のバックアップがオンになっていて、Azure への接続が断たれると、以下のようになります。

-   セルフホステッド ゲートウェイの実行は、構成のメモリ内コピーを使用して機能し続けます
-   停止されていたセルフホステッド ゲートウェイは、構成のバックアップ コピーの使用を開始できるようになります

接続が復元されると、停止の影響を受けた各セルフホステッド ゲートウェイは、関連付けられている API Management サービスに自動的に再接続し、ゲートウェイが "オフライン" になっていた間に発生したすべての構成の更新をダウンロードします。

## <a name="next-steps"></a>次のステップ

-   [このトピックの追加の背景情報についてのホワイトペーパーを読む](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Docker にセルフホステッド ゲートウェイをデプロイする](how-to-deploy-self-hosted-gateway-docker.md)
-   [Kubernetes にセルフホステッド ゲートウェイをデプロイする](how-to-deploy-self-hosted-gateway-kubernetes.md)
