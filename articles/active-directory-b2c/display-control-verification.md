---
title: 表示コントロールを使用して要求を検証する
titleSuffix: Azure AD B2C
description: Azure AD B2C 表示コントロールを使用して、カスタム ポリシーによって提供されるユーザー体験の要求を検証する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94532496"
---
# <a name="verification-display-control"></a>検証表示コントロール

検証[表示コントロール](display-controls.md)を使用して、要求 (電子メール アドレスや電話番号など) をユーザーに送信された確認コードを使用して検証します。

## <a name="verificationcontrol-actions"></a>VerificationControl アクション

検証表示コントロールは、次の 2 つの手順 (アクション) で構成されます。

1. 確認コードの送信先 (電子メールアドレスや電話番号など) をユーザーに要求します。 ユーザーが **[コードの送信]** ボタンを選択すると、検証表示コントロールの **SendCode アクション** が実行されます。 **SendCode アクション** によってコードが生成され、送信されるコンテンツが構築され、それがユーザーに送信されます。 アドレスの値を事前に設定して、2 要素認証として使用できます。

    ![[コードの送信] アクションのサンプル ページ](media/display-control-verification/display-control-verification-email-action-01.png)

1. コードが送信されたら、ユーザーはメッセージを読んで、表示コントロールによって提供されたコントロールに確認コードを入力し、**[コードの確認]** を選択します。 **[コードの確認]** を選択することで、**VerifyCode アクション** が実行され、アドレスに関連付けられたコードが検証されます。 ユーザーが **[新しいコードの送信]** を選択した場合は、最初のアクションが再び実行されます。

    ![コードの確認アクションのサンプル ページ](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>VerificationControl の必須要素

**VerificationControl** には、次の要素が含まれている必要があります。

- `DisplayControl` の型は `VerificationControl` です。
- `DisplayClaims`
  - **送信先** - 確認コードの送信先を指定する 1 つまたは複数の要求。 たとえば、"*電子メール*"、または "*国番号*" と "*電話番号*" です。
  - **確認コード** - コードが送信された後でユーザーが提供する確認コード要求。 この要求には required を設定する必要があり、さらに `ControlClaimType` を `VerificationCode` に設定する必要があります。
- ユーザーが検証プロセスを完了した後、セルフアサート ページに返される出力要求 (省略可能)。 たとえば、"*電子メール*"、または "*国番号*" と "*電話番号*" です。 セルフアサート技術プロファイルでは、これらの要求を使用して、データを永続化するか、次のオーケストレーション手順に対して出力要求がバブル アップされます。
- 次の名前の 2 つの `Action`:
  - **SendCode** - ユーザーにコードを送信します。 通常、このアクションには、コードを生成して送信するための 2 つの検証技術プロファイルが含まれます。
  - **VerifyCode** - コードを検証します。 通常、このアクションには、単一の検証技術プロファイルが含まれます。

次の例では、**email** テキスト ボックスがページに表示されます。 ユーザーが電子メール アドレスを入力して **[コードの送信]** を選択すると、**SendCode** アクションが Azure AD B2C バックエンドでトリガーされます。

その後、ユーザーが **確認コード** を入力し、**[コードの確認]** を選択して、バックエンドで **VerifyCode** アクションをトリガーします。 すべての検証に合格すれば、**VerificationControl** は完了したとみなされ、ユーザーは次の手順に進むことができます。

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
