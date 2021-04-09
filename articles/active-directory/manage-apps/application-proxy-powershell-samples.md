---
title: Azure AD アプリケーション プロキシのための PowerShell サンプル
description: Azure AD アプリケーション プロキシのためのこれらの PowerShell サンプルを使用して、ディレクトリ内のアプリケーション プロキシ アプリとコネクタに関する情報を取得し、ユーザーとグループをアプリに割り当て、証明書情報を取得します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeff14e1676389eb2204cad45ea7b00d5aa9796b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566653"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシのための Azure AD PowerShell の例

次の表には、Azure AD アプリケーション プロキシのための PowerShell スクリプトの例へのリンクが含まれています。 これらのサンプルには、特に明記されていない限り、[Graph 用 AzureAD V2 PowerShell モジュール](/powershell/azure/active-directory/install-adv2)または [Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)が必要です。


これらのサンプルで使用されるコマンドレットの詳細については、「[アプリケーション プロキシ アプリケーションの管理](/powershell/module/azuread/#application_proxy_application_management)」および「[アプリケーション プロキシ コネクタの管理](/powershell/module/azuread/#application_proxy_connector_management)」を参照してください。

| Link | 説明 |
|---|---|
|**アプリケーション プロキシ アプリ**||
| [すべてのアプリケーション プロキシ アプリに関する基本情報を一覧表示する](scripts/powershell-get-all-app-proxy-apps-basic.md) | ディレクトリ内のすべてのアプリケーション プロキシ アプリに関する基本情報 (AppId、DisplayName、ObjId) を一覧表示します。 |
| [すべてのアプリケーション プロキシ アプリに関する拡張情報を一覧表示する](scripts/powershell-get-all-app-proxy-apps-extended.md) | ディレクトリ内のすべてのアプリケーション プロキシ アプリに関する拡張情報 (AppId、DisplayName、ExternalUrl、InternalUrl、ExternalAuthenticationType) を一覧表示します。  |
| [すべてのアプリケーション プロキシ アプリをコネクタ グループ別に一覧表示する](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | ディレクトリ内のすべてのアプリケーション プロキシ アプリに関する情報と、どのコネクタ グループにアプリが割り当てられているかを一覧表示します。 |
| [トークンの有効期間ポリシーを使用しているすべてのアプリケーション プロキシ アプリを取得する](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | トークンの有効期間ポリシーを使用しているディレクトリ内のすべてのアプリケーション プロキシ アプリを、ポリシーの詳細と共に一覧表示します。 このサンプルには、[Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)が必要です。 |
|**コネクタ グループ**||
| [ディレクトリ内のすべてのコネクタ グループとコネクタを取得する](scripts/powershell-get-all-connectors.md) | ディレクトリ内のすべてのコネクタ グループとコネクタを一覧表示します。 |
| [あるコネクタ グループに割り当てられているすべてのアプリを別のコネクタ グループに移動する](scripts/powershell-move-all-apps-to-connector-group.md) | あるコネクタ グループに現在割り当てられているすべてのアプリケーションを別のコネクタ グループに移動します。 |
|**割り当てられたユーザーとグループ**||
| [アプリケーション プロキシ アプリケーションに割り当てられているユーザーとグループを表示する](scripts/powershell-display-users-group-of-app.md) | 特定のアプリケーション プロキシ アプリケーションに割り当てられているユーザーとグループを一覧表示します。 |
| [アプリケーションにユーザーを割り当てる](scripts/powershell-assign-user-to-app.md) | 特定のユーザーをアプリケーションに割り当てます。 |
| [アプリケーションにグループを割り当てる](scripts/powershell-assign-group-to-app.md) | 特定のグループをアプリケーションに割り当てます。 |
|**外部 URL 構成**||
| [既定のドメイン (.msappproxy.net) を使用してすべてのアプリケーション プロキシ アプリを取得する](scripts/powershell-get-all-default-domain-apps.md)  | 既定のドメイン (.msappproxy.net) を使用しているすべてのアプリケーション プロキシ アプリケーションを一覧表示します。 |
| [ワイルドカードによる公開を使用しているすべてのアプリケーション プロキシ アプリを取得する](scripts/powershell-get-all-wildcard-apps.md) | ワイルドカードによる公開を使用しているすべてのアプリケーション プロキシ アプリを一覧表示します。 |
|**カスタム ドメイン構成**||
| [カスタム ドメインを使用しているすべてのアプリケーション プロキシ アプリと証明書情報を取得する](scripts/powershell-get-all-custom-domains-and-certs.md) | カスタム ドメインを使用しているすべてのアプリケーション プロキシ アプリと、カスタム ドメインに関連付けられている証明書情報を一覧表示します。 |
| [証明書をアップロードせずに公開されたすべての Azure AD プロキシ アプリケーション アプリを取得する](scripts/powershell-get-all-custom-domain-no-cert.md) | カスタム ドメインを使用しているが、有効な TLS/SSL 証明書がアップロードされていないすべてのアプリケーション プロキシ アプリを一覧表示します。 |
| [同一の証明書を使用して公開されたすべての Azure AD プロキシ アプリケーション アプリを取得する](scripts/powershell-get-custom-domain-identical-cert.md) | 同一の証明書を使用して公開されたすべての Azure AD プロキシ アプリケーション アプリを一覧表示します。 |
| [同じ証明書を使用して公開されたすべての Azure AD プロキシ アプリケーション アプリを取得して置き換える](scripts/powershell-get-custom-domain-replace-cert.md) | 同じ証明書を使用して公開された Azure AD プロキシ アプリケーション アプリを対象に、証明書を一括で置き換えることができます。 |
