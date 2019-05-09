---
title: Azure AD Connect:同期サービス インスタンス | Microsoft Docs
description: このページでは、Azure AD のインスタンスに関する注意事項を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217192"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect:インスタンスに関する特別な考慮事項
Azure AD Connect は、世界中の Azure AD と Office 365 のインスタンスで最もよく使われています。 ただし、それ以外のインスタンスも存在し、URL の要件が異なるだけでなく、その他の特別な考慮事項があります。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) は、ドイツのデータ トラスティによって運営されている主権のあるクラウドです。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +証明書の失効リスト |

Azure AD テナントにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。

Microsoft Cloud Germany に現在ない機能:

* **パスワード ライトバック**は、Azure AD Connect バージョン 1.1.570.0 以降のプレビューで利用できます。
* その他の Azure AD Premium サービスは使用できません。

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government クラウド
[Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/) は、米国政府向けクラウドです。

このクラウドは、DirSync の以前のリリースでサポートされています。 Azure AD Connect のビルド 1.1.180 から、次世代のクラウドがサポートされています。 この世代は、米国のみをベースにしたエンドポイントを使用し、プロキシ サーバーで開く URL のリストが異なります。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (Azure AD 政府機関向けテナントの自動検出で必須) |
| \*.gov.us.microsoftonline.com |
| +証明書の失効リスト |

> [!NOTE]
> AAD Connect バージョン 1.1.647.0 の時点で、プロキシ サーバーで *. windows.net が開いている場合、レジストリでの AzureInstance 値の設定は不要になりました。

Microsoft Azure Government クラウドに現在ない機能:

* **パスワード ライトバック**は、Azure AD Connect バージョン 1.1.570.0 以降のプレビューで利用できます。
* その他の Azure AD Premium サービスは使用できません。

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
