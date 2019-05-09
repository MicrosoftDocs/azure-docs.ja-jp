---
title: Azure サブスクリプションの請求書による支払い | Microsoft Docs
description: Azure サブスクリプションを請求書で支払う方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: banders
ms.openlocfilehash: 0a17821cc69900c7f24375cbf06a61811bbd52d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786966"
---
# <a name="submit-a-request-to-pay-azure-subscription-by-invoice"></a>Azure サブスクリプションの請求書による支払いの要求を送信する

Azure サポートに要求を送信して、Azure サブスクリプションの支払い方法を、請求書による支払いに変更できます。 要求が承認されると、サブスクリプションを設定して請求書による支払いに変更するための手順が通知されます。

> [!IMPORTANT]
> * 請求書支払いはビジネス アカウントのみでご利用いただけます。
> * [サード パーティと外部のサービス](billing-understand-your-azure-marketplace-charges.md)は請求書支払いでは購入できず、支払いもできません。 サブスクリプションに SendGrid のような外部サービスのリソースが含まれる場合、請求書支払いに変更する前に削除する必要があります。 請求書支払いに切り替え後、外部サービスを購入するには、クレジット カードまたはデビット カードで別のサブスクリプションを用意する必要があります。
> * 支払請求書に切り替えた後にクレジット カードまたはデビット カードによる支払いに戻すことはできません。

## <a name="request-pay-by-invoice"></a>請求書による支払いを要求する

1. [Azure Portal](https://portal.azure.com/) にサインインします。 **[ヘルプとサポート]** > **[新しいサポート要求]** の順に選択します。

    ![[ヘルプとサポート] ボタン](./media/billing-how-to-pay-by-invoice/helpandsupport.png)

2. [問題の種類] で **[課金]** を選択し、請求書で支払うサブスクリプションを選択します。サポート プランを選択し、**[次へ]** を選択します。

3. **[問題の種類]** ボックスで **[Pay by Invoice (請求書による支払い)]** オプションを選択します。

 4. **[詳細]** ボックスに次の情報を入力し、**[次へ]** を選択します。

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option): 
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID: 
         Company Name (as registered under VAT or Government Website): 
         Company Address (as registered under VAT or Government Website): 
         Company Website:
         Country: 
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft: 
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card: 
    
        For cores increase, provide the following additional information:
    
         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription: 
 
    - **[会社名]** と **[会社の住所]** は、Azure アカウントに指定した情報と一致させる必要があります。  情報を表示または更新するには、「[Azure アカウントのプロファイル情報を変更する](billing-how-to-change-azure-account-profile.md)」を参照してください。
    - 与信限度額を承認する前に、Azure portal で請求連絡先を追加する必要があります。 連絡先の詳細を会社の買掛金または財務部門に関連付ける必要があります。 請求連絡先を更新するには、[Azure アカウント センター](https://account.azure.com/Profile)に移動します。

5. 連絡先情報と優先連絡方法を確認し、**[作成]** をクリックします。

必要とするクレジット総額によっては与信審査が必要になることがあります。この場合は、カスタマー サポートから与信審査申込書をお送りします。 与信審査の処理には、お申し込みから 5 ～ 7 営業日かかる可能性があります。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。