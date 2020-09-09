---
title: Apple デバイス用の Microsoft Enterprise SSO プラグイン
titleSuffix: Microsoft identity platform | Azure
description: iOS および macOS デバイス用の Microsoft の Azure Active Directory SSO プラグインについて説明します。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: f98be2ef6a82c099425655fd7e5d25a4358844bf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115443"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー)

> [!NOTE]
> この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

*Apple デバイス用の Microsoft Enterprise SSO プラグイン*では、Apple の [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 機能がサポートされているすべてのアプリケーションでの Azure Active Directory (Azure AD) アカウントに対するシングル サインオン (SSO) が提供されます。 Microsoft は Apple と密接に連携してこのプラグインを開発し、アプリケーションの使いやすさを向上させ、Apple と Microsoft が提供できる最適な保護を提供しています。

Enterprise SSO プラグインのこのパブリック プレビュー リリースは、iOS デバイスでのみ使用でき、特定の Microsoft アプリケーションで配布されます。

Enterprise SSO プラグインで最初に使用できるのは、新しい[共有デバイス モード](msal-ios-shared-devices.md)機能です。

## <a name="features"></a>特徴

Apple デバイス用の Microsoft Enterprise SSO プラグインには、次のような利点があります。

- Apple の Enterprise Single Sign-On 機能をサポートするすべてのアプリケーションで、Azure AD アカウントに対する SSO が提供されます。
- Microsoft Authenticator に自動的に提供され、任意のモバイル デバイス管理 (MDM) ソリューションで有効にすることができます。

## <a name="requirements"></a>必要条件

Apple デバイス用の Microsoft Enterprise SSO プラグインを使用するには:

- iOS 13.0 以降がデバイスにインストールされている必要があります。
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューでは、このようなアプリケーションには [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)が含まれます。
- デバイスは、MDM に登録されている必要があります (たとえば、Microsoft Intune)。
- デバイスで Apple デバイス用の Microsoft Enterprise SSO プラグインを有効にするには、構成がデバイスにプッシュされる必要があります。 このセキュリティ制約は、Apple によって要求されます。

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>モバイル デバイス管理 (MDM) で SSO 拡張機能を有効にする

Apple デバイス用の Microsoft Enterprise SSO プラグインを有効にするには、MDM サービスを介してデバイスに信号が送信される必要があります。 Enterprise SSO プラグインは [Microsoft Authenticator アプリ](..//user-help/user-help-auth-app-overview.md)に組み込まれているため、MDM を使用して Microsoft Enterprise SSO プラグインを有効にするようにアプリを構成します。

Apple デバイス用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

- **[種類]** :リダイレクト
- **拡張機能 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **チーム ID**: `SGGM6D27TK`
- **URL**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Microsoft Intune を MDM サービスとして使用し、Microsoft Enterprise SSO プラグインの構成を容易にすることができます。 詳細については、[Intune の構成に関するドキュメント](/intune/configuration/ios-device-features-settings)を参照してください。

## <a name="using-the-sso-extension-in-your-application"></a>アプリケーションでの SSO 拡張機能の使用

[Apple デバイス用の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) バージョン 1.1.0 以降では、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。

Apple デバイス用の Microsoft Enterprise SSO プラグインによって提供される共有デバイス モードをサポートする場合は、指定されている最低限必要なバージョンの MSAL をアプリケーションで使用してください。

## <a name="next-steps"></a>次のステップ

iOS での共有デバイス モードの詳細については、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」を参照してください。