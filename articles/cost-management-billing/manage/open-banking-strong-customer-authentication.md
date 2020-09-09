---
title: Azure のお客様向けのオープン バンキング (PSD2) と強力なカスタマー認証 (SCA)
description: この記事では、一部の Azure 購入で多要素認証が必要な理由と、認証を完了する方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b4c2db76405e7bad7653c1801eb2bcc5db82fe9b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684798"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Azure のお客様向けのオープン バンキング (PSD2) と強力なカスタマー認証 (SCA)

2019 年 9 月 14 日以降、[欧州経済領域](https://en.wikipedia.org/wiki/European_Economic_Area)の 31 の国/地域の銀行では、オンライン購入を行う人物の身元を検証してからその支払いを処理することが必要になりました。 この検証では、オンライン購入の安全性と保護を確保するのに役立つ多要素認証が必要です。 一部の国/地域では、この検証要件の日付が遅延されます。 詳細については、[PSD2 に関する Microsoft の FAQ](https://support.microsoft.com/en-us/help/4517854?preview) に関するページを参照してください。

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

詳細については、「[Azure 用にクレジット カードを追加、更新、または削除する](change-credit-card.md)」を参照してください。

### <a name="settle-outstanding-charges-for-azure-services"></a>Azure サービスの未払い料金を決済する

ご利用の銀行によって料金が拒否された場合、Azure portal ではご利用の Azure アカウントの状態が **[期限経過]** に変更されます。 ご利用のアカウントの状態を確認するには、次の手順を行います。

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. **[コストの管理と請求]** で検索します。
3. **[コストの管理と請求]** の **[概要]** ページで、 **[個人用サブスクリプション]** グリッドの状態列を確認します。
4. サブスクリプションに **[期限経過]** というラベルが付けられている場合は、 **[残高の清算]** をクリックします。 プロセス中に多要素認証を完了するように求められます。

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Marketplace および予約の購入の未払い料金を決済する

Marketplace および予約の購入は、Azure サービスとは別に課金されます。 ご利用の銀行によって Marketplace または予約の料金が拒否された場合、請求書は期限経過となり、Azure portal に **[今支払う]** オプションが表示されます。 期限が経過した Marketplace および予約の請求書の支払いを行うには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. **[コストの管理と請求]** で検索します。
3. [課金] で、 **[請求書]** を選択します。
5. サブスクリプションのドロップダウン フィルターで、Marketplace または予約の購入に関連付けられているサブスクリプションを選択します。
6. 請求書のグリッドで、[type]\(種類\) 列を確認します。 種類が **[Azure Marketplace and Reservations]\(Azure Marketplace および予約\)** の場合、請求書の期限日であるか期限が過ぎていると、 **[今支払う]** リンクが表示されます。 **[今支払う]** が表示されない場合、請求書は既に支払い済みです。 [今支払う] の実行中に多要素認証を完了するように求められます。

## <a name="next-steps"></a>次のステップ
- Azure の請求額を支払う必要がある場合は、「[支払期限を過ぎた Azure サブスクリプション未払い額の支払い](resolve-past-due-balance.md)」を参照してください。
