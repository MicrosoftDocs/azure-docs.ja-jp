---
title: "パスワードの複雑さ - Azure AD B2C | Microsoft Docs"
description: "Azure Active Directory B2C でコンシューマーによって指定されるパスワードの複雑さの要件を構成する方法"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: ab5547ef76121aa395168844bd69759613ffc045
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: パスワードの複雑さの要件を構成する

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にエンド ユーザーが指定するパスワードの複雑さの要件の変更がサポートされます。  既定では、Azure AD B2C では `Strong` パスワードを使います。  Azure AD B2C では、顧客が使用できるパスワードの複雑さを制御する構成オプションもサポートしています。

## <a name="when-password-rules-are-enforced"></a>パスワードのルールが適用されるタイミング

サインアップ時またはパスワードのリセット時に、エンド ユーザーは、複雑さのルールを満たすパスワードを指定する必要があります。  パスワードの複雑さのルールはポリシーごとに適用されます。  あるポリシーがサインアップ時に 4 桁の PIN を要求し、別のポリシーがサインアップ時に 8 文字の文字列を要求することができます。  たとえば、大人と子供とで異なるパスワードの複雑さを持つポリシーを使用できます。

パスワードの複雑さは、サインイン時には適用されません。  ユーザーは、現在の複雑さの要件を満たしていないためにサインイン時にパスワードの変更を求められることはありません。

パスワードの複雑さを構成できるポリシーの種類を次に示します。

* サインアップまたはサインイン ポリシー
* パスワードのリセット ポリシー
* カスタム ポリシー (「[Configure password complexity in custom policy](active-directory-b2c-reference-password-complexity-custom.md)」(カスタム ポリシーでパスワードの複雑さを構成する))

## <a name="how-to-configure-password-complexity"></a>パスワードの複雑さを構成する方法

1. 次の手順に従って [Azure AD B2C の設定に移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)します。
1. **[サインアップまたはサインイン ポリシー]** を開きます。
1. ポリシーを選び、**[編集]** をクリックします。
1. **[パスワードの複雑さ]** を開きます。
1. このポリシーのパスワードの複雑さを **[シンプル]**、**[強]**、**[カスタム]** のいずれかに変更します。

### <a name="comparison-chart"></a>比較チャート

| 複雑さ | Description |
| --- | --- |
| シンプル | 少なくとも 8 ～ 64 文字のパスワード。 |
| Strong | 少なくとも 8 ～ 64 文字のパスワード。 小文字、大文字、数字、記号の 4 種類のうち 3 種が必要です。 |
| カスタム | このオプションでは、パスワードの複雑さのルールを最も細かく制御できます。  カスタムの長さを構成できます。  数字のみのパスワード (PIN) を受け入れることもできます。 |

## <a name="options-available-under-custom"></a>カスタムで使用可能なオプション

### <a name="character-set"></a>文字セット

数字のみ (PIN) または完全な文字セットを受け入れることができます。

* **[数字のみ]** では、パスワードを入力するときに数字 (0 ～ 9) のみを使用できます。
* **[すべて]** では、任意の文字、数字、記号を使用できます。

### <a name="length"></a>Length

パスワードの長さの要件を制御できます。

* **[最短]** は、少なくとも 4 にする必要があります。
* **[最大長]** は最小の長さ以上で、最大 64 文字にすることができます。

### <a name="character-classes"></a>文字クラス

パスワードで使われる異なる文字の種類を制御できます。

* **[2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol]\(2/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードに少なくとも 2 種類の文字が含まれることを保証します。 たとえば、数値と小文字です。
* **[3 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol]\(3/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードに少なくとも 2 種類の文字が含まれることを保証します。 たとえば、数値、小文字、大文字です。
* **[4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol]\(4/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードにすべての文字種が含まれることを保証します。

    > [!NOTE]
    > **4/4** を要求すると、エンドユーザーが不満を感じます。 いくつかの調査では、この要件にによってパスワードのエントロピは向上しないことが示されています。 [NIST パスワード ガイドライン](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)をご覧ください
