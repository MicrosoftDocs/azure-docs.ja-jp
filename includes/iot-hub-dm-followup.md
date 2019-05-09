---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56740862"
---
## <a name="customize-and-extend-the-device-management-actions"></a>デバイス管理操作のカスタマイズと拡張を行う

IoT ソリューションでは、定義された一連のデバイス管理パターンを拡張したり、デバイス ツインと cloud-to-device メソッド プリミティブを使用してカスタム パターンを作成したりできます。 デバイス管理操作の他の例には、出荷時の設定への復帰、ファームウェアの更新、ソフトウェアの更新、電源管理、ネットワークと接続の管理、データの暗号化などがあります。

## <a name="device-maintenance-windows"></a>デバイスのメンテナンス期間

通常、デバイスは、中断やダウンタイムを最小限に抑えることができる時間に操作を実行するように構成します。 デバイスのメンテナンス期間は、デバイスがその構成を更新する必要がある時間を定義する一般的に使用されるパターンです。 バックエンド ソリューションは、デバイス ツインの必要なプロパティを使用して、メンテナンス期間を可能にするデバイスのポリシーを定義してアクティブにすることができます。 デバイスは、メンテナンス期間ポリシーを受信したときに、デバイス ツインの報告されたプロパティを使用してポリシーの状態を報告することができます。 その後、バックエンド アプリケーションは、デバイス ツインのクエリを使用して、デバイスが各ポリシーに対応していることを確認できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ダイレクト メソッドを使用して、デバイスのリモート再起動をトリガーしました。 報告されるプロパティを使用してデバイスの最後の再起動時間を報告し、デバイス ツインのクエリを実行してクラウドからデバイスの最後の再起動時間を検出しました。

IoT Hub およびリモートによるファームウェアのワイヤレス更新などの他のデバイス管理パターンを確認するには、[ファームウェアの更新方法](../articles/iot-hub/tutorial-firmware-update.md)に関するページを参照してください

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、[ジョブのスケジュールとブロードキャスト](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)に関するページを参照してください。