---
title: 2 段階認証設定の管理 - Azure Active Directory | Microsoft Docs
description: 連絡先情報の変更やデバイスの構成などの Azure Multi-Factor Authentication の使用方法を管理します。
services: active-directory
keywords: 多要素認証のクライアント, 認証の問題, 関連付け ID
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334490"
---
# <a name="manage-your-settings-for-two-step-verification"></a>2 段階認証設定の管理
この記事では、2 段階認証または多要素認証の設定の更新方法に関する質問に回答しています。 アカウントへのサインインで問題が発生している場合のトラブルシューティング ヘルプについては、「[Having trouble with two-step verification (2 段階認証で問題が発生した場合)](multi-factor-authentication-end-user-troubleshoot.md)」をご覧ください。

## <a name="where-to-find-the-settings-page"></a>設定ページの場所
会社の Azure Multi-Factor Authentication の設定方法に応じて、電話番号などの設定を変更できる場所がいくつかあります。

社内のサポートが 2 段階認証を管理するための特定の URL または手順を送信した場合は、その指示に従ってください。 それ以外の場合は、他のすべてのユーザーが次の手順を実行する必要があります。 次の手順に従っても同じオプションが表示されない場合は、会社や学校で独自のポータルをカスタマイズしていると考えられます。 Azure Multi-factor Authentication ポータルへのリンクについては、管理者にお問い合わせください。

**[追加のセキュリティ確認] ページに移動するには**

- [https://aka.ms/MFASetup](https://aka.ms/MFASetup ) にアクセスします。

    ![追加のセキュリティ確認](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

このリンクをクリックしても機能しない場合は、以下の手順に従って **[追加のセキュリティ確認]** ページにアクセスすることもできます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。  

2. 右上でアカウント名を選択し、**プロファイル**を選択します。

3. **[追加のセキュリティ確認]** を選択します。  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. [追加のセキュリティ確認] ページに設定が読み込まれます。

    ![追加のセキュリティ確認](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>電話番号を変更または追加する場合
セカンダリ認証用電話番号を構成することは重要です。  1 つ目の電話番号の携帯電話にモバイル アプリがインストールされている場合が多いため、電話の紛失や盗難が起きた場合に最もすばやくアカウントに再度サインインできる手段は、2 つ目の電話番号を使用する方法になります。

> [!NOTE]
> 1 つ目の電話番号にアクセスできず、アカウントへのサインインでヘルプが必要な場合は、「[Having trouble with two-step verification](multi-factor-authentication-end-user-troubleshoot.md)」(2 段階認証で問題が発生した場合) を参照してください。  

**代表電話番号を変更するには:**  

1. **[追加のセキュリティ確認]** ページで、現在の電話番号のテキスト ボックスを選択し、新しい電話番号を入力します。  
2. **[保存]** を選択します。  
3. この電話番号が確認オプションに使用する番号である場合は、保存する前にその新しい番号を確認する必要があります。  

**予備の電話番号を追加するには:**  

1. [追加のセキュリティ確認] ページで、 **[代替の認証用電話]** の横のチェック ボックスをオンにします。  
2. 予備の話番号をテキスト ボックスに入力します。  
3. **[保存]** を選択すると、変更が完了します。  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>信頼済みとしてマークされているデバイスで 2 段階認証を再び要求する場合

組織の設定によっては、ブラウザーで 2 段階認証を実行するときに、"今後 **X** 日間はこのメッセージを表示しない" というチェック ボックスが表示される場合があります。 このボックスをオンにした後、デバイスを紛失した場合やアカウントが侵害されたと思われる場合は、すべてのデバイスに 2 段階認証を復元する必要があります。

1. [追加のセキュリティ確認] ページで、 **[以前の信頼済みデバイスに Multi-Factor Authentication を復元する]** を選択します。
2. 任意のデバイスで次回サインインするときに、2 段階認証を実行するよう求められます。

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>古いデバイスから Microsoft Authenticator をクリーンアップして新しいデバイスに移行する場合
デバイスからアプリをアンインストールしたり、デバイスをリセットしても、アクティブ化はバック エンドで削除されません。 詳細については、「[Microsoft Authenticator](user-help-auth-app-download-install.md)」参照してください。

## <a name="next-steps"></a>次の手順
* 「[Having trouble with two-step verification (2 段階認証で問題が発生した場合)](multi-factor-authentication-end-user-troubleshoot.md)」でトラブルシューティングのヒントとヘルプを習得します。
* 2 段階認証をサポートしていないアプリに[アプリ パスワード](multi-factor-authentication-end-user-app-passwords.md)を設定します。
