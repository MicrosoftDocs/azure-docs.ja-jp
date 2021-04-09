---
title: 'Azure AD Connect: 同期サービス インスタンス | Microsoft Docs'
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
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90015264"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: インスタンスに関する特別な考慮事項
Azure AD および Microsoft 365 の世界規模のインスタンスでは、Azure AD Connect が最もよく使用されます。 ただし、それ以外のインスタンスも存在し、URL の要件が異なるだけでなく、その他の特別な考慮事項があります。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) は、ドイツのデータ トラスティによって運営されている主権のあるクラウドです。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +証明書の失効リスト |

Azure AD テナントにサインインするときは、onmicrosoft.de ドメインのアカウントを使用する必要があります。

Microsoft Cloud Germany に現在ない機能:

* **パスワード ライトバック** は、Azure AD Connect バージョン 1.1.570.0 以降のプレビューで利用できます。
* その他の Azure AD Premium サービスは使用できません。

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/) は、米国政府向けクラウドです。

このクラウドは、DirSync の以前のリリースでサポートされています。 Azure AD Connect のビルド 1.1.180 から、次世代のクラウドがサポートされています。 この世代は、米国のみをベースにしたエンドポイントを使用し、プロキシ サーバーで開く URL のリストが異なります。

| プロキシ サーバーで開く URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (Azure Government 向けテナントの自動検出で必須) |
| \*.gov.us.microsoftonline.com |
| +証明書の失効リスト |

> [!NOTE]
> Azure AD Connect バージョン 1.1.647.0 の時点で、プロキシ サーバーで *. windows.net が開いている場合、レジストリでの AzureInstance 値の設定は不要になりました。 ただし、Azure AD Connect サーバーからインターネット接続が許可されないお客様の場合、次の手動構成を使用できます。

### <a name="manual-configuration"></a>手動で構成

次の手動での構成手順は、Azure AD Connect で確実に Azure Government 同期エンドポイントが使用されるようにする目的で使用されます。

1. Azure AD Connect のインストールを開始します。
2. 使用許諾契約書への同意が求められる最初のページが表示されたら、先に進まずに、インストール ウィザードを実行中のままにしておきます。
3. regedit を起動し、レジストリ キー `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` の値を `4` に変更します。
4. Azure AD Connect のインストール ウィザードに戻り、使用許諾契約書に同意して続行します。 インストール中、**カスタム構成** インストール パス (高速インストールではなく) を使用し、その後は通常どおりにインストールを続けます。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
