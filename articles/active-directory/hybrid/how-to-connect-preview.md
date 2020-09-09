---
title: Azure AD Connect:プレビュー段階の機能 | Microsoft Docs
description: このトピックでは、Azure AD Connect のプレビュー段階の機能について詳しく説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bb7305fe8ee96697c5c8b2172e32a2a97e5bd6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358431"
---
# <a name="more-details-about-features-in-preview"></a>プレビュー段階の機能の詳細
このトピックでは、現在プレビュー段階の機能を使用する方法について説明します。

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect 同期 V2 エンドポイント API (パブリック プレビュー) 

Microsoft がデプロイした Azure AD Connect 用の新しいエンドポイント (API) では、Azure Active Directory に対する同期サービス操作のパフォーマンスが向上しています。 新しい V2 エンドポイントを利用すると、Azure AD に対するエクスポートとインポートのパフォーマンスが明らかに向上していることがわかるでしょう。 この新しいエンドポイントでは、メンバーが最大 250,000 のグループの同期もサポートされています。 また、このエンドポイントを使用することで、グループ書き戻しが有効になっているとき、メンバーシップの上限なしで、オンプレミス Active Directory に O365 統一グループを書き戻すことができます。   詳細については、「[Azure AD Connect 同期 V2 エンドポイント API (パブリック プレビュー)](how-to-connect-sync-endpoint-api-v2.md)」を参照してください。

## <a name="user-writeback"></a>ユーザーの書き戻し
> [!IMPORTANT]
> ユーザーの書き戻しプレビュー機能は、Azure AD Connect の 2015 年 8 月の更新時に削除されました。 この機能を有効にしていた場合は、無効にする必要があります。
>
>

## <a name="next-steps"></a>次のステップ
「[Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)」に進んでください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
