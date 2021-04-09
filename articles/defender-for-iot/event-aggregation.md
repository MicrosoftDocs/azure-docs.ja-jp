---
title: Defender-IoT-micro-agent クラシック イベントの集計
description: Defender for IoT のイベント集計について説明します。
ms.topic: conceptual
ms.date: 3/23/2021
ms.openlocfilehash: e6c0f0e6e1f4027716ff8e3ca99f1c8803f33260
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779139"
---
# <a name="defender-iot-micro-agent-classic-event-aggregation"></a>Defender-IoT-micro-agent クラシック イベントの集計

Defender for IoT セキュリティ エージェントにより、ローカル デバイスからデータとシステム イベントを収集し、処理と分析を行うためにこのデータを Azure クラウドに送信します。 セキュリティ エージェントは、新しいプロセスや新しい接続イベントなど、さまざまな種類のデバイス イベントを収集します。 新しいプロセスと新しい接続のイベントは両方とも、正当に、1 秒以内にデバイス上で頻繁に発生することがあります。堅牢で包括的なセキュリティのために重要ですが、これによってセキュリティ エージェントが送信を強制されるメッセージの量は、IoT Hub のクォータとコストの制限にすぐに達する、またはそれを超える可能性があります。 ただし、これらのイベントには、デバイスの保護に不可欠な重要なセキュリティ情報が含まれています。

デバイスを保護しながら、追加のクォータとコストを削減するため、Defender for IoT エージェントによりこれらの種類のイベントが集計されます。

イベント集計は、既定で **オン** になっており、推奨はされませんが、手動でいつでも **オフ** にすることができます。

集計は、現在、次の種類のイベントで使用できます。

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (Windows のみ)

## <a name="how-does-event-aggregation-work"></a>イベント集計のしくみ

イベント集計が **オン** のままの場合、Defender for IoT エージェントにより、間隔の期間または時間枠についてイベントが集計されます。
間隔の期間が経過すると、エージェントは、詳細な分析のために、集計されたイベントを Azure クラウドに送信します。
集計されたイベントは、Azure クラウドに送信されるまでメモリに格納されます。

エージェントのメモリ占有領域を減らすために、エージェントは、既にメモリに保持されているのと同じイベントを収集するたびに、この特定のイベントのヒット カウントを増やします。 集計の時間枠が経過すると、エージェントは、発生したそれぞれ特定の種類のイベントのヒット カウントを送信します。 イベント集計は、単に、収集された各種類のイベントのヒット カウントの集計です。

イベントは、次の条件が満たされている場合にのみ同一と見なされます。

* ProcessCreate イベント - **コマンドライン**、**実行可能ファイル**、**ユーザー名**、および **ユーザー ID** が同一である
* ConnectionCreate イベント - **コマンドライン**、**ユーザー ID**、**方向**、**ローカル アドレス**、**リモート アドレス**、**プロトコル**、および **宛先ポート** が同一である。
* ProcessTerminate イベント - **実行可能ファイル** と **終了状態** が同一である

### <a name="working-with-aggregated-events"></a>集計されたイベントの操作

集計中、集計されていないイベント プロパティは破棄され、値 0 でログ分析に表示されます。

* ProcessCreate イベント - **processId** と **parentProcessId** は 0 に設定される
* ConnectionCreate イベント - **processId** と **発信元ポート** は 0 に設定される

## <a name="event-aggregation-based-alerts"></a>イベント集計ベースのアラート

分析後、Defender for IoT により、疑わしい集計イベントに対するセキュリティ アラートが作成されます。 集計イベントから作成されたアラートは、集計されたイベントごとに 1 回だけ表示されます。

各イベントの集計の開始時刻、終了時刻、およびヒット カウントは、Log Analytics 内のイベント **ExtraDetails** フィールドに記録されて調査時に使用されます。

各集計イベントは、収集されたアラートの 24 時間分を表します。 各イベントの左上にある [イベント オプション] メニューを使用すると、集計された個々のイベントを **無視** できます。

## <a name="event-aggregation-twin-configuration"></a>イベント集計のツイン構成

**azureiotsecurity** モジュールのモジュール ツイン ID の [エージェント構成オブジェクト](how-to-agent-configuration.md)内で、Defender for IoT のイベント集計の構成を変更します。

| 構成名 | 指定できる値 | 詳細 | 解説 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | プロセス作成イベントのイベント集計を有効/無効にします |
| aggregationIntervalProcessCreate | ISO8601 Timespan 文字列 | プロセス作成イベントの集計間隔 |
| aggregationEnabledConnectionCreate | boolean| 接続作成イベントのイベント集計を有効/無効にします |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 文字列 | 接続作成イベントの集計間隔 |
| aggregationEnabledProcessTerminate | boolean | プロセス終了イベントのイベント集計を有効/無効にします | Windows のみ|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 文字列 | プロセス終了イベントの集計間隔 | Windows のみ|
|

## <a name="default-configurations-settings"></a>既定の構成設定

| 構成名 | 既定値 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>次のステップ

この記事では、Defender for IoT セキュリティ エージェントと、使用可能なイベント構成オプションについて説明しました。

Defender for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)を理解する
- [セキュリティ エージェント](how-to-deploy-agent.md)を選択してデプロイする
- Defender for IoT の[システムの要件](quickstart-system-prerequisites.md)を確認する
- [IoT Hub で Defender for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [Defender for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスの詳細について確認する
