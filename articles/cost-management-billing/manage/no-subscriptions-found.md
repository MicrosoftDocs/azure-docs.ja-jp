---
title: "\"サブスクリプションが見つかりません\" エラー - Azure Portal へのサインイン"
description: Azure Portal または Azure アカウント センターにサインインしようとすると「サブスクリプションが見つかりません」エラーになるという問題の解決策を示します。
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 70f479148cb2f5f3f2b13c431e0c4b80b1fb9543
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684832"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Azure Portal または Azure アカウント センターにおける「サブスクリプションが見つかりません」のサインイン エラー

[Azure Portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されることがあります。 この記事では、この問題の解決策について説明します。

## <a name="symptom"></a>症状

[Azure Portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されます。

## <a name="cause"></a>原因

この問題は、誤ったディレクトリで選択した場合、または、自分のアカウントに十分なアクセス許可がない場合に発生します。

## <a name="solution"></a>解決策

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>シナリオ 1:[Azure Portal](https://portal.azure.com) でエラー メッセージが表示される

この問題を解決するには、次の手順に従います。

* 右上にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。

  ![Azure Portal の右上にあるディレクトリを選択する](./media/no-subscriptions-found/directory-switch.png)
* 適切な Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、[アカウントに所有者ロールを割り当て](../../role-based-access-control/role-assignments-portal.md)ます。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>シナリオ 2: [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でエラー メッセージが表示される

使用しているアカウントがアカウント管理者であるかどうかを確認します。 どのアカウントがアカウント管理者であるかを確認するには、以下の手順に従ってください。

1. Azure Portal の[サブスクリプション ビュー](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にサインインします。
1. 確認するサブスクリプションを選択し、 **[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。  

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
