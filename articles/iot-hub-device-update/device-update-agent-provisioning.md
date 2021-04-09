---
title: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097756"
---
# <a name="device-update-agent"></a>デバイス更新エージェント

IoT Hub 用デバイス更新では、イメージベースとパッケージベースの 2 つの形式の更新がサポートされています。 

* イメージ更新を使用すると、デバイスの最終状態の信頼性が高くなります。 通常、運用前環境と運用環境の間でイメージ更新の結果をレプリケートする方が簡単です。そうすれば、パッケージやその依存関係の場合と同様の課題が生じることがないからです。 アトミックな性質のため、A/B フェールオーバー モデルを簡単に導入することもできます。 
* パッケージベースの更新は、デバイス上の特定のコンポーネントまたはアプリケーションのみを変更するターゲット指定の更新となります。 このため、帯域幅の消費量が少なくなるほか、更新プログラムのダウンロードとインストールにかかる時間を短縮することができます。 通常、パッケージ更新プログラムを使用すると、更新プログラムを適用するときのデバイスのダウンタイムを短縮し、イメージ作成のオーバーヘッドを回避できます。 

デバイス更新エージェントをビルド、実行、変更する方法については、次のリンクをご覧ください。

## <a name="build-the-device-update-agent"></a>デバイス更新エージェントをビルドする

手順に従って、ソースからデバイス更新エージェントを[ビルド](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)します。

## <a name="run-the-device-update-agent"></a>デバイス更新エージェントを実行する

エージェントが正常にビルドされたら、エージェントを[実行](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)します。

## <a name="modifying-the-device-update-agent"></a>デバイス更新エージェントの変更

次に、エージェントをイメージに組み込むために必要な変更を行います。  ガイダンスについては、デバイス更新エージェントを[変更](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md)する方法をご覧ください。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

問題が発生した場合は、IoT Hub 用デバイス更新の[トラブルシューティング ガイド](troubleshoot-device-update.md)をご確認ください。考えられる問題を取り除き、Microsoft に提供する必要がある情報を収集するのに役に立ちます。

## <a name="next-steps"></a>次の手順

IoT Hub 用デバイス更新の簡単なデモについては、事前にビルドされている以下のイメージとバイナリを使用してください。  

[イメージ更新: Raspberry Pi 3 B+ 参照 Yocto イメージの使用を開始する](device-update-raspberry-pi.md)

[イメージ更新: Ubuntu (18.04 x64) シミュレーター参照エージェントの使用を開始する](device-update-simulator.md)

[パッケージ更新: Ubuntu Server 18.04 x64 パッケージ エージェントの使用を開始する](device-update-ubuntu-agent.md)

