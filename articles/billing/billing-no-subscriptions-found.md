---
title: 「サブスクリプションが見つかりません」エラー - Azure portal へのサインイン | Microsoft Docs
description: Azure Portal または Azure アカウント センターにサインインしようとすると「サブスクリプションが見つかりません」エラーになるという問題の解決策を示します。
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881114"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Azure portal または Azure アカウント センターにおける「サブスクリプションが見つかりません」のサインイン エラー

[Azure Portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されることがあります。 この記事では、この問題の解決策について説明します。

## <a name="symptom"></a>症状

[Azure portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されます。

## <a name="cause"></a>原因

この問題は、誤ったディレクトリで選択した場合、または、自分のアカウントに十分なアクセス許可がない場合に発生します。 

## <a name="solution"></a>解決策

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>シナリオ 1:[Azure portal](https://portal.azure.com) でエラー メッセージが表示される

この問題を解決するには、次の手順に従います。

* 右上にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。

  ![Azure ポータルの右上にあるディレクトリを選択する](./media/billing-no-subscriptions-found/directory-switch.png)
* 適切な Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、[アカウントに所有者ロールを割り当て](../role-based-access-control/role-assignments-portal.md)ます。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>シナリオ 2:[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でエラー メッセージが表示される

使用しているアカウントがアカウント管理者であるかどうかを確認します。 どのアカウントがアカウント管理者であるかを確認するには、以下の手順に従ってください。

1. Azure Portal の[サブスクリプション ビュー](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にサインインします。
1. 確認するサブスクリプションを選択し、**[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。  

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
