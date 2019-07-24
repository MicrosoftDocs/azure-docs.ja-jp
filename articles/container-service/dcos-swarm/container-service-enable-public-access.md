---
title: (非推奨) Azure DC/OS コンテナー アプリへのアクセスの有効化
description: Azure Container Service の DC/OS コンテナーへのパブリック アクセスを有効にする方法。
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996595"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(非推奨) Azure Container Service アプリケーションへのパブリック アクセスを有効にする

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

ACS [パブリック エージェント プール](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) のすべての DC/OS コンテナーは、自動的にインターネットに公開されます。 既定では、ポート **80**、**443**、**8080** が開かれ、それらのポートでリッスンしているすべての (パブリック) コンテナーにアクセスできます。 この記事では、Azure Container Service のアプリケーションのために、さらに多くのポートを開く方法について説明します。

## <a name="open-a-port-portal"></a>ポートを開く (ポータル)
まず、必要なポートを開く必要があります。

1. ポータルにログインします。
2. Azure Container Service をデプロイしたリソース グループを見つけます。
3. エージェント ロード バランサー ( **XXXX-agent-lb-XXXX**というような名前) を選択します。
   
    ![Azure container service load balancer](./media/container-service-enable-public-access/agent-load-balancer.png)
4. **[プローブ]**、**[追加]** の順にクリックします。
   
    ![Azure container service load balancer probes](./media/container-service-enable-public-access/add-probe.png)
5. プローブのフォームに入力し、 **[OK]** をクリックします。
   
   | フィールド | 説明 |
   | --- | --- |
   | Name |プローブのわかりやすい名前。 |
   | ポート |テストするコンテナーのポート。 |
   | Path |(HTTP モードの場合) プローブする相対 Web サイト パス。 HTTPS はサポートされていません。 |
   | interval |プローブの試行の間隔 (秒単位)。 |
   | 異常のしきい値 |コンテナーが異常と判断されるまでの、連続するプローブの試行回数。 |
6. エージェント ロード バランサーのプロパティに戻って、**[負荷分散規則]**、**[追加]** の順にクリックします。
   
    ![Azure container service load balancer rules](./media/container-service-enable-public-access/add-balancer-rule.png)
7. ロード バランサーのフォームに入力し、 **[OK]** をクリックします。
   
   | フィールド | 説明 |
   | --- | --- |
   | Name |ロード バランサーのわかりやすい名前。 |
   | ポート |パブリック受信ポート。 |
   | バックエンド ポート |トラフィックのルーティング先となるコンテナーの内部パブリック ポート。 |
   | バックエンド プール |このプール内のコンテナーが、このロード バランサーの対象となります。 |
   | プローブ |**バックエンド プール** 内のターゲットが正常であるかどうかを判断するために使用されるプローブ。 |
   | セッション永続化 |セッションの期間中、クライアントからのトラフィックをどのように処理するかを決定します。<br><br>**なし**:同じクライアントからの連続する要求は、任意のコンテナーで処理できます。<br>**クライアント IP**:同じクライアント IP からの連続する要求は、同じコンテナーで処理できます。<br>**クライアント IP とプロトコル**:同じクライアント IP とプロトコルの組み合わせからの連続する要求は、同じコンテナーで処理できます。 |
   | アイドル タイムアウト |(TCP のみ) *keep-alive* メッセージに依存せずに、TCP/HTTP クライアントを開いたままにしておく時間 (分単位)。 |

## <a name="add-a-security-rule-portal"></a>セキュリティ規則を追加する (ポータル)
次に、開いたポートからファイアウォールを介してトラフィックをルーティングするセキュリティ規則を追加する必要があります。

1. ポータルにログインします。
2. Azure Container Service をデプロイしたリソース グループを見つけます。
3. **パブリック** エージェント ネットワーク セキュリティ グループ (**XXXX-agent-public-nsg-XXXX** のような名前) を選択します。
   
    ![Azure container service network security group](./media/container-service-enable-public-access/agent-nsg.png)
4. **[受信セキュリティ規則]**、**[追加]** の順に選択します。
   
    ![Azure container service network security group rules](./media/container-service-enable-public-access/add-firewall-rule.png)
5. パブリック ポートを許可するようにファイアウォール規則を設定し、 **[OK]** をクリックします。
   
   | フィールド | 説明 |
   | --- | --- |
   | Name |ファイアウォール規則のわかりやすい名前。 |
   | 優先順位 |規則の優先順位。 数値が低いほど、優先度は高くなります。 |
   | ソース |この規則によって許可または拒否される受信 IP アドレスの範囲を制限します。 制限を指定しない場合は、 **[任意]** を使用します。 |
   | サービス |このセキュリティ規則の対象となる定義済みサービスのセットを選択します。 または、 **[カスタム]** を使用して独自に作成します。 |
   | プロトコル |**TCP** または **UDP** に基づいて、トラフィックを制限します。 制限を指定しない場合は、 **[任意]** を使用します。 |
   | ポートの範囲 |**[サービス]** が **[カスタム]** である場合は、この規則が影響するポートの範囲を指定します。 **80** のような単一のポートを使用することも、**1024-1500** のような範囲を使用することもできます。 |
   | Action |条件に一致するトラフィックを許可または拒否します。 |

## <a name="next-steps"></a>次の手順
[パブリックとプライベートの DC/OS エージェント](container-service-dcos-agents.md)の違いについて学習します。

[DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)の詳細をお読みください。

