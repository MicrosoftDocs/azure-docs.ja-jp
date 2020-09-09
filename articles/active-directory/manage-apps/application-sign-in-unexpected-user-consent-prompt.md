---
title: アプリケーションにサインインするときに、予期しない同意プロンプトが表示される | Microsoft Docs
description: Azure AD と統合されているアプリケーションでユーザーに予期しない同意プロンプトが表示されたときのトラブルシューティングの方法
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9559e22a8396de93bbe46f20b76855d9ff00f753
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763722"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>アプリケーションにサインインするときに、予期しない同意プロンプトが表示される

Azure Active Directory と統合する多くのアプリケーションを実行するためには、さまざまなリソースへのアクセス許可が必要です。 これらのリソースを Azure Active Directory に統合する際にも、Azure AD 同意フレームワークを使用してリソースへのアクセス許可が要求されます。 

このため、アプリケーションを初めて使用する際に同意プロンプトが表示されます。通常、これは 1 回限りの操作です。 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>ユーザーに同意プロンプトが表示されるシナリオ

追加のプロンプトは、次のようなさまざまなシナリオで表示されます。

* アプリケーションに必要なアクセス許可のセットが変更された場合。

* アプリケーションに最初に同意したユーザーが管理者ではなく、現在はさまざまな (管理者以外の) ユーザーが、アプリケーションを初めて使用している場合。

* アプリケーションに最初に同意したユーザーは管理者だったが、同意したのが組織全体の代表としてではなかった場合。

* 最初に同意が許可された後に、アプリケーションが追加のアクセス許可を要求するために[増分および動的な同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent)を使用している場合。 通常、この方法は、アプリケーションのオプション機能を追加するために、ベースライン機能に必要なアクセス許可を超えるアクセス許可が必要になる場合に使用されます。

* 最初に同意が許可された後で取り消された場合。

* 開発者がアプリケーションを、使用するたびに同意プロンプトを表示するように構成した場合 (注: これはベスト プラクティスではありません)。

## <a name="next-steps"></a>次のステップ

-   [Azure Active Directory (v1.0 エンドポイント) のアプリ、アクセス許可、および同意](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Azure Active Directory (v2.0 エンドポイント) のスコープ、アクセス許可、および同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


