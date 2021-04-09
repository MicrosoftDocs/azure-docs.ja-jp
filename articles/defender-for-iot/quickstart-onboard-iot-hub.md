---
title: 'クイックスタート: エージェントベースのソリューションに Defender for IoT をオンボードする'
description: このクイックスタートでは、Azure IoT Hub で Defender for IoT セキュリティ サービスをオンボードし、有効にする方法について説明します。
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: b795773ae0eb667cf7f53f4209b6476ba937b17f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780958"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>クイックスタート: エージェントベースのソリューションに Defender for IoT をオンボードする

この記事では、既存の IoT Hub 上で Defender for IoT サービスを有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」を参照してください。

Defender for IoT の IoT Hub を使用して、IoT セキュリティを管理できます。 IoT Hub にある管理ポータルでは、以下の操作を実行できます。 

- IoT Hub のセキュリティを管理します。

- IoT Hub テレメトリに基づいて、エージェントをインストールすることなく、IoT デバイスのセキュリティの基本的な管理を行う。 

- マイクロ エージェントに基づいて、IoT デバイスのセキュリティの高度な管理を行う。

> [!NOTE]
> Defender for IoT では、現在、Standard レベルの IoT Hub だけがサポートされています。

## <a name="prerequisites"></a>前提条件

なし

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Defender for IoT を IoT ハブにオンボードする

すべての新しい IoT hub に対して、Defender for IoT は既定で **[有効]** に設定されます。 IoT Hub の作成プロセス中に Defender for IoT が **[有効]** に切り替えられることを確認できます。

トグルが **[有効]** に設定されていることを確認するには、以下の操作を行います。

1. Azure Portal に移動します。

1. Azure サービスの一覧から、 **[IoT Hub]** を選択します。

1. **［作成］** を選択します

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="上部のツール バーで作成ボタンを選択します。" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. **[管理]** タブを選択し、 **[Defender for IoT]** トグルが **[有効]** に設定されていることを確認します。

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Defender for IoT のトグルが有効に設定されていることを確認します。":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Defender for IoT を既存の IoT Hub にオンボードする

Defender for IoT を既存の IoT ハブにオンボードして、デバイス ID 管理、デバイスからクラウドおよびクラウドからデバイスへの通信パターンを監視できます。

Defender for IoT を既存の IoT ハブにオンボードするには:

1. [IoT Hub] に移動します。 

1. オンボードする IoT ハブを選択します。

1. **[セキュリティ]** セクションで任意のオプションを選択します。

1.  **[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\)**   をクリックし、オンボード フォームに記入します。 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="ソリューションを保護するには、[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\) ボタンを選択します。":::

**[Secure your IoT solution]\(IoT ソリューションのセキュリティ保護\)** ボタンは、IoT ハブがまだオンボードされていない場合、またはオンボード中に [Defender for IoT] トグルを **[オフ]** のままにしていた場合にのみ表示されます。

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="オンボード中にトグルが [オフ] に設定されていた場合。":::

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションを構成してください。

> [!div class="nextstepaction"]
> [Defender for IoT マイクロ エージェントのモジュール ツインを作成する (プレビュー)](quickstart-create-micro-agent-module-twin.md)
