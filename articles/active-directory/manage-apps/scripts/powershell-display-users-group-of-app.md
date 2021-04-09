---
title: PowerShell サンプル - アプリケーション プロキシ アプリのユーザーとグループを一覧表示する
description: 特定の Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションに割り当てられているすべてのユーザーとグループを一覧表示する PowerShell の例。
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
ms.openlocfilehash: 540df7cbe2d35cd705e9d2b88f66a82604951957
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548546"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションに割り当てられているユーザーとグループを表示する

この PowerShell スクリプトの例では、特定の Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションに割り当てられているユーザーとグループが一覧表示されます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>スクリプトの説明

| command | Notes |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| ユーザーを取得します。 |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| グループを取得します。 |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | サービス プリンシパルを取得します。 |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | ユーザー アプリケーション ロールの割り当てを取得します。 |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | グループ アプリケーション ロールの割り当てを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
