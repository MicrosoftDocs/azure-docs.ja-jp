---
title: PowerShell のサンプル - 既定のドメインを使用するアプリケーション プロキシ アプリ
description: 既定のドメイン (.msappproxy.net) を使用するすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示する PowerShell の例。
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
ms.openlocfilehash: 9de36d27b592576300728f3720087073c165a672
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511203"
---
# <a name="get-all-application-proxy-apps-using-default-domains-msappproxynet"></a>既定のドメイン (.msappproxy.net) を使用するすべてのアプリケーション プロキシ アプリを取得する

この PowerShell スクリプトの例では、既定のドメイン (.msappproxy.net) を使用するすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションが一覧表示されます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-default-domain-apps.ps1 "Get all Application Proxy apps using default domains (.msappproxy.net)")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Azure AD でアプリケーション プロキシ用に構成されたアプリケーションを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。