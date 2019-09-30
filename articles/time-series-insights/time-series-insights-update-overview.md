---
title: 概要:Azure Time Series Insights プレビュー | Microsoft Docs
description: Azure Time Series Insights プレビューの概要です。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: ffa4424dd3173cf6493ec219fe92925c970ad08c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258322"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューとは

Azure Time Series Insights プレビューは、エンド ツー エンドのサービスとしてのプラットフォーム (PaaS) のオファリングです。 豊富なコンテキスト情報を付加され、時系列に合わせて最適化された IoT スケールのデータの収集、処理、格納、分析、クエリに使用されます。 Time Series Insights は、アドホックなデータ探索と運用分析に最適です。 Time Series Insights は、産業用 IoT 展開の広範なニーズを満たす、個別に拡張可能なカスタマイズされたサービス オファリングです。

> [!TIP]
> 一般提供段階 (GA) の機能については、[Azure Time Series Insights GA の概要](time-series-insights-overview.md)に関するページを参照してください。

## <a name="video"></a>ビデオ

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Azure Time Series Insight プレビューの詳細を確認してください。 </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>IoT データを定義する

IoT データとは、アセットが集約された組織で利用できる産業用のデータです。 IoT データは、ノイズの多い測定を記録するアセットから送信されるため、極めて構造化されていないことがよくあります。 このような測定値としては、温度、モーション、湿度などがあります。 このようなデータ ストリームでは、大きなギャップ、破損したメッセージ、誤った読み取りといった特徴がよく見られます。 このようなストリームからのデータは、分析を行う前に、クリーンアップする必要があります。

IoT データは、CRM や ERP といったファースト パーティ ソースからの追加データ入力がある場合にのみ意味を持つことがよくあります。 天気や場所など、サード パーティのデータ ソースから入力されることもあります。

結果として、データの一部だけが、運用や業務の目的に利用されます。 このようなデータが、業務レポートおよび業務分析に対して、一貫性と包括性を備えた現時点での正しい情報を提供します。 収集された IoT データをアクションにつながる分析情報に変換するには、以下のことが必要です。

* 分析用にデータをクリーンアップ、フィルター、補間、変換、および準備するためのデータ処理。
* データ内を移動したりデータを理解したりするための、つまりデータを正規化したりコンテキスト化したりするための構造。
* 何十年分もの処理 (派生) されたデータと生データを長期または無期限に保持するためのコスト効率の高いストレージ。

