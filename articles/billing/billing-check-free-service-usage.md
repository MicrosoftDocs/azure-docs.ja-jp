---
title: Azure 無料サービスの使用状況の監視および追跡 | Microsoft Docs
description: 無料サービスの使用状況を確認する方法を説明します。 Azure Portal と使用状況データの csv を使用します。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849858"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Azure 無料アカウントで無料サービスの使用状況を確認する 

Azure 無料アカウントに含まれる無料サービスは、使用制限を超過しない限りは課金されません。 使用制限内で使用するために、Azure Portal または使用状況ファイルを使用して、無料サービスの使用状況を監視および追跡できます。 

## <a name="check-usage-on-the-azure-portal"></a>Azure Portal で使用状況を確認する

1.  [Azure Portal](https://portal.azure.com) にサインインします。

2.  左側のナビゲーション領域で、**[すべてのサービス]** を選択します。

3.  **[サブスクリプション]** を選択します。

4.  無料アカウントにサインアップしたときに作成したサブスクリプションを選択します。

    ![すべてのサブスクリプションを表示したスクリーンショット](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  [概要] セクションには、サブスクリプション ID、オファーの種類、サブスクリプション名などのサブスクリプションに関する重要な情報が表示されます。 無料アカウントのクレジットがいつごろ期限切れになるかの情報も、ここで確認できます。

    ![サブスクリプションの重要な情報を表示したスクリーンショット](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  下にスクロールすると、現在のコストと今後の予想コストに関する情報が表示されます。 このコストには、無料アカウントに含まれていないサービスの使用量と、無料サービスの使用制限を超過した分の使用量が含まれています。 

    ![サブスクリプションのコスト情報を表示したスクリーンショット](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  [概要] セクションの最後の部分に、無料サービスの使用状況に関する表があります。 

    ![無料サービスの使用状況を表示したスクリーンショット](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    表には次の列が含まれています。

* **メーター名:** 消費しているメーターの測定単位を示します。 サービスのメーター マッピングの詳細については、「[無料サービスと測定のマッピングについて](billing-understand-free-service-meter-mapping.md)」をご覧ください。
* **使用状況/使用制限:** 当月の使用状況とメーターの使用制限。 この情報はステータス バーでも確認できます。
* **状態:** メーターの使用状況ステータス。 使用状況によって、次のいずれかの状態が表示されます。
  * **[未使用]:** メーターを使用しなかった、またはメーターの使用状況が課金システムに伝わらなかった。
  * **[\<日付> に超えました]:**\<日付> にメーターの使用制限を超過した。
  * **[超える可能性は低い]:** メーターの使用制限を超過する可能性が低い。
  * **[\<日付> に超えます]:**\<日付> にメーターの使用制限を超過する可能性がある。

## <a name="check-usage-through-the-usage-file"></a>使用状況ファイルを使用して使用状況を確認する

使用状況ファイルには、Azure サブスクリプションの詳しい情報が記載されています。 Azure アカウント センターから、毎月および毎日の使用状況のファイルをダウンロードできます。 使用状況ファイルのダウンロード方法と、必要なアクセス許可の詳細については、[請求書と使用状況の取得](billing-download-azure-invoice-daily-usage-date.md)に関するページをご覧ください。 使用状況ファイル内の列の詳細については、[使用状況の用語](billing-understand-your-usage.md)に関するページをご覧ください。

使用状況ファイルには、無料サービスと有料サービス両方の使用状況に関する情報が含まれています。 無料サービスのメーターは、メーター名の末尾に **[Free]\(無料\)** という文字が付加されています。 無料のメーターを見つけるには、Excel でファイルを開き、**[測定カテゴリ]** 列でフィルターをかけて **[- Free](- 無料)** というテキストを含むセルを見つけます ([テキスト フィルター] &rarr; [Contains](次を含む) を使用します)&nbsp;

![無料サービスの使用状況を表示したスクリーンショット](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。