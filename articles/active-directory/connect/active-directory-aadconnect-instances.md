---
title: "Azure AD Connect: 同期サービス インスタンス | Microsoft Docs"
description: "このページでは、Azure AD のインスタンスに関する注意事項を説明します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: ce4d51c4ed38645ee81325404ff7214f845d5bf0
ms.openlocfilehash: 317f3be6cb39da7c7c57f7f368c750672609726e
ms.lasthandoff: 01/31/2017


---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: インスタンスに関する特別な考慮事項
Azure AD Connect は、世界中の Azure AD と Office 365 のインスタンスで最もよく使われています。 ただし、それ以外のインスタンスも存在し、URL の要件が異なるだけでなく、その他の特別な考慮事項があります。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) は、ドイツのデータ トラスティによって運営されている主権のあるクラウドです。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +証明書の失効リスト |

Azure AD テナントにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。

Microsoft Cloud Germany に現在ない機能:

* **Azure AD Connect Health** は使用できません。
* **自動更新**は使用できません。
* **パスワード ライトバック**は使用できません。
* その他の Azure AD Premium サービスは使用できません。

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government クラウド
[Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/) は、米国政府向けクラウドです。

このクラウドは、DirSync の以前のリリースでサポートされています。 Azure AD Connect のビルド 1.1.180 から、次世代のクラウドがサポートされています。 この世代は、米国のみをベースにしたエンドポイントを使用し、プロキシ サーバーで開く URL のリストが異なります。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.com |
| \*.gov.us.microsoftonline.com |
| +証明書の失効リスト |

Azure AD Connect は、Azure AD テナントが Government クラウドにあることを自動的に検出できません。 そのため、Azure AD Connect のインストール時に次の操作を実行する必要があります。

1. Azure AD Connect のインストールを開始します。
2. 使用許諾契約書への同意が求められる最初のページが表示されたら、先に進まずに、インストール ウィザードを実行中のままにしておきます。
3. regedit を起動し、レジストリ キー `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` の値を `2` に変更します。
4. Azure AD Connect のインストール ウィザードに戻り、使用許諾契約書に同意して続行します。 インストール中は、 **カスタム構成** インストール パス (簡単インストールではなく) を使用してください。 以降は通常どおりにインストールを続行します。

Microsoft Azure Government クラウドに現在ない機能:

* **Azure AD Connect Health** は使用できません。
* **自動更新**は使用できません。
* **パスワード ライトバック**は使用できません。
* その他の Azure AD Premium サービスは使用できません。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

