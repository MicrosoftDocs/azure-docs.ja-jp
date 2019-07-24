---
title: Azure SaaS アプリケーション オファーのプラン | Microsoft Docs
description: Azure Marketplace にある SaaS アプリケーション オファーのプランを作成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 0394fa759f4b6f6af59e075d9bc67668886d8075
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195881"
---
# <a name="saas-application-plans-tab"></a>[SaaS application Plans]\(Saas アプリケーション プラン\) タブ

[プラン] タブを使用して新しいプランを作成します。 SaaS アプリの Microsoft オプションを介して Sell を使用している場合、プランを少なくとも 1 つ追加する必要があります。

![新しいプランを作成する](./media/saas-plans-new-plan.png)

## <a name="create-a-new-plan"></a>新しいプランを作成する

新しいプランを作成するには:

1. **[プラン]** の下で **[+ 新しいプラン]** を選択します。
2. **[新しいプラン]** ポップアップ ウィンドウで**プラン ID** を入力します。 この ID の最大長は 50 文字です。 この ID は小文字の英数字、ハイフン、アンダースコアのみで構成する必要があります。 オファーの発行後、この ID は変更できません。
3. **[OK]** を選択してプラン ID を保存します。

   ![新しいプランのプラン ID](./media/saas-plans-plan-ID.png)

### <a name="to-configure-the-plan"></a>プランを構成するには:

1. **[プランの詳細]** で、次のフィールドの情報を指定します。

   - [タイトル] - プランのタイトルを入力します。 このタイトルは 50 文字に制限されています。
   - [説明] - 説明を入力します。 この説明は 500 文字に制限されています。
   - [Is this a private plan?]\(プライベート プランか\) - プランが一部の顧客グループのみ利用できる場合、**[はい]** を選択します。
   - [国/リージョンの可用性] - プランは少なくとも 1 つの国またはリージョンで利用できるようにする必要があります。 **[リージョンの選択]** をクリックします。 **[国/リージョンの可用性を選択する]** 一覧から国/リージョンを選択し、**[OK]** を選択します。 
   - [Legacy Pricing]\(レガシ価格\) - 月あたりのコストを USD で指定します。

2. **[Simplified Currency Pricing]\(簡略化された通貨による価格\)** に次の情報を入力します。

   - [Billing Term]\(請求期間\) - 月額料金が既定で選択されています。 年間料金を指定することもできます。
   - [月額料金] - 月額料金を指定します。この価格はレガシ価格に一致させる必要があります。

   >[!NOTE] 
   >請求期間に年額料金を追加する場合、**[年額料金]** を年あたりの USD 金額で入力するように求められます。

3. **[保存]** を選択し、プランの構成を完了します。

   >[!NOTE] 
   >料金変更の保存後、料金データをエクスポート/インポートできます。

![プランの詳細を構成する](./media/saas-plans-plan-details.png)

## <a name="next-steps"></a>次の手順

[[チャンネル情報] タブ](./cpp-channel-info-tab.md)