IoT の一般的なデータ フローを次の図に示します。

  ![IoT データ フロー][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>産業用 IoT の Azure Time Series Insights

現在の IoT の状況は多様です。 お客様は、製造、自動車、エネルギー、公益、スマート ビルディング、コンサルティングなどの業界に広がっています。 シナリオには、データの形式がわからないアドホックなデータ探索が含まれます。 また、運用効率を高めるためにスキーマ化された (明示的にモデル化された) データの運用分析を行うシナリオも含まれます。 通常、これらのシナリオは共存しており、さまざまなユース ケースをサポートしています。 産業用 IoT 企業とそのデジタル革命を成功させる鍵となるプラットフォーム機能には次のようなものがあります。

- ウォームとコールド両方の多層ストレージ。
- 何十年分もの時系列データを格納する機能。
- アセット ベースの運用インテリジェンスのためにクエリを明示的にモデル化したり最適化したりする機能。

Time Series Insights は、IoT データ探索と運用分析情報のための、包括的なエンド ツー エンドの PaaS オファリングです。 Time Series Insights は IoT 規模のタイム シリーズ データを分析するための、完全に管理されたクラウド サービスを提供しています。

スキーマレスのメモリ内ストアに生データを格納できます。 分散クエリ エンジンと API を使用して、対話型のアドホック クエリを実行できます。 リッチなユーザー エクスペリエンスを利用して、1 秒間に何十億ものイベントを視覚化できます。 詳しくは、[データ探索機能](./time-series-insights-overview.md)に関するページをご覧ください。

Time Series Insights では、現在プレビュー段階である Operational Insights の機能も提供されます。 Time Series Insights を対話型のデータ探索および運用インテリジェンスと共に利用することで、IoT アセットから収集したデータからさらに価値を取り出すことができます。 プレビュー オファリングでは以下がサポートされています。

* スケーラブルで、パフォーマンスが高く、コストに最適化された時系列データ ストア。 このクラウドベースの IoT ソリューションでは、何年分もの時系列データの傾向を秒単位で示すことができます。
* アセットとデバイスからの派生シグナルと非派生シグナルに関連付けられたドメインおよびメタデータを表すセマンティック モデルのサポート。
* アセット ベースのデータ分析情報と豊富なアドホック データ分析を組み合わせて拡張されたユーザー エクスペリエンス。 この組み合わせにより、業務と運用のインテリジェンスが実現されます。
* 高度な機械学習および分析ツールとの統合。 Azure Databricks、Apache Spark、Azure Machine Learning、Jupyter notebooks、Power BI などのツールが含まれます。 これらのツールは、時系列データの課題に取り組んで運用効率を高めるのに役立ちます。

Operational Insights およびデータ探索は、データ処理、ストレージ、およびクエリに対する単純な従量課金制の価格モデルで提供されます。 この課金モデルは、変化するビジネス ニーズに適しています。

次の概要データ フロー図は更新を示したものです。

  ![主な機能][2]

これらの重要な産業用 IoT 機能が導入された Time Series Insights でには、以下のような重要な利点があります。

| | |
| ---| ---|
| IoT 規模の時系列データに対応する多層ストレージ | データ取り込み用の共通データ処理パイプラインでは、対話型クエリのためのウォーム ストレージにデータを格納できます。 また、大量のデータ用のコールド ストレージにデータを格納することもできます。 高パフォーマンスのアセット ベースの[クエリ](./time-series-insights-update-tsq.md)を利用します。 |
| 未加工のテレメトリをコンテキスト化してアセット ベースの分析情報を生成する時系列モデル | わかりやすい[時系列モデル](./time-series-insights-update-tsm.md)で未加工のテレメトリ データをコンテキスト化します。 高パフォーマンスでコスト最適化されたデバイス ベースのクエリを使って、充実した運用インテリジェンスを実現します。 |
| 他のデータ ソリューションとの円滑で連続的な統合 | Time Series Insights のデータは、オープンソース化された Apache Parquet ファイルに[格納](./time-series-insights-update-storage-ingress.md)されます。 ファースト パーティかサード パーティかにかかわらず、エンド ツー エンドのシナリオで他のデータ ソリューションと簡単に統合できます。 これらのシナリオには、ビジネス インテリジェンス、高度な機械学習、予測分析などが含まれます。 |
| ほぼリアルタイムのデータ探索 | [Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)のユーザー エクスペリエンスは、取り込みのパイプライン経由ですべてのデータ ストリーミングの視覚化を提供しています。 イベント ソースを接続するとすぐに、イベント データの表示、調査、クエリを行うことができます。 これにより、デバイスが想定どおりにデータを出力しているかどうかを検証できます。 IoT アセットの正常性、生産性、全体的な有効性を監視することもできます。 |
| 根本原因分析と異常検出 | [Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)では、多段階の根本原因分析を実行して保存するために、パターン ビューとパースペクティブ ビューの両方がサポートされています。 Azure Stream Analytics と組み合わせて Time Series Insights を使用すると、ほぼリアルタイムでアラートや異常を検出できます。 |
| Time Series Insights プラットフォーム上に構築されたカスタム アプリケーション | Time Series Insights では、[JavaScript SDK](./tutorial-explore-js-client-lib.md) がサポートされています。 SDK では、豊富なコントロールとクエリへの簡単なアクセスが提供されます。 SDK を使用し、特定のビジネス ニーズに合わせて、Time Series Insights 上にカスタム IoT アプリケーションを構築します。 また、Time Series Insights の [Query API](./time-series-insights-update-tsq.md) を直接使用して、カスタム IoT アプリケーションへのデータを生成できます。 |

## <a name="next-steps"></a>次の手順

Azure Time Series Insights プレビューの概要:

> [!div class="nextstepaction"]
> [クイック スタート ガイドを読む](./time-series-insights-update-quickstart.md)

ユース ケースの学習:

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのユース ケース](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview-one.png
[2]: media/v2-update-overview/overview-two.png
