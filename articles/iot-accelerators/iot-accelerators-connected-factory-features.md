---
title: コネクテッド ファクトリ ソリューションの機能 - Azure | Microsoft Docs
description: コネクテッド ファクトリ事前構成済みソリューションの機能の概要。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 2a11640959a8c7fdd0d238aba92698eb47934969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080448"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>コネクテッド ファクトリ IoT ソリューション アクセラレータとは

コネクテッド ファクトリは Microsoft の Azure Industrial IoT 参照アーキテクチャの実装で、オープンソース ソリューションとしてパッケージ化されています。 商用製品の開始点として使用できます。 コネクテッド ファクトリ ソリューションの事前構築済みバージョンを [Azure IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com/#solutions/types/CF)から Azure サブスクリプションにデプロイできます。

![コネクテッド ファクトリ ソリューションのダッシュボード](./media/iot-accelerators-connected-factory-features/dashboard.png)

コネクテッド ファクトリ ソリューション アクセラレータの[コードは GitHub で入手できます](https://github.com/Azure/azure-iot-connected-factory)。

コネクテッド ファクトリには、次のような機能があります。

## <a name="industrial-device-interoperability"></a>産業用デバイスの相互運用性

- OPC UA インターフェイスを使用して産業用資産に接続します。
- (Docker コンテナーで OPC UA サーバーを実行している) シミュレートされた生産ラインを使用して、そこからライブ テレメトリを表示します。
- クラウド ダッシュボードから OPC UA サーバーの OPC UA 情報モデルを参照します。

## <a name="remote-management"></a>リモート管理

- クラウド ダッシュボードから OPC UA 資産を構成します (メソッドの呼び出し、データの読み取り、書き込み)。
- クラウド ダッシュボードで、OPC UA アセットからの利用統計情報の発行と発行の取り消しを行います。

## <a name="cloud-dashboard"></a>クラウド ダッシュボード

- クラウド ダッシュボードでテレメトリ プレビューを直接表示します。
- Time Series Insights エクスプローラー ダッシュボードを使用して、利用統計情報の傾向を表示し、相関関係を作成します。
- クラウド ダッシュボードから、計算した総合設備効率 (OEE) と主要業績評価指標 (KPI) を参照します。
- 産業用資産階層をツリー トポロジと対話型マップに表示します。
- クラウド ダッシュボードからアラートを表示して確認し、閉じます。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) は、大量の時系列データの保存、視覚化、クエリを目的として構築されています。 コネクテッド ファクトリはこのサービスを活用します。
- コネクテッド ファクトリはこのサービスと統合されているので、デバイス データの詳細なリアルタイム分析を実行できます。

## <a name="rules-and-alerts"></a>ルールとアラート

[しきい値に基づくアラート ルールを構成](iot-accelerators-connected-factory-configure.md)します。

## <a name="end-to-end-security"></a>エンドツーエンドのセキュリティ

- ロールベースのアクセス制御 (RBAC) を使用してユーザーのセキュリティ アクセス許可を構成します。
- OPC UA 認証 (X.509 証明書を使用) とセキュリティ トークンを使用してエンドツーエンドの暗号化が実装されます。

## <a name="customizability"></a>カスタマイズ性

- ソリューションをカスタマイズして、独自のビジネス要件を満たします。
- 完全なソリューション ソース コードは GitHub にあります。 [コネクテッド ファクトリ事前構成済みソリューション](https://github.com/Azure/azure-iot-connected-factory)のリポジトリをご覧ください。

## <a name="next-steps"></a>次の手順

コネクテッド ファクトリ ソリューション アクセラレータについて詳しくは、クイック スタート「[工業用 IoT デバイスを管理するクラウドベースのソリューションを試す](quickstart-connected-factory-deploy.md)」をご覧ください。
