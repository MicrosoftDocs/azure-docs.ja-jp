---
title: カスタム Azure AD アプリのトークン有効期間の既定値を変更する
description: Azure AD で開発しているアプリケーションのトークンの有効期間ポリシーを更新する方法
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114933"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>独自に開発したアプリケーションのトークン有効期間の既定値を変更する方法

この記事では、Azure AD PowerShell を使用して、トークンの有効期間ポリシーを設定する方法を示します。 Azure AD Premium では、非機密クライアントに対して発行されたトークンの有効期間をアプリ開発者とテナント管理者が構成できます。 トークンの有効期間ポリシーは、テナント全体またはアクセスされるリソースに対して設定されます。

1. トークンの有効期間ポリシーを設定するには、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードする必要があります。
1. **Connect-AzureAD -Confirm** コマンドを実行します。

    次のサンプル ポリシーは、MaxAgeSingleFactor 更新トークンを設定します。 次のポリシーを作成します。 ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>次のステップ

* 所属組織内のすべてのアプリ、マルチテナント アプリ、または所属組織内の特定のサービス プリンシパルに対するトークンの有効期間を設定する方法を含む、Azure AD によって発行されたトークンの有効期間を構成する方法については、[Azure AD で構成可能なトークンの有効期間](./active-directory-configurable-token-lifetimes.md)に関するページを参照してください。 
* [Azure AD のトークン リファレンス](./id-tokens.md)