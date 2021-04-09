---
title: Azure Active Directory B2C におけるサブ体験 | Microsoft Docs
description: Azure Active Directory B2C にカスタム ポリシーのサブ体験要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386869"
---
# <a name="sub-journeys"></a>サブ体験

サブ体験を使用して、ユーザー体験内のオーケストレーション ステップのフローを整理して簡略化できます。 [ユーザー体験](userjourneys.md)では、証明書利用者アプリケーションがユーザーの任意の要求を取得できるようにする、ポリシーの明示的なパスを指定します。 ユーザーはこれらのパスを通じて証明書利用者に提示される要求を検索します。 つまり、ユーザー体験では、Azure AD B2C Identity Experience Framework が要求をプロセスする際に、エンド ユーザーがたどるビジネス ロジックを定義します。 ユーザー体験は、トランザクションを成功させるために従う必要のあるオーケストレーション シーケンスとして表されます。 オーケストレーション ステップの [ClaimsExchange](userjourneys.md#claimsexchanges) 要素は、実行される単一の[技術プロファイル](technicalprofiles.md)に関連付けられます。

サブ体験は、オーケストレーション ステップをグループ化したものであり、ユーザー体験内の任意の時点で呼び出すことができます。 サブ体験を使用して、再利用可能なステップ シーケンスを作成したり、ビジネス ロジックをもっと適切に表現する分岐を実装したりできます。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>ユーザー体験の分岐

サブ体験は[ユーザー体験](userjourneys.md)と同じように動作します。どちらも、トランザクションを成功させるために従う必要のあるオーケストレーション シーケンスとして表されます。 ユーザー体験は独自に呼び出すことができ、実行するには SendClaims ステップが必要です。 サブ体験はユーザー体験のコンポーネントであり、単独で呼び出すことはできず、常にユーザー体験から呼び出されます。

この分岐の主要コンポーネントによって、ユーザー体験でのビジネス ロジックの処理を向上させることができます。 一般的なオーケストレーション ステップが、個別に呼び出される個々のピースにグループ化されます。 サブ体験を使用すると体験が単純化され、(同じ事前条件を持つ) 複数のオーケストレーション ステップが結合されます。 サブ体験はユーザー体験からのみ呼び出され、別のサブ体験を呼び出すことはできません。

2 種類のサブ体験があります。

- **呼び出し** - 呼び出し元に制御が戻ります。 サブ体験が実行された後、ユーザー体験内で現在実行中のオーケストレーション ステップに制御が返されます。
- **転送** - 制御をサブ体験に移します (不可逆分岐)。 サブ体験には、要求を証明書利用者アプリケーションに返すための SendClaims ステップが必要です。

## <a name="example-scenarios"></a>シナリオの例

### <a name="call-sub-journey"></a>サブ体験を呼び出す

サブ体験の呼び出しは、次のシナリオで役立ちます。

- 年齢制限:年齢制限の場合、ユーザー体験で共有される多数のコンポーネントがあります。 分岐を使用して、共通の要素を共有可能なコンポーネントにまとめることができます。  
- 保護者の同意:保護者の同意を得る設計の中で、分岐を便利に使用できます。小規模に実行される要求にユーザー体験からアクセスでき、ユーザーの同意が必要であることを検出した後で保護者の同意を得るユーザー体験に分岐できます。 
- サインインするためのサインアップ:ユーザーが既にディレクトリに存在するが、実際にアカウントを作成したことを忘れてしまったというシナリオを考えてみましょう。 このような場合は、入力した資格情報が既に存在していることをユーザーに通知するのではなく、ポリシーによってユーザーのサインアップ フローからサインイン フローへの切り替えを実行できる体験をユーザーに再度開始させるほうが望ましい場合があります。  

### <a name="transfer-sub-journey"></a>サブ体験を転送する

サブ体験の転送は、次のシナリオで役立ちます。

- ブロック ページの表示。
- 要求をサブ体験にルーティングして実行し、トークンを発行する A/B テスト。

## <a name="adding-a-subjourneys-element"></a>サブ体験要素の追加

次に示すのは、種類が `Call` である `SubJourney` 要素の例です。ここでは制御がユーザー体験に戻ります。

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

次に示すのは、種類が `Transfer` である `SubJourney` 要素の例です。ここでは、トークンが証明書利用者アプリケーションに戻ります。

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>サブ体験のステップを呼び出す

サブ体験は、`InvokeSubJourney` 型の新しいオーケストレーション ステップを使用して実行されます。 次に示すのは、このオーケストレーション ステップのすべての実行要素を表示する例です。

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> サブ体験で別のサブ体験を呼び出すことはできません。

## <a name="components"></a>コンポーネント

ポリシーでサポートされるサブ体験を定義するには、ポリシー ファイルの最上位の要素の下に **SubJourneys** 要素を追加します。

**SubJourneys** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 完全なユーザー フローに必要な構成すべてを定義するサブ体験。 |

**SubJourneys** 要素には、次の属性が含まれます。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | サブ体験によってポリシー内のサブ体験を参照するために使用できるサブ体験識別子。 この属性は、[Candidate](userjourneys.md#journeylist) 要素の **SubJourneyReferenceId** 要素によってポイントされます。 |
| Type | はい | 指定できる値: `Call` または `Transfer`。 詳細については、「[ユーザー体験の分岐](#user-journey-branching)」を参照してください。|

**SubJourney** 要素には、次の要素が含まれます。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | トランザクションを成功させるために従う必要のあるオーケストレーション シーケンス。 すべてのユーザー体験は、シーケンスで実行されるオーケストレーション手順の順序付きリストで構成されます。 いずれかの手順に失敗した場合、トランザクションは失敗します。 |

## <a name="orchestrationsteps"></a>OrchestrationSteps

オーケストレーション ステップの要素の完全な一覧については、「[UserJourneys](userjourneys.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[UserJourneys](userjourneys.md) の詳細