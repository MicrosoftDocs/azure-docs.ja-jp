---
title: Azure AD のセルフ サービスによるパスワードのリセット Windows 7 および 8.1 - Azure Active Directory
description: Windows 7 または 8.1 のログオン画面で「パスワードを忘れた場合」を使用してセルフ サービスによるパスワードのリセットを有効にする方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d3e955059724756eb7102c1b9fbbf55ed203ab
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370450"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>方法:Windows 7、8、8.1 からパスワードのリセットを有効にする

管理者がセルフ サービスによるパスワードのリセット (SSPR) を有効にしていても、ユーザーはブラウザー ウィンドウで [SSPR ポータル](https://aka.ms/sspr)にアクセスすることができないため、ヘルプデスクには引き続きパスワードをリセットするよう電話がかかってきます。 Windows 10 マシンでは、チュートリアル「[ログイン画面からの Azure AD パスワード リセット](tutorial-sspr-windows.md)」を使用して、ログオン画面で「パスワードのリセット」リンクを有効にすることができます。次のガイダンスは、Windows 7、8、および 8.1 のユーザーが Windows ログオン画面で SSPR を使用してパスワードをリセットできるようにするのに役立ちます。

Windows 10 マシンとは異なり、Windows 7、8、および 8.1 マシンには、パスワードのリセットのための Azure AD ドメイン参加済み (Active Directory ドメイン参加済み) 要件はありません。

![「パスワードを忘れた場合」が表示されている Windows 7 ログオン画面の例 表示されるリンク](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>前提条件

* Azure AD のセルフ サービス パスワード リセットを有効にする必要があります。
* パッチが適用された Windows 7 または Windows 8.1 オペレーティング システム。
* [トランスポート層セキュリティ (TLS) レジストリ設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)にあるガイダンスを使用して有効にした TLS 1.2。

> [!WARNING]
> TLS 1.2 は、自動ネゴシエートするように設定されているだけでなく、有効になっている必要があります。

## <a name="install"></a>Install

1. 有効にしたい Windows バージョン用の適切なインストーラーをダウンロードします。

   1. ソフトウェアは [https://aka.ms/sspraddin](https://aka.ms/sspraddin) にある Microsoft ダウンロード センターで入手できます

1. インストールしたいマシンにサインインし、インストーラーを実行します。
1. インストール後に再起動することを強くお勧めします。
1. 再起動後に、ログオン画面でユーザーを選択し、「パスワードを忘れた場合」をクリックして、 パスワード リセットのワークフローを開始します。
1. 画面に表示される手順に従ってワークフローを完了すると、パスワードがリセットされます。

![Windows 7 で「パスワードを忘れた場合」をクリックしたときの例 SSPR フロー](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>サイレント インストール

* サイレント インストールの場合は、コマンド "msiexec/i SsprWindowsLogon.PROD.msi/qn" を使用します
* サイレント アンインストールの場合は、コマンド "msiexec/x SsprWindowsLogon.PROD.msi/qn" を使用します

## <a name="caveats"></a>注意事項

「パスワードを忘れた場合」リンクを使用できるようにするには、SSPR 用に登録する必要があります。

![パスワードをリセットするための追加のセキュリティ情報が必要です](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Microsoft Authenticator アプリを使用した通知とコードでのパスワードのリセットは、この初期リリースでは機能しません。 ユーザーには、ポリシーの要件を満たす代替の方法が登録されている必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

イベントは、マシンと Azure AD の両方でログ記録されます。

Azure AD イベントには、パスワードのリセットが発生した IP アドレスと ClientType に関する情報が含められます。

![Azure AD 監査ログにおける Windows 7 のパスワードのリセットの例](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

追加のログ記録が必要な場合は、詳細ログ記録を有効にするようにマシンのレジストリ キーを変更できます。 詳細ログ記録は、トラブルシューティングの目的でのみ有効にしてください。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

* 詳細ログ記録を有効にするには、REG_DWORD:"EnableLogging" を作成して 1 に設定します。
* 詳細ログ記録を無効にするには、REG_DWORD "EnableLogging" を 0 に変更します。

お使いの Windows 7、8、および 8.1 コンピューターがプロキシ サーバーまたはファイアウォールの内側にある場合は、passwordreset.microsoftonline.com への HTTPS トラフィック (443) を許可する必要があります。

## <a name="next-steps"></a>次の手順

* [Windows 10 ユーザーがログオン画面でパスワードをリセットできるようにする](tutorial-sspr-windows.md)
