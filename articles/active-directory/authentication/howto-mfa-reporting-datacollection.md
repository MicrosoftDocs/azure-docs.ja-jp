---
title: Azure MFA ユーザー データの収集 - Azure Active Directory
description: Azure Multi-Factor Authentication によるユーザーの認証に役立つ情報としてどの情報が使用されますか。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bc0f0dbcd08df887b2484be6ca8c92a85962c1c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848291"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Azure Multi-Factor Authentication によるユーザー データの収集

このドキュメントでは、Azure Multi-Factor Authentication Server (MFA Server) と Azure MFA (クラウドベース) によって収集されたユーザー情報を削除する場合にその情報を見つける方法について説明します。

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>収集される情報

MFA Server、NPS 拡張機能、および Windows Server 2016 Azure MFA AD FS アダプターは、次の情報を収集して 90 日間保存します。

認証の試行 (レポートとトラブルシューティングに使用):

- Timestamp
- ユーザー名
- 名
- 姓
- 電子メール アドレス
- ユーザー グループ
- 認証方法 (電話、テキスト メッセージ、モバイル アプリ、OATH トークン)
- 電話呼び出しモード (標準、PIN)
- テキスト メッセージの方向 (一方向、双方向)
- テキスト メッセージのモード (OTP、OTP + PIN)
- モバイル アプリのモード (標準、PIN)
- OATH トークンのモード (標準、PIN)
- 認証の種類
- アプリケーション名
- メインの電話番号の国番号
- メインの電話番号
- メインの電話番号の内線番号
- 認証されたメインの電話番号
- メインの電話番号の呼び出し結果
- 予備の電話番号の国番号
- 予備の電話番号
- 予備の電話番号の内線番号
- 認証された予備の電話番号
- 予備の電話番号の呼び出し結果
- 全体の認証
- 全体の結果
- 結果
- 認証済み
- 結果
- 発信 IP アドレス
- デバイス
- デバイス トークン
- デバイスの種類
- モバイル アプリのバージョン
- OS バージョン
- 結果
- 使用された通知の確認

アクティブ化 (Microsoft Authenticator モバイル アプリでアカウントをアクティブ化する試行):
- ユーザー名
- アカウント名
- Timestamp
- アクティブ化コードの取得の結果
- アクティブ化の成功
- アクティブ化のエラー
- アクティブ化の状態の結果
- デバイス名
- デバイスの種類
- アプリのバージョン
- OATH トークンが有効

ブロック (ブロックされた状態の判定とレポートのために使用):

- ブロックのタイムスタンプ
- ユーザー名によるブロック
- ユーザー名
- 国番号
- 電話番号
- 書式化された電話番号
- 拡張機能
- クリーンな内線番号
- Blocked
- ブロックした理由
- 完了のタイムスタンプ
- 完了理由
- アカウントのロックアウト
- 不正アクセスのアラート
- ブロックされなかった不正アクセス アラート
- 言語

バイパス (レポートに使用):

- バイパスのタイムスタンプ
- バイパスの秒数
- ユーザー名によるバイパス
- ユーザー名
- 国番号
- 電話番号
- 書式化された電話番号
- 拡張機能
- クリーンな内線番号
- バイパスの理由
- 完了のタイムスタンプ
- 完了理由
- バイパスの使用

変更 (MFA サーバーまたは Azure AD にユーザーの変更を同期するために使用):

- 変更のタイムスタンプ
- ユーザー名
- 新しい国番号
- 新しい電話番号
- 新しい内線番号
- 新しい予備の電話番号の国番号
- 新しい予備の電話番号
- 新しい予備の内線番号
- 新しい PIN
- PIN の変更が必要
- 古いデバイス トークン
- 新しいデバイス トークン

## <a name="gather-data-from-mfa-server"></a>MFA Server からのデータの収集

MFA Server バージョン 8.0 以降では、管理者は、次の手順に従ってユーザーのすべてのデータをエクスポートできます。

