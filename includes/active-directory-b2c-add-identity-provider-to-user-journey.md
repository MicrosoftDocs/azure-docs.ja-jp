---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674238"
---
## <a name="add-a-user-journey"></a>ユーザー体験を追加する 

この時点では、ID プロバイダーはセットアップされていますが、サインイン ページではまだ使用できません。 独自のカスタム ユーザー体験がない場合は、既存のテンプレート ユーザー体験の複製を作成してください。そうでない場合は、次の手順に進みます。 

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
1. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
1. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
1. ユーザー体験の ID の名前を変更します。 たとえば、「 `Id="CustomSignUpSignIn"` 」のように入力します。

## <a name="add-the-identity-provider-to-a-user-journey"></a>ユーザー体験に ID プロバイダーを追加する 

これでユーザー体験ができたので、ユーザー体験に新しい ID プロバイダーを追加します。 最初にサインイン ボタンを追加してから、ボタンをアクションにリンクします。 アクションは、前に作成した技術プロファイルです。

1. ユーザー体験内で、`Type="CombinedSignInAndSignUp"` または `Type="ClaimsProviderSelection"` を含むオーケストレーション ステップ要素を見つけます。 これは通常、最初のオーケストレーション ステップです。 **ClaimsProviderSelections** 要素には、ユーザーがサインインに使用できる ID プロバイダーの一覧が含まれています。 要素の順序により、ユーザーに表示されるサインイン ボタンの順序が制御されます。 **ClaimsProviderSelection** XML 要素を追加します。 **TargetClaimsExchangeId** の値をフレンドリ名に設定します。

1. 次のオーケストレーション ステップで、**ClaimsExchange** 要素を追加します。 **ID** を、ターゲットの要求交換 ID の値に設定します。**TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。

次の XML は、ID プロバイダーを使用したユーザー体験の最初の 2 つのオーケストレーション ステップを示しています。