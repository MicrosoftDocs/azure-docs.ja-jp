---
title: パートナー センターで従量制課金の異常を管理する | Azure Marketplace
description: 従量制課金に対する自動の異常検出によって、ご自身の顧客がコマーシャル マーケットプレースのオファーの従量制課金使用量に応じて正しく請求されるようにする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092513"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>パートナー センターで従量制課金の異常を管理する

現在、カスタムの従量制課金オプションは、[サービスとしてのソフトウェア](plan-saas-offer.md) (SaaS) オファーと、マネージド アプリケーション プランを提供する [Azure アプリケーション](plan-azure-application-offer.md#types-of-plans)に対して使用できます。

従量制課金オプションを使用して、標準以外のユニットに基づく使用量の請求を可能にするコマーシャル マーケット プレース プログラムでオファーを作成する場合は、顧客が予想よりも多くのサービスを使用した際にそれを検知する必要があります。

## <a name="use-the-anomaly-detection-feature"></a>異常検出機能を使用する

Microsoft は、Microsoft からお客様への請求の前に SaaS または Azure マネージド アプリケーション オファーの顧客の超過分使用量を報告することをパートナーに委任しています。 間違った使用量が報告された場合、顧客が間違った請求書を受け取る可能性があり、Microsoft とパートナーの両方の信頼性が損なわれることになります。

顧客が正しく請求されるようにするには、SaaS アプリおよび Azure アプリケーションで管理された両方のアプリケーション プランに対して、**異常検出** 機能を使用します。 この機能は、従量制課金に対して使用量を監視し、予想される範囲内での使用量の予想値を予測します。 使用量が想定される範囲外の場合は予想外 (異常) として扱われ、パートナー センターのコマーシャル マーケットプレース プログラムの [オファーの概要] ページにアラート通知が表示されます。 設定したカスタム メーター ディメンションごとに、顧客の使用量を毎日追跡できます。

## <a name="view-and-manage-metered-usage-anomalies"></a>従量制課金使用量の異常の表示と管理

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[分析]** の順に選択します。
1. **[Metered usage anomalies]\(従量制課金使用量の異常\)** タブを選択します。

    [![[利用状況] ページの [Metered usage anomalies]\(従量制課金使用量の異常\) タブを示しています。](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    "***図 1:[Metered usage anomalies]\(従量制課金使用量の異常\) タブ***

1. 従量制課金に対して検出された使用量の異常については、発行元として、その異常が本当なのかどうかを調査して確認するように求められます。 診断を確認するには、 **[Mark as anomaly]\(異常としてマーク\)** を選択します。

     [![[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックスを示しています。](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    "***図 2:[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックス***

1. 検出された超過分使用量の異常が正規のものではないと思われる場合は、そのフィードバックを提供できます。そのためには、パートナー センターで異常のフラグが設定された特定の超過分使用量について、 **[Not an anomaly]\(異常ではない\)** を選択します。

    [![[Why is it not an anomaly]\(異常ではない理由\) ダイアログ ボックスを示しています。](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    "***図 3:[Why is it not an anomaly?]\(異常ではない理由\) ダイアログ ボックス***

1. ページを下にスクロールすると、未確認の異常の一覧が表示されます。 一覧には、未確認のまま残っている異常が表示されます。 パートナー センターでフラグが設定された任意の異常を正規のものまたは誤りとしてマークすることができます。

   [![パートナー センターの [利用状況] ページでの未確認の異常の一覧を示しています。](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    "***図 4:パートナー センターでの未確認の異常の一覧***

1. また、超過分の使用量に対して実行したアクションを示す異常のアクション ログも表示されます。 アクション ログでは、どの超過分使用量イベントが正規のものまたは誤りとしてマークされたかを確認できます。

   [![[利用状況] ページの異常のアクション ログを示しています。](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)
   ***図 5:異常のアクション ログ***

1. パートナー センターの分析で、エクスポート レポートにおける超過分使用量イベントの修正再表示はサポートされていません。 パートナー センターで、異常に対して修正された超過分の使用量を入力できます。詳細は調査のために Microsoft チームに渡されます。 調査に基づいて、超過料金が発生した顧客に Microsoft から必要に応じてクレジット返金が発行されます。 フラグが設定された異常のどれかを選択したときに **[Mark as anomaly]\(異常としてマーク\)** を選択すると、使用量の超過分の異常を正規のものとしてマークできます。

   [![[Mark as anomaly]\(異常としてマーク\) ダイアログ ボックスを示しています。](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)
   ***図 6: [Mark as anomaly]\(異常としてマーク\) ダイアログ ボックス***

パートナー センターで超過分の使用量に対して初めて不規則のフラグが設定された場合、異常を正規のものまたは誤りとしてマークするために、そのインスタンスから 30 日の期間が与えられます。 30 日の期間が終了すると、発行元として異常に対処することはできなくなります。

> [!IMPORTANT]
> 過少報告された超過分の使用量単位は、修正再表示および財務調整の対象にできません。

選択した計算期間のすべての異常 (確認済みまたはそれ以外) を確認できます。 計算期間は、過去 30 日間、過去 60 日間、過去 90 日間とさまざまです。

> [!IMPORTANT]
> 例外が最初にパートナー センターで報告されてから 30 日以内に、フラグが設定された異常を処理することが必要とされます。

ウィジェットのフィルター モーダルを使用すると、個々のオファーと個々のカスタム メーターを選択できます。

超過分の使用量を自分で異常としてマークするか、異常としてフラグ設定したモデルを正規のものとして確認した後、選択を "異常ではない" に変更することはできません。

> [!IMPORTANT]
> 過剰請求の状況が発生した場合は、超過分の使用量を再送信できます。

## <a name="see-also"></a>関連項目
- [コマーシャル マーケットプレースの測定サービスを使用した SaaS の従量制課金](./partner-center-portal/saas-metered-billing.md)
- [マネージド アプリケーションの従量制課金](./partner-center-portal/azure-app-metered-billing.md)
- [従量制課金に対する異常検出サービス](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
