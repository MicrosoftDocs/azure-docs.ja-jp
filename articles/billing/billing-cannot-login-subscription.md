---
title: "Azure サブスクリプションにサインインできない | Microsoft Azure"
description: "Azure サブスクリプションのログインに関する一般的な問題をトラブルシューティングする方法について説明します。"
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
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: fc1c8cb6b7c186e189658cacf218ec7e9261472a
ms.lasthandoff: 02/08/2017


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Azure サブスクリプションにサインインして管理することができない
この記事では、ログインに関する問題の最も一般的な解決方法を紹介します。

## <a name="page-hangs-in-the-loading-status"></a>ページの読み込み中にハングする
インターネット ブラウザーのページがハングする場合、[Azure Portal](https://portal.azure.com) にアクセスできるまで、次の各手順を試してみてください。

* ページを更新します。
* 別のインターネット ブラウザーを使用します。
* Microsoft Internet Explorer を使用している場合は、InPrivate ブラウズ モードを使用して Azure Portal にアクセスします。 
  
  A.    **[ツール]**ツール ボタン![](./media/billing-cannot-login-subscription/Toolsbutton.png) > **[セーフティ]** > **[InPrivate ブラウズ]** の順にクリックします。
  
  B.    [Azure Portal](https://portal.azure.com) にアクセスしてサインインします。

## <a name="error-message-no-subscriptions-found"></a>"サブスクリプションが見つかりません" エラー メッセージが表示される
アカウントに必要なアクセス許可がない場合、"**サブスクリプションが見つかりません**" というエラー メッセージが表示されることがあります。 適切な管理者としてログインしていることを確認してください。 アカウント管理者は[アカウント センター](https://account.windowsazure.com/Subscriptions)のみにアクセスできます。 サービス管理者 (SA) と共同管理者 (CA) がアクセスできるのは [Azure Portal](https://portal.azure.com) または Azure クラシック ポータルのみです。

**シナリオ 1: [Azure Portal](https://portal.azure.com) でこのエラー メッセージが表示された場合**

この問題を解決するには、アカウントに [共同管理者または所有者のロールを追加します](billing-add-change-azure-subscription-administrator.md) 。

**シナリオ 2: [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でこのエラー メッセージが表示された場合**

使用しているアカウントがアカウント管理者であるかどうかを確認します。 どのアカウントがアカウント管理者であるかを確認するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com)にサインインします。
2. ハブ メニューで、 **[サブスクリプション]**を選択します。
3. 確認するサブスクリプションを選択し、 **[設定]**を選択します。
4. **[プロパティ]**を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>サインイン時に自動で別のユーザーになる
この問題は、インターネット ブラウザーで複数のユーザー アカウントを使用している場合に発生する可能性があります。

この問題を解決するには、次の方法のいずれかを試してください。

* キャッシュをクリアし、インターネット Cookie を削除する。 Internet Explorer で、**[ツール]** ![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png) > **[インターネット オプション]** > **[削除]** の順にクリックします。 一時ファイル、Cookie、パスワード、閲覧履歴の各チェック ボックスをオンにして [削除] をクリックします。
* Internet Explorer の設定をリセットして、構成済みの個人設定を元に戻す。 **[ツール]** ![ツール ボタン](./media/billing-cannot-login-subscription/Toolsbutton.png) > **[インターネット オプション]** > **[詳細設定]** の順にクリックし、**[個人設定を削除する]** チェック ボックスをオンにして **[リセット]** を選択します。
* InPrivate ブラウズ モードで Azure Portal にアクセスする。 **[ツール]**ツール ボタン![](./media/billing-cannot-login-subscription/Toolsbutton.png) > **[セーフティ]** > **[InPrivate ブラウズ]** の順にクリックします。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。 


