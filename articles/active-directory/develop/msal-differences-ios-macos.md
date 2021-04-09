---
title: iOS と macOS での MSAL の違い | Azure
titleSuffix: Microsoft identity platform
description: iOS と macOS での Microsoft Authentication Library (MSAL) の使用方法の違いについて説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064917"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>iOS 用と macOS 用の Microsoft Authentication Library の違い

この記事では、iOS および macOS 用の Microsoft Authentication Library (MSAL) の機能の違いについて説明します。

> [!NOTE]
> Mac では、MSAL は macOS アプリのみをサポートします。

## <a name="general-differences"></a>一般的な違い

macOS 用の MSAL は、iOS で使用できる機能のサブセットです。

macOS 用の MSAL は、以下をサポートしていません。

- `ASWebAuthenticationSession`、`SFAuthenticationSession`、`SFSafariViewController` などのブラウザーの種類。
- Microsoft Authenticator アプリを使用した仲介型認証は、macOS ではサポートされていません。

同じ発行元のアプリ間でのキーチェーン共有は、macOS 10.14 以前ではさらに制限されます。 キーチェーンを共有する必要があるアプリへのパスを指定するには、[アクセス制御リスト](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc)を使用します。 ユーザーには、追加のキーチェーン プロンプトが表示される場合があります。

macOS 10.15 以降では、MSAL の動作は iOS と macOS で同じです。 MSAL は、キーチェーン共有に[キーチェーン アクセス グループ](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)を使用します。 

### <a name="conditional-access-authentication-differences"></a>条件付きアクセス認証の違い

条件付きアクセスのシナリオで iOS 用の MSAL を使用する場合、ユーザーへのプロンプトは少なくなります。 これは、状況によってはユーザーへのプロンプトが不要になるブローカー アプリ (Microsoft Authenticator) を iOS が使用するためです。

### <a name="project-setup-differences"></a>プロジェクトの設定の違い

**macOS**

- macOS でプロジェクトを設定するときは、アプリケーションが有効な開発証明書または運用証明書で署名されていることを確認してください。 MSAL は未署名モードでも動作しますが、キャッシュの永続化に関する動作が変わります。 アプリを署名なしで実行するのは、デバッグ目的のみである必要があります。 署名されていないアプリを配布すると、次のようになります。
1. 10.14 以前では、アプリを再起動するたびに MSAL からキーチェーン パスワードの入力が求められます。
2. 10.15 以降では、トークンを取得するたびに資格情報の入力が求められます。 

- macOS アプリでは、AppDelegate 呼び出しを実装する必要はありません。

**iOS**

- 認証ブローカー フローをサポートするためのプロジェクトを追加で設定する必要があります。 その手順については、チュートリアルで説明しています。
- iOS プロジェクトでは、カスタム スキームを info.plist に登録する必要があります。 これは macOS では必要ありません。
