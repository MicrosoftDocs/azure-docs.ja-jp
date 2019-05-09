---
title: リモート監視ソリューションでのシミュレートされたデバイスの動作 - Azure | Microsoft Docs
description: この記事では、JavaScript を使用して、リモート監視ソリューションでのシミュレートされたデバイスの動作を定義する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449522"
---
# <a name="implement-the-device-model-behavior"></a>デバイス モデルの動作の実装

記事「[デバイス モデル スキーマについて](iot-accelerators-remote-monitoring-device-schema.md)」では、シミュレートされたデバイス モデルを定義するスキーマについて説明しました。 この記事では、シミュレートされたデバイスの動作を実装する 2 種類の JavaScript ファイルに言及しました。

- **状態**: デバイスの内部状態を更新するために一定の間隔で実行される JavaScript ファイル。
- **メソッド**: ソリューションがデバイスに対するメソッドを呼び出したときに実行される JavaScript ファイル。

> [!NOTE]
> デバイス モデルの動作は、デバイス シミュレーション サービスでホストされているシミュレートされたデバイスに対してのみ有効です。 実際のデバイスを作成する場合は、「[デバイスをリモート監視ソリューション アクセラレータに接続する](iot-accelerators-connecting-devices.md)」を参照してください。

この記事では、次のことについて説明します。

>[!div class="checklist"]
> * シミュレートされたデバイスの状態を制御する
> * シミュレートされたデバイスがリモート監視ソリューションからのメソッド呼び出しに応答する方法を定義する
> * スクリプトをデバッグする

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>次の手順

この記事では、独自のシミュレートされたカスタム デバイス モデルの動作を定義する方法について説明しました。 この記事では、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * シミュレートされたデバイスの状態を制御する
> * シミュレートされたデバイスがリモート監視ソリューションからのメソッド呼び出しに応答する方法を定義する
> * スクリプトをデバッグする

シミュレートされたデバイスの動作を指定する方法について理解できたら、次のステップとして、[シミュレートされたデバイスを作成する](iot-accelerators-remote-monitoring-create-simulated-device.md)方法について学習することをお勧めします。

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
