---
title: エージェント ベースの推奨事項
titleSuffix: Azure Defender for IoT
description: セキュリティに関する推奨事項の概念と Defender for IoT デバイスでの使用方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100641028"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT デバイスのセキュリティに関する推奨事項

Defender for IoT では、Azure リソースと IoT デバイスがスキャンされ、攻撃対象領域を減らすためのセキュリティに関する推奨事項が提供されます。
セキュリティに関する推奨事項は実践的で、顧客がセキュリティに関するベスト プラクティスに準拠できるよう支援することを目的にしています。

この記事では、IoT デバイスに関してトリガーできる推奨事項の一覧を示します。

## <a name="agent-based-recommendations"></a>エージェント ベースの推奨事項

デバイスに関する推奨事項は、デバイスのセキュリティ体制を改善するための洞察および提案を提供します。

| 重大度 | Name | Data Source | 説明 |
|--|--|--|--|
| Medium | デバイスで開いているポート | クラシック セキュリティ モジュール | リッスン中のエンドポイントがデバイス上に見つかりました。 |
| Medium | チェーンのいずれかで制限の少なすぎるファイアウォール ポリシーが見つかりました。 | クラシック セキュリティ モジュール | 許可のファイアウォール ポリシーが見つかりました (入力/出力)。 ファイアウォール ポリシーは既定ではすべてのトラフィックを拒否しなければならず、デバイスとの間の必要な通信を許可するにはルールを定義する必要があります。 |
| Medium | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | クラシック セキュリティ モジュール | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました。 |
| Medium | 出力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | クラシック セキュリティ モジュール | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました。 |
| Medium | オペレーティング システムのベースライン検証に失敗しました | クラシック セキュリティ モジュール | デバイスが [CIS Linux ベンチマーク](https://www.cisecurity.org/cis-benchmarks/)に準拠していません。 |

### <a name="agent-based-operational-recommendations"></a>エージェント ベースの運用に関する推奨事項

運用に関する推奨事項は、セキュリティ エージェントの構成を改善するための洞察および提案を提供します。

| 重大度 | Name | Data Source | 説明 |
|--|--|--|--|
| 低 | エージェントが未使用のメッセージを送信します | クラシック セキュリティ モジュール | 過去 24 時間の 10% 以上のセキュリティ メッセージが 4 KB 未満でした。 |
| 低 | セキュリティ ツインの構成が最適ではありません | クラシック セキュリティ モジュール | セキュリティ ツインの構成が最適ではありません。 |
| 低 | セキュリティ ツインの構成に競合があります | クラシック セキュリティ モジュール | セキュリティ ツインの構成で競合が検出されました。 |  |

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)
- [セキュリティ データにアクセスする](how-to-security-data-access.md)方法を学習ぶ
- [デバイスの調査](how-to-investigate-device.md)について学ぶ
