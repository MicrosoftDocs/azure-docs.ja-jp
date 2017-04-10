---
title: "予測的なメンテナンスのチュートリアル | Microsoft Docs"
description: "Azure IoT の予測的なメンテナンスの構成済みソリューションのチュートリアルです。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 719f9810abb12cbe8645651d35cbff936cecd158
ms.openlocfilehash: 57531b609b095359c8bf0ea3685af40124f46311
ms.lasthandoff: 02/01/2017


---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>予測的なメンテナンスの構成済みソリューションのチュートリアル

## <a name="introduction"></a>はじめに

IoT Suite の予測的なメンテナンスの構成済みソリューションは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。 この構成済みソリューションを使用すると、メンテナンスの最適化などのアクティビティを先手を打って実行できます。 このソリューションは、IoT Hub、Stream Analytics、[Azure Machine Learning][lnk-machine-learning] ワークスペースなどの主要な Azure IoT Suite サービスを組み合わせたものです。 このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測するモデルが含まれています。 このソリューションでは、固有のビジネス要件を満たすソリューションを計画および実装するための開始地点として使用できる、IoT ビジネス シナリオが完全に実装されています。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![][img-architecture]

青色の項目は、構成済みのソリューションをプロビジョニングするために選択したリージョンに、プロビジョニングされている Azure サービスです。 構成済みソリューションをデプロイできるリージョンの一覧は、[プロビジョニング ページ][lnk-azureiotsuite]に表示されます。

緑色の項目は、シミュレートされている航空機エンジン デバイスです。 これらのシミュレートされているデバイスの詳細については、次のセクションを参照してください。

灰色の項目は、"*デバイスの管理*" 機能を実装しているコンポーネントを示します。 予測的なメンテナンスの構成済みソリューションの現在のリリースでは、これらのリソースはプロビジョニングできません。 デバイスの管理の詳細については、[リモート監視の事前構成済みソリューション][lnk-remote-monitoring]に関するページを参照してください。

## <a name="simulated-devices"></a>シミュレートされたデバイス

構成済みのソリューションでは、航空機エンジン デバイスをシミュレーションしています。 このソリューションでは、1 台の航空機にマッピングされる&2; つのエンジンをプロビジョニングしています。 各エンジンは、4 種類のテレメトリを出力します。センサー 9、センサー 11、センサー 14、センサー 15 は、Machine Learning モデルがそのエンジンの RUL を計算するために必要なデータを提供します。 シミュレートされたデバイスはそれぞれ、次のテレメトリ メッセージを IoT Hub に送信します。

"*サイクル数*"。 サイクルは、2 ～&10; 時間の期間で完了したフライトを表します。 フライト中、テレメトリ データは&30; 分ごとにキャプチャされます。

*テレメトリ*。 エンジン属性を示すセンサーは&4; つあります。 センサーは総称的に、センサー 9、センサー 11、センサー 14、およびセンサー 15 とラベル付けされています。 これらの&4; つのセンサーは、RUL モデルから有用な結果を得るために十分なテレメトリを表します。 構成済みソリューションで使用されるモデルは、実際のエンジンのセンサー データなど、公開されているデータ セットから作成されています。 元のデータ セットからこのモデルを作成する方法の詳細については、「[Cortana Intelligence Gallery Predictive Maintenance Template (Cortana Intelligence ギャラリーの予測的なメンテナンス テンプレート)][lnk-cortana-analytics]」を参照してください。

シミュレーション デバイスは、ソリューションの IoT Hub から送信された次のコマンドを処理することができます。

| コマンド | Description |
| --- | --- |
| StartTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を開始します。 |
| StopTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を停止します。 |

IoT Hub は、デバイスのコマンドの受信確認を渡します。

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics ジョブ
**ジョブ: テレメトリ** は、2 つのステートメントを使用して、デバイスのテレメトリの受信ストリームに対して動作します。 1 つ目では、デバイスからのすべてのテレメトリを選択し、Web アプリで視覚化されるデータが格納される Blob Storage に送信します。 2 つ目のステートメントは&2; 分間のスライディング ウィンドウに渡る平均センサー値を計算し、このデータを Event Hub を介して **イベント プロセッサ**に送信します。

## <a name="event-processor"></a>イベント プロセッサ
**イベント プロセッサ ホスト**は、Azure Web Job で実行されます。 **イベント プロセッサ** は、完了したサイクルの平均センサー値を受け取ります。 それらの値は、トレーニングされたモデルを公開する API に渡され、エンジンの RUL が計算されます。 この API は、ソリューションの一部としてプロビジョニングされた Machine Learning ワークスペースによって公開されます。

## <a name="machine-learning"></a>Machine Learning
Machine Learning コンポーネントは、実際の航空機エンジンから収集されたデータから派生したモデルを使用します。 この Machine Learning ワークスペースには、プロビジョニングしたソリューションの状態が**準備完了**である場合、そのソリューションの [azureiotsuite.com][lnk-azureiotsuite] ページのタイルから移動することができます。


## <a name="next-steps"></a>次のステップ
これで予測的なメンテナンスの構成済みソリューションの主要なコンポーネントを見終わったので、それをカスタマイズすることができます。 [構成済みソリューションのカスタマイズのガイダンス][lnk-customize]に関するページを参照してください。

IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

* [IoT スイートに関してよく寄せられる質問][lnk-faq]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
