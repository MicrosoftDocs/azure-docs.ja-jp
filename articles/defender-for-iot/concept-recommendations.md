---
title: IoT Hub のセキュリティに関する推奨事項
description: セキュリティに関する推奨事項の概念と Defender for IoT Hub での使用方法について説明します。
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779343"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub のセキュリティに関する推奨事項

Defender for IoT では、Azure リソースと IoT デバイスがスキャンされ、攻撃対象領域を減らすためのセキュリティに関する推奨事項が提供されます。
セキュリティに関する推奨事項は実践的で、顧客がセキュリティに関するベスト プラクティスに準拠できるよう支援することを目的にしています。

この記事では、IoT Hub に関してトリガーできる推奨事項の一覧を示します。

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub の組み込みの推奨事項

推奨事項のアラートは、ご利用の環境のセキュリティ体制を改善するためのアクションの分析情報と推奨事項を示します。

| 重大度 | Name | Data Source | 説明 |
|--|--|--|--|
| 高 | 複数のデバイスで同一の認証資格情報が使われています | IoT Hub | IoT Hub 認証資格情報が複数のデバイスで使われています。 このプロセスは、不正なデバイスが正当なデバイスを偽装していることを示している場合があります。 資格情報を重複して使用すると、悪意のあるアクターによるデバイス偽装のリスクが高くなります。 |
| Medium | 既定の IP フィルター ポリシーを拒否にする必要があります | IoT Hub | 許可されるトラフィックを定義するルールを IP フィルター構成に含めて、それ以外のトラフィックはすべて既定で拒否する必要があります。 |
| Medium | IP フィルター ルールに含まれている IP 範囲が大きい | IoT Hub | IP 許可フィルター ルールのソース IP 範囲が大きすぎます。 過度に制限の少ないルールでは、ご使用の IoT ハブが悪意のあるアクターに公開されてしまう可能性があります。 |
| 低 | IoT Hub で診断ログを有効にする | IoT Hub | ログを有効にし、それらを最大 1 年間保持します。 ログを保持することで、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)
- [セキュリティ データにアクセスする](how-to-security-data-access.md)方法を学習ぶ
- [デバイスの調査](how-to-investigate-device.md)について学ぶ
