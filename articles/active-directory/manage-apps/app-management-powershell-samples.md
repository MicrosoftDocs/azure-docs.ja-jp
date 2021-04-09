---
title: Azure Active Directory のアプリケーション管理用の PowerShell サンプル
description: これらの PowerShell サンプルは、Azure Active Directory テナントで管理しているアプリに使用します。 これらのサンプル スクリプトを使用すると、シークレットと証明書の有効期限に関する情報を見つけることができます。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: f5f7ec8245a43440a400b9ca6b55bf1093eb62cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636190"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>アプリケーション管理用の Azure Active Directory PowerShell の例

次の表には、Azure AD アプリケーション管理用の PowerShell スクリプトの例へのリンクが含まれています。 これらのサンプルでは、次のいずれかが必要です。
- [Graph 用 AzureAD V2 PowerShell モジュール](/powershell/azure/active-directory/install-adv2)
- [Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (これ以外の指定がない場合)。

これらのサンプルで使用されるコマンドレットの詳細については、「[アプリケーション](/powershell/module/azuread/#applications)」を参照してください。

| Link | 説明 |
|---|---|
|**アプリケーション管理スクリプト**||
| [シークレットと証明書のエクスポート (アプリの登録)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Azure Active Directory テナントでのアプリの登録用にシークレットと証明書をエクスポートします。 |
| [シークレットと証明書のエクスポート (エンタープライズ アプリ)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Azure Active Directory テナントのエンタープライズ アプリのシークレットと証明書をエクスポートします。 |
| [間もなく期限が切れるシークレットと証明書のエクスポート](scripts/powershell-export-apps-with-expriring-secrets.md) | Azure Active Directory テナントで、間もなく期限が切れるシークレットと証明書、およびその所有者を含むアプリの登録をエクスポートします。 |
| [必要な日付を過ぎた後に期限が切れるシークレットと証明書のエクスポート](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Azure Active Directory テナントで、必要な日付を過ぎた後に期限が切れるシークレットと証明書を含むアプリの登録をエクスポートします。 これは、非対話型の Client_Credentials Oauth フローを使用します。 |
