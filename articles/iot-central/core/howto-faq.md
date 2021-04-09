---
title: Azure IoT Central についてよく寄せられる質問 | Microsoft Docs
description: Azure IoT Central についてよく寄せられる質問 (FAQ) と回答
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796042"
---
# <a name="frequently-asked-questions-for-iot-central"></a>IoT Central についてよく寄せられる質問

**デバイスが IoT Central アプリケーションに接続していない場合、資格情報の問題がないかどうかを確認するにはどうすればよいですか。**

「[デバイスからのデータが Azure IoT Central で表示されない原因を解決する](troubleshoot-connection.md)」には、デバイスの接続の問題を診断する手順が記載されています。

**カスタマー サポートにチケットを提出するにはどうすればよいですか?**

ヘルプが必要な場合は、[Azure サポート チケット](https://portal.azure.com/#create/Microsoft.Support)を提出できます。

その他のサポート オプションなど、詳細については、[Azure IoT のサポートとヘルプのオプション](../../iot-fundamentals/iot-support-help.md)に関するページを参照してください。

**デバイスのブロックを解除するにはどうすればよいですか?**

デバイスがブロックされると、そのデバイスは IoT Central アプリケーションにデータを送信できません。 ブロックされたデバイスは、アプリケーションの **[デバイス]** ページで "**ブロック**" という状態です。 データの送信を再開するには、まずオペレーターがデバイスのロックを解除する必要があります。

:::image type="content" source="media/howto-faq/blocked.png" alt-text="ブロックされたデバイスを示すスクリーンショット":::

オペレーターがデバイスのブロックを解除すると、状態は前の値 ( **[登録済み]** または **[プロビジョニング済み]** ) に戻ります。

**デバイスを承認するにはどうすればよいですか?**

**[デバイス]** ページのデバイスの状態が **[承認を待っています]** の場合は、 **[自動承認]** オプションが無効になっていることを意味します。

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="自動承認オプションが無効になっているアプリケーションを示すスクリーンショット。":::

デバイスがデータの送信を開始する前に、オペレーターが明示的にデバイスを承認する必要があります。 **[Devices]\(デバイス\)** ページで手動で登録されていないが、有効な資格情報を使って接続されているデバイスの場合、デバイスの状態が **[Waiting for Approval]\(承認待ち\)** になります。 オペレーターは、 **[デバイス]** ページから **[承認]** ボタンを使用してこれらのデバイスを承認できます。

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="デバイスを承認する方法を示すスクリーンショット":::

**デバイスをデバイス テンプレートに関連付けるにはどうすればよいですか?**

デバイスの状態が **[関連付けなし]** の場合は、IoT Central に接続しているデバイスにデバイス テンプレートが関連付けられていないことを意味します。 この状況は通常、次のシナリオで発生します。

- デバイス テンプレートを指定せずに、 **[デバイス]** ページで **[インポート]** を使用して一連のデバイスが追加された。
- デバイス テンプレートを指定せずに **[デバイス]** ページから手動でデバイスが登録された。 その後、有効な資格情報を使用してデバイスが接続された。  

オペレーターは、 **[デバイス]** ページから **[移行]** ボタンを使用して、デバイス テンプレートにデバイスを関連付けることができます。 詳細については、[「Azure IoT Central アプリケーションでデバイスを管理する」のデバイスのテンプレートへの移行](howto-manage-devices.md)に関するセクションを参照してください。

**IoT Hub の詳細についてはどこで確認できますか?**

Azure IoT Central は、デバイス接続を可能にするクラウド ゲートウェイとして Azure IoT Hub を使用します。 IoT Hub では、次のことが可能になります。

- クラウド内での大規模なデータ インジェスト。
- デバイスの管理。
- セキュリティ保護されたデバイス接続。

IoT Hub の詳細については、[Azure IoT Hub](../../iot-hub/index.yml) に関するページを参照してください。

**Device Provisioning Service (DPS) の詳細についてはどこで確認できますか?**

Azure IoT Central から DPS を使用すると、デバイスをアプリケーションに接続できるようになります。 デバイスを IoT Central に接続する際に DPS が果たす役割の詳細については、「[Azure IoT Central に接続する](concepts-get-connected.md)」を参照してください。 DPS の詳細については、「[Azure IoT Hub Device Provisioning Service を使用したデバイスのプロビジョニング](../../iot-dps/about-iot-dps.md)」を参照してください。