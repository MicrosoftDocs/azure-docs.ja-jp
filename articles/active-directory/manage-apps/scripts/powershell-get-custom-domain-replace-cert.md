---
title: PowerShell サンプル - アプリケーション プロキシ アプリの証明書の置換
description: Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーション間で証明書を一括置換する PowerShell の例。
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
ms.openlocfilehash: c9482a5cde6928b7606c6fda4f7a140b791ab23a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260047"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>同じ証明書を使用して公開されたすべてのアプリケーション プロキシ アプリケーションを取得し、その証明書を置き換えます。

この PowerShell スクリプトの例を使用すると、同じ証明書で公開されているすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを対象に、証明書を一括で置き換えることができます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD でアプリケーション プロキシ用に構成されたアプリケーションを取得します。 |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate) | Azure AD のアプリケーション プロキシ用に構成されたアプリケーションに証明書を割り当てます。 このコマンドを実行すると、証明書がアップロードされ、アプリケーションでカスタム ドメインを使用できるようになります。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
