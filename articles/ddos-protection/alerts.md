---
title: Azure DDoS Protection Standard の DDoS 保護アラートの表示および構成
description: Azure DDoS Protection Standard の DDoS 保護アラートの表示および構成について説明します。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575290"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>DDoS 保護アラートの表示と構成

Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 豊富なテレメトリは、DDoS 攻撃の間に、詳細なメトリックを含む Azure Monitor を通じて公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェイスを介した高度な分析用に、ログを [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure Event Hubs)、OMS Log Analytics、Azure Storage とさらに統合できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Monitor を使用してアラートを構成する
> * ポータルを使用してアラートを構成する
> * Azure Security Center でアラートを表示する
> * アラートを検証およびテストする

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このチュートリアルの手順を実行する前に、まず [Azure DDoS Standard 保護プラン](manage-ddos-protection.md)を作成し、仮想ネットワーク上で DDoS Protection Standard を有効にしておく必要があります。
- DDoS は、仮想ネットワーク内のリソースに割り当てられているパブリック IP アドレスを監視します。 仮想ネットワーク内のパブリック IP アドレスを持つリソースがない場合は、最初にパブリック IP アドレスを使用してリソースを作成する必要があります。 Azure App Service Environment を除き、[Azure サービスの仮想ネットワーク](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関する記事に一覧表示されている Resource Manager (クラシックではない) を通じてデプロイされている、すべてのリソースのパブリック IP アドレスを監視できます (バックエンド仮想マシンが仮想ネットワーク内にある Azure Load Balancer を含む)。 このチュートリアルを続行するために、[Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンをすばやく作成できます。     

## <a name="configure-alerts-through-azure-monitor"></a>Azure Monitor を使用してアラートを構成する

これらのテンプレートを使用すると、診断ログを有効にしたすべてのパブリック IP アドレスに対してアラートを構成できます。 そのため、これらのアラート テンプレートを使用するには、まず、診断設定が有効になっている Log Analytics ワークスペースが必要です。 「[DDoS 診断ログの表示と構成](diagnostic-logging.md)」を参照してください。

### <a name="azure-monitor-alert-rule"></a>Azure Monitor のアラート ルール

この [Azure Monitor アラート ルール](https://aka.ms/DDOSmitigationstatus)では、アクティブな DDoS 軽減が発生したことを検出するための単純なクエリが実行されます。 これは、攻撃を受ける可能性があることを示します。 アクション グループを使用すると、アラートの結果としてアクションを呼び出すことができます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>ロジック アプリを使用した Azure Monitor のアラート ルール

この[テンプレート](https://aka.ms/ddosalert)では、強化された DDoS 軽減アラートの必要なコンポーネント(Azure Monitor のアラート ルール、アクション グループ、およびロジック アプリ) を展開します。 このプロセスの結果は、IP に関連付けられているリソースに関する情報など、攻撃を受けた IP アドレスの詳細を含む電子メール アラートです。 リソースの所有者は、セキュリティ チームと共に、電子メールの受信者として追加されます。 基本的なアプリケーションの可用性テストも実行され、結果は電子メール アラートに含められます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>ポータルを使用してアラートを構成する

Azure Monitor のアラート構成を使用すると、利用可能な DDoS Protection メトリックをどれでも選び、攻撃中に有効な軽減策がある場合にそのメトリックのアラートを受けることができます。 

1. [Azure portal](https://portal.azure.com/) にサインインし、DDoS Protection プランに移動します。
2. **[監視]** で **[メトリック]** を選びます。
3. 灰色のナビゲーション バーで、 **[新しいアラート ルール]** を選択します。 
4. 独自の値を入力または選択するか次の例の値を入力し、残りの既定値はそのまま受け入れて、 **[アラート ルールの作成]** を選択します。

    |設定                  |値                                                                                               |
    |---------                |---------                                                                                           |
    | スコープ                   | **[リソースの選択]** を選択します。 </br> ログに記録するパブリック IP アドレスを含む **サブスクリプション** を選択し、 **[リソースの種類]** で **[パブリック IP アドレス]** を選択してから、メトリックのログを記録する特定のパブリック IP アドレスを選択します。 </br> **[Done]** を選択します。 | 
    | 条件 | **[条件の選択]** を選択します。 </br> シグナル名で、 **[DDoS 攻撃中かどうか]** を選択します。 </br> **[演算子]** で、 **[次の値以上]** を選択します。 </br> **[集計の種類]** で、 **[最大]** を選択します。 </br> **[しきい値]** で、「*1*」と入力します。 **[DDoS 攻撃中かどうか]** メトリックの場合、**0** は攻撃を受けていないことを示し、**1** は攻撃を受けていることを示します。 </br> **[Done]** を選択します。 | 
    | Actions | **[アクション グループの追加]** を選択します。 </br> **[アクション グループの作成]** を選択します。 </br> **[通知]** の **[通知の種類]** で、 **[メール/SMS メッセージ/プッシュ/音声]** を選択します。 </br> **[名前]** に「_MyUnderAttackEmailAlert_」と入力します。 </br> 編集ボタンをクリックし、 **[メール]** と次のオプションを必要なだけ選択してから、 **[OK]** を選択します。 </br> **[Review + create]\(レビュー + 作成\)** を選択します。 | 
    | アラート ルールの詳細 | **[アラート ルール名]** の下で、「_MyDdosAlert_」と入力します。 |

攻撃の検出から数分以内に、次の図のような電子メールが Azure Monitor メトリックから届くはずです。

![攻撃アラート](./media/manage-ddos-protection/ddos-alert.png)

また、[webhook の構成](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細についても学習できます。

## <a name="view-alerts-in-azure-security-center"></a>Azure Security Center でアラートを表示する

Azure Security Center は、[セキュリティ アラート](../security-center/security-center-managing-and-responding-alerts.md)の一覧と、問題の調査および修復に役立つ情報を提供します。 この機能により、DDoS 攻撃に関連するアラートや、ほぼリアルタイムで攻撃を軽減するために実行されるアクションを含んだ、アラートの統合ビューが得られます。
DDoS 攻撃の検出と軽減に対して表示される 2 つの特定のアラートがあります。

- **パブリック IP に対して検出された DDoS 攻撃**:DDoS Protection サービスにより、パブリック IP アドレスのいずれかが DDoS 攻撃の対象であることが検出されると、このアラートが生成されます。
- **パブリック IP に対して軽減された DDoS 攻撃**:このアラートは、パブリック IP アドレスへの攻撃が軽減された場合に生成されます。
アラートを表示するには、Azure portal で **[Security Center]** を開きます。 **[脅威の防止]** で、 **[セキュリティ アラート]** を選択します。 次のスクリーンショットは、DDoS 攻撃アラートの例を示します。

![Azure Security Center での DDoS アラート](./media/manage-ddos-protection/ddos-alert-asc.png)

アラートには、攻撃、地域および脅威インテリジェンス情報、修復手順の下にあるパブリック IP アドレスの全般的な情報が含まれます。

## <a name="validate-and-test"></a>検証とテスト

DDoS 攻撃をシミュレートしてアラートを検証するには、「[DDoS 検出を検証する](test-through-simulations.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

- Azure Monitor を使用してアラートを構成する
- ポータルを使用してアラートを構成する
- Azure Security Center でアラートを表示する
- アラートを検証およびテストする

DDoS 攻撃をテストおよびシミュレートする方法については、以下のシミュレーション テスト ガイドを参照してください。

> [!div class="nextstepaction"]
> [シミュレーションを通じたテスト](test-through-simulations.md)
