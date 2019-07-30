---
title: セキュリティ情報 (プレビュー) のメソッドと情報の変更に関する概要 - Azure Active Directory | Microsoft Docs
description: 2 要素認証およびセルフサービスのパスワード リセットに対するセキュリティ情報メソッドを追加、更新、および削除する方法についての概要です。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: olhaun
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bcef64c8bf8c2b52e5cc5d954de933d830ba4c1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382890"
---
# <a name="overview-about-changing-your-security-info-preview-methods-and-information"></a>セキュリティ情報 (プレビュー) のメソッドと情報の変更に関する概要

**[セキュリティ情報]** ページ内から、既定のセキュリティ情報メソッドを追加、編集、削除、変更できます。 各メソッドの詳細および情報を変更する方法については、関連するメソッドごとの記事をご覧ください。

## <a name="two-factor-verification-versus-password-reset-authentication"></a>2 要素認証とパスワードのリセット認証

セキュリティ情報メソッドは、2 要素認証とパスワードのリセットの両方に使用されます。 ただし、すべてのメソッドを両方に使用できるわけではありません。

| Method | 使用対象 |
| ------ | -------- |
| Authenticator アプリ | 2 要素認証とパスワードのリセット認証。 |
| テキスト メッセージ | 2 要素認証とパスワードのリセット認証。 |
| 電話 | 2 要素認証とパスワードのリセット認証。 |
| 電子メール アカウント | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |
| セキュリティの質問 | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |

## <a name="method-based-how-to-articles"></a>メソッドごとの方法に関する記事

次の一覧では、メソッドごとの記事を示します。 各記事では、指定されているメソッドに基づいてセキュリティ情報を追加、編集、削除、または変更する方法についての詳細が提供されています。

| 記事 | 説明 |
| ------ |------------ |
| [認証アプリ用にセキュリティ情報を設定する](security-info-setup-auth-app.md) | Microsoft Authenticator アプリにセキュリティ情報メソッドを変更する方法について説明します。 |
| [テキスト メッセージ用にセキュリティ情報を設定する](security-info-setup-text-msg.md) | テキスト メッセージにセキュリティ情報メソッドを変更する方法について説明します。 |
| [電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md) | 電話呼び出しにセキュリティ情報メソッドを変更する方法について説明します。 |
| [メールを使用するようにセキュリティ情報を設定する](security-info-setup-email.md) | メール アドレスを使用するようにセキュリティ情報メソッドを変更する方法について説明します。 |
| [あらかじめ定義されたセキュリティの質問を使用するようにセキュリティ情報を設定する](security-info-setup-questions.md)|セキュリティの質問を使用するようにセキュリティ情報メソッドを変更する方法について説明します。 |
