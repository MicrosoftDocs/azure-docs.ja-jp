---
title: PIM 向けの Microsoft Graph API (プレビュー) - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) 向けの Microsoft Graph API (プレビュー) の使用に関する情報を提供します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb102c23d80095b8eb79fc25d1c1fd9d7f374fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91529696"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Privileged Identity Management 向けの Microsoft Graph API (プレビュー)

Azure Active Directory の [Microsoft Graph API](/graph/overview) を使用して、Privileged Identity Management タスクを実行できます。 この記事では、Privileged Identity Management 向けの Microsoft Graph API シリーズを使用するための重要な概念について説明します。

Microsoft Graph API の詳細については、[Azure AD Privileged Identity Management API のリファレンス](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)に関するページをご確認ください。

> [!IMPORTANT]
> Microsoft Graph でベータ版の API はプレビュー段階であり、変更されることがあります。 実稼働アプリケーションにおけるこれらの API の使用はサポートされていません。

## <a name="required-permissions"></a>必要なアクセス許可

Privileged Identity Management 向けの Microsoft Graph API シリーズを呼び出すには、以下のアクセス許可が **1 つまたは複数** 必要です。

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>アクセス許可を設定する

アプリケーションが Privileged Identity Management 向けの Microsoft Graph API シリーズを呼び出すには、それらが必要なアクセス許可を備えていなければなりません。 必要なアクセス許可を指定する最も簡単な方法は、[Azure AD 同意フレームワーク](../develop/consent-framework.md)を使用することです。

### <a name="set-permissions-in-graph-explorer"></a>Graph エクスプローラーでアクセス許可を設定する

ご自分の呼び出しをテストするために Graph エクスプローラーを使用している場合は、お客様はツール内でアクセス許可を指定できます。

1. グローバル管理者として [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)にサインインします。

1. **[アクセス許可の変更]** をクリックします。

    ![[アクセス許可の変更] 操作が選択されている [Graph エクスプローラー] ページを示すスクリーンショット。](./media/pim-apis/graph-explorer.png)

1. 含めたいアクセス許可の横にあるチェック ボックスをオンにします。 `PrivilegedAccess.ReadWrite.AzureAD` は、Graph エクスプローラーではまだ使用できません。

    ![Graph エクスプローラー - アクセス許可の変更](./media/pim-apis/graph-explorer-modify-permissions.png)

1. **[アクセス許可の変更]** をクリックして、アクセス許可の変更を適用します。

## <a name="next-steps"></a>次のステップ

- [Azure AD Privileged Identity Management API のリファレンス](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)