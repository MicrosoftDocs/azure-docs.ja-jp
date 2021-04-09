---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050488"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge モジュールをデプロイする

モジュールを IoT Edge デバイスにデプロイするにはいくつかの方法があり、そのうちどれでも Azure Event Grid on IoT Edge で使用できます。 この記事では、Azure portal で Event Grid on IoT Edge をデプロイする手順について説明します。

>[!NOTE]
> このチュートリアルでは、Event Grid モジュールを永続化せずにデプロイします。 つまり、このチュートリアルで作成するすべてのトピックとサブスクリプションは、モジュールを再デプロイすると削除されます。 永続化を設定する方法の詳細については、次の記事を参照してください。「[Linux で状態を永続化する](../articles/event-grid/edge/persist-state-linux.md)」または「[Windows で状態を永続化する](../articles/event-grid/edge/persist-state-windows.md)」。 運用環境のワークロードの場合は、永続化を使用して Event Grid モジュールをインストールすることをお勧めします。

>[!IMPORTANT]
> このチュートリアルでは、クライアント認証が無効になっている Event Grid モジュールをデプロイし、HTTP サブスクライバーを許可します。 運用環境のワークロードについては、HTTPS 要求のみを許可し、サブスクライバーではクライアント認証を有効にすることをお勧めします。 Event Grid モジュールを安全に構成する方法の詳細については、「[セキュリティと認証](../articles/event-grid/edge/security-authentication.md)」を参照してください。
 
### <a name="select-your-iot-edge-device"></a>IoT Edge デバイスを選択する

1. [Azure ポータル](https://portal.azure.com)
1. IoT Hub に移動します。
1. **[デバイスの自動管理]** セクションのメニューで **[IoT Edge]** を選択します。 
1. デバイスの一覧でターゲット デバイスの ID をクリックします
1. **[Set Modules] \(モジュールの設定)** を選択します。 ページは開いたままにしておきます。 引き続き次のセクションの手順で使用します。

### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。  3 つのステップがあります。**モジュールの追加**、**ルートの指定**、および **デプロイの確認**。

### <a name="add-modules"></a>モジュールを追加する

1. **[デプロイ モジュール]** セクションで、 **[追加]** を選択します
1. モジュールの種類のドロップダウン リストから、 **[IoT Edge モジュール]** を選択します
1. コンテナーの名前、イメージ、およびコンテナー作成オプションを指定します。

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **[名前]** : eventgridmodule
   * **[イメージの URI]** : `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **[コンテナーの作成オプション]** :

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. **[保存]**
 1. **[次へ]** をクリックして、ルートのセクションに進みます

    > [!NOTE]
    > Azure VM をエッジ デバイスとして使用している場合は、ポート 4438 で受信トラフィックを許可する受信ポート規則を追加します。 規則を追加する手順については、[VM に対してポートを開く方法](../articles/virtual-machines/windows/nsg-quickstart-portal.md)に関するページを参照してください。


### <a name="setup-routes"></a>ルートを設定する

 既定のルートを保持し、 **[次へ]** を選択して確認のセクションに進みます

### <a name="review-deployment"></a>デプロイを確認する

1. 確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 一覧に次の 2 つのモジュールが表示されていることを確認します: **$edgeAgent** と **$edgeHub**。 これらの 2 つのモジュールは、IoT Edge ランタイムを構成し、すべてのデプロイの既定値として使用されます。
1. デプロイ情報を確認し、 **[送信]** を選択します。

### <a name="verify-your-deployment"></a>デプロイを確認する

1. デプロイを送信すると、自分の IoT ハブの IoT Edge ページに戻ります。
1. デプロイで対象にした **IoT Edge デバイス** を選択して、その詳細を開きます。
1. デバイスの詳細で、Event Grid モジュールが **[デプロイで指定]** および **[デバイス別に報告]** の両方として一覧表示されていることを確認します。

モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。