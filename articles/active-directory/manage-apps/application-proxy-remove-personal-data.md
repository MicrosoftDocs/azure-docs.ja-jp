---
title: 個人データの削除 - Azure Active Directory アプリケーション プロキシ
description: Azure Active Directory アプリケーション プロキシのデバイスにインストールされているコネクタから個人データを削除します。
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258950"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシの個人データの削除

Azure Active Directory アプリケーション プロキシでは、デバイスにコネクタをインストールする必要があります。つまり、デバイスに個人データが存在する可能性があります。 この記事では、個人データを削除してプライバシーを向上させる方法について説明します。

## <a name="where-is-the-personal-data"></a>個人データの場所

アプリケーション プロキシでは、次の種類のログに個人データを書き込むことができます。

- コネクタ イベント ログ
- Windows イベント ログ

## <a name="remove-personal-data-from-windows-event-logs"></a>Windows イベント ログから個人データを削除する

Windows イベント ログのデータ保有期間を構成する方法については、「[Settings for event logs (イベント ログの設定)](https://technet.microsoft.com/library/cc952132.aspx)」をご覧ください。 Windows イベント ログについては、「[Using Windows Event Log (Windows イベント ログの使用)](/windows/win32/wes/using-windows-event-log)」をご覧ください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>コネクタ イベント ログから個人データを削除する

アプリケーション プロキシのログに個人データが含まれないようにするには、次のいずれかを実行します。

- 必要に応じてデータを削除または表示する
- ログ記録をオフにする

以下のセクションを参照して、コネクタ イベント ログから個人データを削除します。 コネクタがインストールされているすべてのデバイスで、削除プロセスを実行する必要があります。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>特定のデータを表示またはエクスポートする

特定のデータを表示またはエクスポートするには、各コネクタ イベント ログで関連エントリを検索します。 ログは、`C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` にあります。

ログはテキスト ファイルであるため、[findstr](/windows-server/administration/windows-commands/findstr) を使用してユーザーに関連するテキスト エントリを検索できます。  

個人データを見つけるには、ログ ファイルで ユーザー ID を検索します。

Kerberos の制約付き委任を使用するアプリケーションによってログに記録された個人データを見つけるには、ユーザー名の種類の次の構成要素を検索します。

- オンプレミスのユーザー プリンシパル名
- ユーザー プリンシパル名のユーザー名部分
- オンプレミスのユーザー プリンシパル名のユーザー名部分
- オンプレミスのセキュリティ アカウント マネージャー (SAM) のアカウント名

### <a name="delete-specific-data"></a>特定のデータを削除する

特定のデータを削除するには、次の手順に従います。

1. Microsoft Azure AD アプリケーション プロキシ コネクタ サービスを再起動して、新しいログ ファイルを生成します。 新しいログ ファイルを生成すると、古いログ ファイルを削除または変更できるようになります。 
1. 前述の[特定のデータを表示またはエクスポートする](#view-or-export-specific-data)プロセスに従って、削除する必要がある情報を見つけます。 すべてのコネクタ ログで検索します。
1. 関連するログ ファイルを削除するか、個人データが含まれたフィールドを選択して削除します。 不要になった場合は、古いログ ファイルをすべて削除することもできます。

### <a name="turn-off-connector-logs"></a>コネクタ ログをオフにする

コネクタ ログに個人データが含まれないようにする 1 つの方法として、ログの生成をオフにします。 コネクタ ログの生成を停止するには、`C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` から、次の強調表示されている行を削除します。

![削除すべき強調表示されたコードを含むコード スニペットを示します](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>次のステップ

アプリケーション プロキシの概要については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](application-proxy.md)」をご覧ください。