---
title: Azure AD B2C サービスの制限と制約
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C サービスのサービスの制限と制約に関するリファレンス。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979914"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C サービスの制限と制約

この記事では、Azure Active Directory B2C (Azure AD B2C) サービスの使用上の制約およびその他のサービスの制限を説明します。

## <a name="end-userconsumption-related-limits"></a>エンド ユーザーまたは従量課金に関連する制限

次のエンド ユーザーに関連するサービスの制限は、Azure AD B2C によってサポートされるすべての認証および承認のプロトコル (SAML、Open ID Connect、OAuth2、ROPC など) に適用されます。

|カテゴリ |制限    |
|---------|---------|
|Azure AD B2C テナントごとの IP アドレスあたりの要求数       |6,000/5 分          |
|Azure AD B2C テナントごとの要求の合計数     |12,000/分          |

要求の数は、Azure AD B2C ユーザー体験中に発生するディレクトリの読み取りと書き込みの回数によって異なります。 たとえば、ディレクトリから読み取る単純なサインイン体験は、1 つの要求で構成されます。 サインイン体験でもディレクトリを更新する必要がある場合、この操作は追加の要求としてカウントされます。

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C 構成の制限

次の表に、Azure AD B2C サービスの管理構成の制限を示します。

|カテゴリ  |制限  |
|---------|---------|
|アプリケーションあたりのスコープの数        |1000          |
|ユーザーあたりの[カスタム属性](user-profile-attributes.md#extension-attributes) の数 <sup>1</sup>       |100         |
|アプリケーションあたりのリダイレクト URL の数       |100         |
|アプリケーションあたりのサインアウト URL の数        |1          |
|属性あたりの文字列制限      |250 文字          |
|サブスクリプションあたりの B2C テナントの数      |20         |
|カスタム ポリシーでの[継承](custom-policy-overview.md#inheritance-model)のレベル     |10         |
|Azure AD B2C テナントあたりのポリシーの数      |200          |
|ポリシー ファイルの最大サイズ      |400 KB          |

<sup>1</sup> 「[Azure AD サービスの制限と制約](../active-directory/enterprise-users/directory-service-limits-restrictions.md)」も参照してください。

## <a name="next-steps"></a>次のステップ

- [Microsoft Graph の調整ガイダンス](/graph/throttling)について学習します 
- [Azure AD B2C アプリケーションの検証の相違点](../active-directory/develop/supported-accounts-validation.md)について学習します













