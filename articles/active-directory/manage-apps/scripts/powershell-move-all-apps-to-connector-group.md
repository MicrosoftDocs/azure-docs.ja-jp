---
title: PowerShell サンプル - アプリケーション プロキシ アプリを別のグループに移動する
description: Azure Active Directory (Azure AD) アプリケーション プロキシの PowerShell の例は、現在コネクタグループに割り当てられているすべてのアプリケーションを別のコネクタグループに移動するために使用されます。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2632dbc4db093126d9b334e338a63dc8d0079b0c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511102"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>コネクタ グループに割り当てられているすべてのアプリを、別のコネクタ グループに移動する

この PowerShell スクリプトの例では、コネクタグループに、現在割り当てられているすべての Azure Active Directory (Azure AD) アプリケーション プロキシのアプリケーションを別のコネクタ グループに移動します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD アプリケーションを取得します。 |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | すべてのコネクタ グループの一覧を取得するか、指定した場合には、指定されたコネクタ グループの詳細を取得します。 |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | 指定されたコネクタ グループを、指定したアプリケーションに割り当てます。|

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。