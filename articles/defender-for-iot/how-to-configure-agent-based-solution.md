---
title: Azure Defender for IoT エージェントベースのソリューションを構成する
description: Azure Defender for IoT エージェントベースのソリューションでデータ収集を構成する方法について説明します
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 9a21b336299438b89fae8d5a837130762a7f36e8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784392"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Azure Defender for IoT エージェントベースのソリューションを構成する  

この記事では、Azure Defender for IoT エージェントベースのソリューションでデータ収集を構成する方法について説明します。

## <a name="configure-data-collection"></a>データ収集を構成する

Azure Defender for IoT エージェントベースのソリューションでデータ収集を構成するには、以下の手順を実行します。 

1. Azure portal に移動し、Defender for IoT がアタッチされている IoT Hub を選択します。 

1.  **[セキュリティ]**   メニューで、 **[設定]** を選択します。 

1.  **[データ収集]** を選択します。 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="[セキュリティ] メニューの [設定] から [データ収集] を選択します。":::

## <a name="geolocation-and-ip-address-handling"></a>位置情報と IP アドレスの処理 

IoT ソリューションのセキュリティを確保するために、IoT デバイス、IoT Edge、IoT Hub の受信接続と送信接続の IP アドレスは既定で収集され、保存されます。 この情報は必須で、疑わしい IP アドレス ソースからの異常な接続を検出するために使用されます。 たとえば、既知のボットネットの IP アドレス ソースやご利用の位置情報とは異なる外部の IP アドレス ソースから接続を確立しようとするケースが考えられます。 Defender for IoT サービスでは、一連の IP アドレス データをいつでも柔軟に有効にしたり無効にしたりすることができます。 

一連の IP アドレス データを有効または無効にするには、次の手順に従います。 

1. ご利用の IoT Hub を開き、 **[セキュリティ]**   メニューから  **[設定]**   メニューを選択します。 

1.  **[データ収集]**   画面を選択し、必要に応じて位置情報と IP アドレスの処理の設定を変更します。 

## <a name="log-analytics-creation"></a>Log Analytics の作成 

Defender for IoT を使用すると、セキュリティ アラート、推奨事項、および未加工のセキュリティ データを Log Analytics ワークスペースに格納することができます。 IoT Hub での Log Analytics インジェストは、Defender for IoT ソリューションでは既定で **[オフ]** に設定されています。 Defender for IoT を Log Analytics ワークスペースにアタッチし、セキュリティ データを保存することもできます。 

Defender for IoT によって、既定で次の 2 種類の情報が Log Analytics ワークスペースに保存されます。
 
- セキュリティ アラート

- 推薦事項 

追加の情報の種類のストレージを `raw events` として追加することもできます。 

> [!Note] 
> Log Analytics に  `raw events`  を格納すると、追加のストレージ コストがかかります。 

マイクロ エージェントで使用できるよう Log Analytics を有効にするには、以下の手順を実行します。 

1. **[ワークスペースの構成]**  >  **[データ収集]** に移動し、トグルを  **[オン]** に切り替えます。 

1. 新しい Log Analytics ワークスペースを作成するか、既存のものをアタッチします。 

1.  **[Access to raw security data]\(生セキュリティ データにアクセスする\)**   オプションが選択されていることを確認します。  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="[Access to raw security data]\(生セキュリティ データにアクセスする\) が選択されていることを確認します。":::

1.  **[保存]** を選択します。

毎月 Azure Log Analytics サービスに取り込まれるデータのうち、お客様 1 人あたり最初の 5 GB は無料です。 Azure Log Analytics ワークスペースに取り込まれたすべてのデータ (GB) は、最初の 31 日間無料で保持されます。 価格の詳細については、[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページを参照してください。 

Log Analytics のワークスペースの構成を変更するには、次の手順に従います。 

1. IoT Hub の **[セキュリティ]** メニューで、 **[設定]** を選択します。 

1.  **[データ収集]**   画面を選択し、Log Analytics 設定のワークスペース構成を必要に応じて変更します。 

構成後に Log Analytics ワークスペースのアラートにアクセスするには:

1. Defender for IoT でアラートを選択します。

1. **[Investigate alerts in Log Analytics workspace]\(Log Analytics ワークスペースでアラートを調査する\)** を選択します。

構成後に Log Analytics ワークスペースのアラートにアクセスするには:

1. Defender for IoT で推奨事項を選択します。

1. **[Investigate recommendations in Log Analytics workspace]\(Log Analytics ワークスペースで推奨事項を調査\)** を選択します。 
 
Log Analytics からデータのクエリを実行する方法の詳細については、 [Log Analytics のクエリの概要](../azure-monitor/logs/get-started-queries.md)に関する記事を参照してください。 

## <a name="turn-off-defender-for-iot"></a>Defender for IoT を無効にする 

特定の IoT Hub に対して Defender for IoT サービスを有効または無効にするには、次の手順に従います。 

1. IoT Hub の **[セキュリティ]** メニューで、 **[設定]** を選択します。

1.  **[データ収集]**   画面を選択し、Log Analytics 設定のワークスペース構成を必要に応じて変更します。

## <a name="next-steps"></a>次のステップ 

次の記事に進んで、[ソリューションを構成](quickstart-configure-your-solution.md)してください。