---
title: Azure VPN Gateway からの診断ログ イベントにアラートを設定する
description: VPN Gateway 診断ログ イベントにアラートを構成する手順
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: c84d457c51f71bdf315bbbcec674ff1186dd905f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249010"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>VPN Gateway からの診断ログ イベントにアラートを設定する

この記事では、Azure Log Analytics を使用して、Azure VPN Gateway からの診断ログ イベントに基づいてアラートを設定する方法について説明します。 

Azure では、次のログを使用できます。

|***Name*** | ***説明*** |
|---        | ---               |
|GatewayDiagnosticLog | ゲートウェイ構成イベント、主要な変更、およびメンテナンス イベントの診断ログが含まれています |
|TunnelDiagnosticLog | トンネルの状態変更イベントが含まれています。 トンネルの接続/切断イベントには、状態変更の理由の概要があります (該当する場合) |
|RouteDiagnosticLog | ゲートウェイで発生する静的ルートへの変更および BGP イベントがログに記録されます |
|IKEDiagnosticLog | ゲートウェイ上の IKE コントロールのメッセージおよびイベントがログに記録されます |
|P2SDiagnosticLog | ゲートウェイ上のポイント対サイト コントロールのメッセージおよびイベントがログに記録されます |

## <a name="setup"></a>アラートを設定する

次の手順の例では、サイト対サイト VPN トンネルを含む切断イベントのアラートを作成します。


1. Azure portal の **[すべてのサービス]** で **[Log Analytics]** を検索し、 **[Log Analytics ワークスペース]** を選択します。

   ![Log Analytics ワークスペースに移動するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "作成")

2. **[Log Analytics]** ページで **[作成]** を選択します。

   ![[作成] ボタンがある log Analytics ページ](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "選択")

3. **[新規作成]** を選択し、詳細を入力します。

   ![Log Analytics ワークスペースを作成するための詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "選択")

4. **[モニター]**  >  **[診断設定]** ブレードで VPN ゲートウェイを見つけます。

   ![[診断設定] で VPN ゲートウェイを検索するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "選択")

5. 診断をオンにするには、ゲートウェイをダブルクリックし、 **[診断をオンにする]** を選択します。

   ![診断をオンにするための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "選択")

6. 詳細を入力し、確実に **[Log Analytics への送信]** と **[TunnelDiagnosticLog]** が選択されているようにします。 手順 3 で作成した Log Analytics ワークスペースを選択します。

   ![選択されたチェック ボックス](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "選択")

7. 仮想ネットワーク ゲートウェイ リソースの概要に移動し、 **[監視]** タブから **[アラート]** を選択します。次に新しいアラート ルールを作成するか、または既存のアラート ルールを編集します。

   ![新しいアラート ルールを作成するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選択")

   ![ポイント対サイト](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "選択")
8. Log Analytics ワークスペースとリソースを選択します。

   ![ワークスペースとリソースの選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "選択")

9. **[条件の追加]** で、シグナル ロジックとして **[カスタム ログ検索]** を選択します。

   ![カスタム ログ検索のための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "選択")

10. **[検索クエリ]** ボックスに次のクエリを入力します。 必要に応じて、<> 内の値を置き換えます。

    ```
    AzureDiagnostics |
      where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
      remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"
    ```

    しきい値を 0 に設定し、 **[完了]** を選択します。

    ![クエリの入力としきい値の選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "選択")

11. **[ルールの作成]** ページで、 **[アクション グループ]** セクションの **[新規作成]** を選択します。 詳細を入力して、 **[OK]** を選択します。

    ![新しいアクション グループの詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "選択")

12. **[ルールの作成]** ページで、 **[アクションをカスタマイズする]** に詳細を入力し、 **[アクション グループ名]** セクションに正しい名前が表示されていることを確認します。 **[アラート ルールの作成]** を選択して、ルールを作成します。

    ![ルールを作成するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "選択")

## <a name="next-steps"></a>次の手順

トンネル メトリックにアラートを構成するには、「[Set up alerts on VPN Gateway metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)」(VPN Gateway メトリックにアラートを設定する) を参照してください。
