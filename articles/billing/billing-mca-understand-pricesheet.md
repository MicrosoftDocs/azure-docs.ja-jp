---
title: Microsoft 顧客契約の価格シートの用語を理解する - Azure | Microsoft Docs
description: Azure サブスクリプションの使用状況と課金内容を確認して理解する方法について説明します
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880757"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約の価格シートの用語を理解する

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認します](#check-access-to-a-microsoft-customer-agreement)。

課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者の場合、組織の価格シートを Azure portal からダウンロードすることができます。 [組織の価格の表示およびダウンロード](billing-ea-pricing.md)に関する記事を参照してください。

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft 顧客契約の価格シートの詳細な用語と説明

次のセクションでは、Microsoft の顧客契約の価格シートに表示されている重要な用語について説明します。

| **フィールド名**   | **説明**   |
| --- | --- |
| billingAccountId  | 請求先アカウントの一意の識別子。   |
| billingAccountName  | 請求先アカウントの名前。  |
| billingProfileId  | 課金プロファイルの一意の識別子。   |
| billingProfileName  | 請求書を受け取るよう設定されている課金プロファイルの名前。 価格シート内の価格は、この課金プロファイルに関連付けられています。 |
| productOrderName  | 購入した製品プランの名前。 |
| serviceFamily  | Azure サービスの種類。例: Compute、Analytics、Security |
| Product  | 料金が発生する製品の名前。例: Basic SQL DB vs Standard SQL DB  |
| productId  | メーターが消費される製品の一意の識別子。 |
| unitOfMeasure  | サービス課金の測定単位の一意の識別子。 たとえば、コンピューティング サービスは時間単位で課金されます。 |
| meterId  | メーターの一意の識別子。 |
| meterName  | メーターの名前。 メーターは、Azure サービスのデプロイ可能なリソースを表します。 |
| meterCategory  | メーターの分類カテゴリの名前。 たとえば、_Cloud services_、_Networking_ などです。 |
| meterType  |  メーターの種類の名前。 |
| meterSubCategory  | メーターのサブ分類カテゴリの名前。  |
| meterRegion  | サービスのメーターが使用可能なリージョンの名前。 データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。    |
| tierId  | 価格レベルを特定します (該当する場合)。 消費されるユニット数に応じて価格が変動する場合、tierMinimumUnits と連携して価格レベルを設定します。    |
| tierMinimumUnits  | 価格が定義されているレベル範囲の下限を定義します。 たとえば、範囲が 0 から 100 の場合、tierMinimumUnits は 0 になります。  |
| effectiveStartDate  | 価格が有効になる開始日。 |
| effectiveEndDate  | 有効価格の終了日。 |
| unitPrice  | メーターと製品オーダー名に特有の、課金時のユニットあたりの価格 (有効なブレンド価格ではない)。  注:レベルごとに価格が異なるサービスの場合、単価は使用量の詳細のダウンロード内の有効価格と同じではありません。  複数のレベルの価格にわたるサービスの場合、有効価格はレベル全体のブレンド価格であり、レベル別の単価は示されません。 ブレンド価格または有効価格は、複数のレベル (各レベルには固有の単価が存在) にまたがって消費された数量に対する正味価格です。 |
| basePrice  | お客様がサインオンした時点での市場価格、またはサインオン後の場合はサービス メーターが開始した時点での市場価格。   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [組織の価格の表示とダウンロード](billing-ea-pricing.md)
