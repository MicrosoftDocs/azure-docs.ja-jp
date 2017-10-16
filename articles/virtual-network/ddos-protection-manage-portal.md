---
title: "Azure Portal を使用した Azure DDoS Protection Standard の管理 | Microsoft Docs"
description: "Azure Monitor で Azure DDoS Protection Standard テレメトリを使用して攻撃を軽減する方法について説明します。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal を使用した Azure DDoS Protection Standard の管理

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) は、現在プレビュー段階にあります。 制限された数の Azure リソースが、選択されたいくつかの地域で DDoS Protection をサポートします。 サブスクリプション用に DDoS Protection を有効にするには、制限されたプレビュー中に[このサービスに登録する](http://aka.ms/ddosprotection)必要があります。 登録すると、Azure DDoS チームから連絡があり、有効化プロセスが案内されます。 DDoS Protection は、米国東部、米国西部、米国中部の各地域で使用できます。 プレビュー中、サービスの使用に対して課金されることはありません。

この記事では、Azure Portal を使用して DDoS Protection を有効にしたり、DDoS Protection を無効にしたり、テレメトリを使用して攻撃を軽減したりする方法について説明します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal (http://portal.azure.com) にログインします。

## <a name="enable-ddos-protection"></a>DDoS Protection を有効にする

新しい仮想ネットワークまたは既存の仮想ネットワーク上で DDoS Protection を有効にします。

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>新しい仮想ネットワークを作成し、DDoS Protection を有効にします。

1. Azure Portal の左上にある **[新規]** ボタンをクリックします。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. 仮想ネットワークの情報を入力します。 *[DDoS 保護]* で、**[有効]** をクリックしてから、**[作成]** をクリックします。

    ![Create virtual network](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

DDoS Protection を有効にすると課金が発生するという警告が表示されます。 プレビュー中、DDoS Protection に対する課金は発生しません。 一般公開 (GA) では課金が発生し、顧客は課金と GA の開始前 30 日の通知を受け取ります。

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>既存の仮想ネットワーク上で DDoS Protection を有効にする 

1. Azure Portal メニューの **[仮想ネットワーク]** をクリックしてから、仮想ネットワークを選択します。
2. **}[DDoS Protection]** をクリックし、*}[DDoS Protection]* 画面で **[有効]** をクリックしてから、**[保存]** をクリックします。 

DDoS Protection を有効にすると課金が発生するという警告が表示されます。 プレビュー中、DDoS Protection に対する課金は発生しません。 一般公開 (GA) では課金が発生し、顧客は課金と GA の開始前 30 日の通知を受け取ります。

## <a name="disable-ddos-protection"></a>DDoS Protection を無効にする

1. Azure Portal メニューの **[仮想ネットワーク]** をクリックしてから、仮想ネットワークを選択します。
2. **}[DDoS Protection]** をクリックし、*[DDoS Protection]* 画面で **[無効]** をクリックしてから、**[保存]** をクリックします。

## <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS Protection メトリックにアラートを構成する

Azure Monitor のアラート構成を利用すると、攻撃中にアクティブな軽減策が存在する場合に警告するいずれかの使用可能な DDoS Protection メトリックを選択できます。 これらの条件が満たされると、指定されたアドレスでアラート電子メールを受信します。

1. **[監視]** をクリックしてから、**[メトリック]** をクリックします。
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、および Azure のパブリック IP を選択します。
3. メトリックの電子メール アラートを構成するには、**[Click to add an alert] (クリックしてアラートを追加)** をクリックします。 電子メール アラートは任意のメトリックに作成できますが、最も明らかなメトリックは **[Under DDoS attack or not] (DDoS 攻撃を受けているかどうか)** です。 これはブール値 1 または 0 です。 **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。
4. 攻撃を受けている場合に電子メールを受信するようにするには、**[Under DDoS attack or not] (DDoS 攻撃を受けているかどうか)** と **[Condition to Greater than zero (0) over the last 5 minutes] (過去 5 分間にゼロ (0) を超えた条件)** のメトリックを設定します。 他のメトリックに対して同様のアラートを設定できます。

    ![メトリックを構成する](./media/ddos-protection-manage-portal/ddos-metrics.png)

    攻撃の検出から数分以内に、Azure Monitor メトリックを使用してユーザーに通知します。

    ![攻撃アラート](./media/ddos-protection-manage-portal/ddos-alert.png) 

また、[webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-what-are-logic-apps.md)の詳細についても学習できます。

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS Protection メトリックへのログ記録を構成する

1. **[監視]** をクリックしてから、**[診断設定]** をクリックします。
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、および Azure のパブリック IP を選択します。
3. **[Turn on diagnostics to collect the following data] (診断を有効にして次のデータを収集する)** をクリックします。

ログ記録に使用できるオプションとして、次の 3 つがあります。

- **[ストレージ アカウントへのアーカイブ]**  – ログをストレージ アカウントに書き込みます。
- **[Stream to an event hub] (イベント ハブへのストリーミング)** – ログの受信者がイベント ハブを使用してログを取得できるようにします。 これにより、Splunk やその他の SIEM システムとの統合が可能になります。
- **[Log Analytics への送信]** – ログを Azure OMS Log Analytics サービスに書き込みます。

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

1. **[監視]** をクリックしてから、**[メトリック]** をクリックします。 
2. *[メトリック]* 画面で、リソース グループ、**[パブリック IP アドレス]** のリソースの種類、および Azure のパブリック IP を選択します。 一連の使用可能なメトリックが画面の左側に表示されます。 これらのメトリックは、選択されると、概要画面の Azure Monitor メトリック チャートにグラフ化されます。 

メトリック名は、異なるパケットの種類およびバイト数とパケット数を、各メトリックでのタグ名の基本的な構成で次のように提供します。

- **削除されたタグ名 (たとえば、削除された受信パケット DDoS):** DDoS 保護システムによって削除/除去されたパケットの数。
- **転送されたタグ名 (たとえば、転送された受信パケット DDoS):** DDoS システムによって宛先 VIP に転送されたパケットの数 (フィルター処理されなかったトラフィック)。
- **タグ名なし (たとえば、受信パケット DDoS):** 除去システムに入力されたパケットの総数 (削除および転送されたパケットの総数を表します)。

## <a name="next-steps"></a>次のステップ

- [Azure 診断ログの詳細を確認する](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)