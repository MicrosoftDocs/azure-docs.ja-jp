---
title: PowerShell のサンプル - Azure Active Directory テナントで、間もなく期限が切れるシークレットと証明書を含むアプリをエクスポートします。
description: Azure Active Directory テナントで指定したアプリについて、間もなく期限が切れるシークレットと証明書を含むすべてのアプリをエクスポートする PowerShell の例。
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
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149717"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>間もなく期限が切れるシークレットと証明書を含むアプリをエクスポートする

この PowerShell スクリプトの例では、指定したアプリについて、間もなく期限が切れるシークレット、証明書、およびそれらの所有者を含むすべてのアプリ登録をディレクトリから CSV ファイルにエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、変更せずに直接使用できます。 管理者には、有効期限と、既に有効期限が切れているシークレットまたは証明書を表示するかどうかを確認するメッセージが表示されます。

"Add-Member" コマンドは、CSV ファイル内に列を作成する役割を担います。
"New-Object" コマンドを使用すると、CSV ファイル エクスポートの列に使用されるオブジェクトが作成されます。
エクスポートを非対話的に行う場合は、PowerShell で CSV ファイル パスを使用して "$Path" 変数を直接変更できます。

| コマンド | Notes |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | ディレクトリからアプリケーションを取得します。 |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | ディレクトリからアプリケーションの所有者を取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション管理のためのその他の PowerShell の例については、[アプリケーション管理のための Azure AD PowerShell の例](../app-management-powershell-samples.md)に関する記事を参照してください。
