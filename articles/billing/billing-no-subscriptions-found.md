---
title: "Azure Portal または Azure アカウント センターにサインインしようとすると「サブスクリプションが見つかりません」エラーになる | Microsoft Docs"
description: "Azure Portal または Azure アカウント センターにサインインしようとすると「サブスクリプションが見つかりません」エラーになるという問題の解決策を示します。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Azure Portal または Azure アカウント センターでの「サブスクリプションが見つかりません」エラー
[Azure Portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」エラー メッセージが表示されることがあります。 この記事では、この問題の解決策について説明します。

## <a name="symptom"></a>症状

[Azure Portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されます。

## <a name="cause"></a>原因

この問題は、自分のアカウントに十分なアクセス許可がない場合に発生します。 

## <a name="solution"></a>解決策

適切な管理者としてログインしていることを確認してください。 アカウント管理者はアカウント センターにのみアクセスできます。 サービス管理者 (SA) と共同管理者 (CA) は Azure Portal または Azure クラシック ポータルに対してのみアクセス許可を持ちます。

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>シナリオ 1: [Azure Portal](https://portal.azure.com) でエラー メッセージが表示される

この問題を解決するには、次の手順に従います。

* 右上にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。

  ![Azure Portal の右上にあるディレクトリを選択する](./media/billing-no-subscriptions-found/directory-switch.png)

* 適切な Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、[アカウントを所有者として追加](billing-add-change-azure-subscription-administrator.md)します。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>シナリオ 2: [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でエラー メッセージが表示される

使用しているアカウントがアカウント管理者であるかどうかを確認します。 どのアカウントがアカウント管理者であるかを確認するには、以下の手順に従ってください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、 **[サブスクリプション]**を選択します。
3. 確認するサブスクリプションを選択し、 **[設定]**を選択します。
4. **[プロパティ]**を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。 

