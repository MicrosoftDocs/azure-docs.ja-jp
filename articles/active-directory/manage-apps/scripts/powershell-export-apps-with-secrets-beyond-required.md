---
title: PowerShell のサンプル - Azure Active Directory テナントで必要な日付を過ぎた後に期限が切れるシークレットと証明書を含むアプリをエクスポートします。
description: Azure Active Directory テナント内の指定したアプリについて、必要な日付を過ぎた後に期限が切れるシークレットと証明書を含むすべてのアプリをエクスポートする PowerShell サンプル。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 9c0e5508830343561833785fbce31f547a8a7428
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149683"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>必要な日付を過ぎた後に期限が切れるシークレットと証明書を含むアプリをエクスポートする

この PowerShell スクリプトの例では、指定したアプリについて、必要な期間が過ぎた後に期限切れになるすべてのアプリ登録シークレットと証明書をディレクトリから CSV ファイルに非対話形式でエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは非対話形式で動作しています。 これを使用する管理者は、"#PARAMETERS TO CHANGE" セクションの値を、実際のアプリ ID、アプリケーション シークレット、テナント名、アプリの資格情報の有効期限、および CSV がエクスポートされるパスに変更する必要があります。
このスクリプトでは、[Oauth フロー Client_Credential](../../develop/v2-oauth2-client-creds-grant-flow.md) を使用します。"RefreshToken" 関数は、管理者によって変更されたパラメーターの値に基づいてアクセス トークンを作成します。

"Add-Member" コマンドは、CSV ファイル内に列を作成する役割を担います。

| コマンド | Notes |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) | Web ページまたは Web サービスに HTTP 要求と HTTPS 要求を送信します。 これは、応答を解析し、リンク、画像、およびその他の重要な HTML 要素のコレクションを返します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション管理のためのその他の PowerShell の例については、[アプリケーション管理のための Azure AD PowerShell の例](../app-management-powershell-samples.md)に関する記事を参照してください。
