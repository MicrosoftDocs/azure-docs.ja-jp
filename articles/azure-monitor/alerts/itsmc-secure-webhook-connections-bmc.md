---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート - BMC を使用した構成
description: この記事では、ITSM 製品またはサービスを Azure Monitor のセキュア エクスポートで BMC に接続する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041707"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix を Azure Monitor に接続する

以降のセクションでは、BMC Helix 製品と Azure のセキュア エクスポートを接続する方法について詳細に説明します。

## <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認します。

* Azure AD が登録されている。
* サポートされているバージョンの BMC Helix Multi-Cloud Service Management (バージョン 19.08 以降) がある。

## <a name="configure-the-bmc-helix-connection"></a>BMC Helix の接続を構成する

1. セキュア エクスポートの URI を取得するために、BMC Helix 環境で次の手順を使用します。

   1. Integration Studio にログインします。
   1. **[Create Incident from Azure Alerts]** フローを探します。
   1. Webhook URL をコピーします。
   
   ![Integration Studio の Webhook URL のスクリーンショット。](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. バージョンに応じた手順に従ってください。
   * [Azure Monitor バージョン 20.02 との事前構築済み統合の有効化](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * [Azure Monitor バージョン 19.11 との事前構築済み統合の有効化](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. BMC Helix での接続の構成の一部として、統合 BMC インスタンスにアクセスし、次の手順に従います。

   1. **[カタログ]** を選択します。
   2. **[Azure アラート]** を選択します。
   3. **[コネクタ]** を選択します。
   4. **[構成]** を選択します。
   5. **[新しい接続を追加]** 構成を選択します。
   6. 構成セクションの情報を入力します。
      - **Name**:名前を指定します。
      - **承認の種類**:**NONE**
      - **説明**:名前を指定します。
      - **サイト**:**Cloud**
      - **インスタンスの数**:**2** (既定値)。
      - **チェック**:既定では、使用が有効になるように選択されています。
      - Azure テナント ID と Azure アプリケーション ID は、前に定義したアプリケーションから取得されます。

![BMC の構成を示すスクリーンショット。](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
