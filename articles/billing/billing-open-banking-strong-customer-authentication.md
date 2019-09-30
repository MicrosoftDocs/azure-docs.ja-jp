---
title: Azure のお客様向けのオープン バンキング (PSD2) と強力なカスタマー認証 (SCA)
description: この記事では、一部の Azure 購入で多要素認証が必要な理由と、認証を完了する方法について説明します。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 3074ed30c5e1f6edae78a8ef3c3d655e302a2663
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997283"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Azure のお客様向けのオープン バンキング (PSD2) と強力なカスタマー認証 (SCA)

2019 年 9 月 14 日以降、[欧州経済領域](https://en.wikipedia.org/wiki/European_Economic_Area)の 31 か国の銀行では、オンライン購入を行う人物の身元を検証してからその支払いを処理することが必要になりました。 この検証では、オンライン購入の安全性と保護を確保するのに役立つ多要素認証が必要です。 一部の国では、この検証要件の日付が遅延されます。 詳細については、[PSD2 に関する Microsoft の FAQ](https://support.microsoft.com/en-us/help/4517854?preview) に関するページを参照してください。

## <a name="what-psd2-means-for-azure-customers"></a>Azure のお客様にとっての PSD2 の意味

[欧州経済地域](https://en.wikipedia.org/wiki/European_Economic_Area)にある銀行によって発行されたクレジット カードを使用して Azure の支払いを行う場合、ご利用のアカウントの支払い方法として多要素認証を完了することが求められる場合があります。 ご利用の Azure アカウントにサインアップするとき、またはご利用の Azure アカウントをアップグレードするとき (その時点では購入を行わなくても)、多要素認証のチャレンジを完了するように求められる場合があります。 また、ご利用の Azure アカウントの支払い方法を変更する場合、使用上限を削除する場合、未払い残高の決済や Azure クレジットの購入など、Azure portal からの即時支払いを行う場合に、多要素認証を提供するように求められる場合もあります。

ご利用の銀行から月額の Azure の料金が拒否された場合、Azure から期限経過に関する電子メールが届き、それを修正するための手順が示されます。 Azure portal で多要素認証チャレンジを完了し、未払い残高を決済することができます。

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Azure portal で多要素認証を完了する

次のセクションでは、Azure portal で多要素認証を完了する方法について説明します。 ご自分に適用される情報を使用します。

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Azure アカウントのアクティブな支払い方法を変更する

ご利用の Azure アカウントのアクティブな支払い方法を変更するには、次の手順を行います。

1. アカウント管理者として [Azure portal](https://portal.azure.com) にサインインし、 **[コストの管理と請求]** に移動します。
2. **[概要]** ページで、 **[個人用サブスクリプション]** グリッドから対応するサブスクリプションを選択します。
3. [請求先] で、 **[支払方法]** を選択します。 サブスクリプションのアクティブな支払い方法として、新しいクレジットカードを追加することも、既存のカードを設定することもできます。 ご利用の銀行で多要素認証が必要な場合は、プロセス中に認証チャレンジを完了するように求められます。

詳細については、「[Azure 用にクレジット カードを追加、更新、または削除する](billing-how-to-change-credit-card.md)」を参照してください。

### <a name="settle-outstanding-charges-for-azure-services"></a>Azure サービスの未払い料金を決済する

ご利用の銀行によって料金が拒否された場合、Azure portal ではご利用の Azure アカウントの状態が **[期限経過]** に変更されます。 ご利用のアカウントの状態を確認するには、次の手順を行います。

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. **[コストの管理と請求]** で検索します。
3. **[コストの管理と請求]** の **[概要]** ページで、 **[個人用サブスクリプション]** グリッドの状態列を確認します。
4. サブスクリプションに **[期限経過]** というラベルが付けられている場合は、 **[残高の清算]** をクリックします。 プロセス中に多要素認証を完了するように求められます。

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Marketplace および予約の購入の未払い料金を決済する

Marketplace および予約の購入は、Azure サービスとは別に課金されます。 ご利用の銀行によって Marketplace または予約の料金が拒否された場合、Azure portal ではご利用の請求書の状態が **[期限経過]** と表示されます。 Marketplace および予約の請求書の状態を確認するには、次の手順を行います。

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. **[コストの管理と請求]** で検索します。
3. [請求先] で、[請求書] を選択します。
4. 右側にある **[Azure Marketplace と Reservations]** タブをクリックします。
5. 対応するサブスクリプションを選択します。
6. [請求書] グリッドで、状態列を確認します。 請求書が **[期限]** または **[期限経過]** になっている場合は、 **[今支払う]** をクリックします。 プロセス中に多要素認証を完了するように求められます。

## <a name="next-steps"></a>次の手順
- Azure の請求額を支払う必要がある場合は、「[支払期限を過ぎた Azure サブスクリプション未払い額の支払い](billing-azure-subscription-past-due-balance.md)」を参照してください。