- MFA Server にログインし、 **[ユーザー]** タブに移動します。目的のユーザーを選択し、 **[編集]** ボタンをクリックします。 (Alt + PrtScn キーを押して) 各タブのスクリーンショットを取ると、ユーザーに現在の MFA 設定を提供できます。
- MFA Server のコマンド ラインから次のコマンドを実行して、JSON 形式のファイルを生成します (インストールに合わせてパスを変更してください)。`C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>`
- 管理者は、Web サービス SDK の GetUserGdpr 操作をオプションとして使用して、特定のユーザー用に収集されたすべての MFA クラウド サービス情報をエクスポートしたり、より大きなレポート ソリューションに組み込んだりすることもできます。
- `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` とすべてのバックアップ内で "\<username> " を検索して (検索に引用符を含めます)、追加または変更されたユーザー レコードのすべてのインスタンスを検索します。
   - これらのレコードは、MFA Server UX の [Logging]\(ログの記録\) セクションの [ログ ファイル] タブで **[ユーザー変更をログ ファイルに記録する]** チェックボックスをオフにすることによって制限できます (ただし、削除することはできません)。
   - syslog が構成されていて、MFA Server UX の [Logging]\(ログの記録\) セクションの [Syslog] タブで **[ユーザー変更をログ ファイルに記録する]** チェックボックスがオンになっている場合は、代わりに syslog からログ エントリを収集できます。
- MultiFactorAuthSvc.log と認証試行に関連する他の MFA Server ログ ファイル内のユーザー名の他の出現箇所は運用データと見なされ、MultiFactorAuthGdpr.exe エクスポートまたは Web サービス SDK の GetUserGdpr を使用して提供される情報と重複していると見なされます。

## <a name="delete-data-from-mfa-server"></a>MFA Server からのデータの削除

MFA Server のコマンド ラインから次のコマンドを実行して、このユーザー用に収集されたすべての MFA クラウド サービス情報を削除します (インストールに合わせてパスを変更してください)。`C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>`

- エクスポートに含まれるデータはリアルタイムで削除されますが、運用データまたは重複したデータが完全に削除されるまでに最大で 30 日かかる場合があります。
- 管理者は、Web サービス SDK の DeleteUserGdpr 操作をオプションとして使用して、特定のユーザー用に収集されたすべての MFA クラウド サービス情報を削除したり、より大きなレポート ソリューションに組み込んだりすることもできます。

## <a name="gather-data-from-nps-extension"></a>NPS 拡張機能からのデータの収集

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用して、エクスポートを要求します。

- MFA 情報はエクスポートに含まれます。この操作が完了するまでに数時間または数日かかることがあります。
- AzureMfa/AuthN/AuthNOptCh、AzureMfa/AuthZ/AuthZAdminCh、および AzureMfa/AuthZ/AuthZOptCh イベント ログ内に出現するユーザー名は運用データと見なされ、エクスポートで提供される情報と重複していると見なされます。

## <a name="delete-data-from-nps-extension"></a>NPS 拡張機能からのデータの削除

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用してアカウントの削除要求を行って、このユーザー用に収集されたすべての MFA クラウド サービス情報を削除します。

- データが完全に削除されるまでには最大で 30 日かかる場合があります。

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Windows Server 2016 Azure MFA AD FS アダプターからのデータの収集

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用して、エクスポートを要求します。 

- MFA 情報はエクスポートに含まれます。この操作が完了するまでに数時間または数日かかることがあります。
- AD FS Tracing/Debug イベント ログ (有効な場合) 内に出現するユーザー名は運用データと見なされ、エクスポートで提供される情報と重複していると見なされます。

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Windows Server 2016 Azure MFA AD FS アダプターからのデータの削除

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用してアカウントの削除要求を行って、このユーザー用に収集されたすべての MFA クラウド サービス情報を削除します。

- データが完全に削除されるまでには最大で 30 日かかる場合があります。

## <a name="gather-data-for-azure-mfa"></a>Azure MFA のデータの収集

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用して、エクスポートを要求します。

- MFA 情報はエクスポートに含まれます。この操作が完了するまでに数時間または数日かかることがあります。

## <a name="delete-data-for-azure-mfa"></a>Azure MFA のデータの削除

[Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) を使用してアカウントの削除要求を行って、このユーザー用に収集されたすべての MFA クラウド サービス情報を削除します。

- データが完全に削除されるまでには最大で 30 日かかる場合があります。

## <a name="next-steps"></a>次の手順

[MFA Server のレポート](howto-mfa-reporting.md)
