---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの日付要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの日付要求変換の例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: ad4c6c78556f98e2905b3583910e498055257c36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511127"
---
# <a name="date-claims-transformations"></a>日付要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの日付要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

ある日時要求 (文字列データ型) が 2 番目の日時要求 (文字列データ型) よりも後であることを確認し、例外をスローします。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | string | 最初の要求の型。2 番目の要求よりも後である必要があります。 |
| inputClaim | rightOperand | string | 2 番目の要求の型。最初の要求よりも前である必要があります。 |
| InputParameter | AssertIfEqualTo | ブール値 | 左オペランドが右オペランドと等しい場合にこのアサーションを渡すかどうかを指定します。 |
| InputParameter | AssertIfRightOperandIsNotPresent | ブール値 | 右オペランドがない場合にこのアサーションを渡すかどうかを指定します。 |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | 2 つの日時の間で時刻が等しいと見なすことができるミリ秒数を指定します (たとえば、時刻の誤差を説明)。 |

**AssertDateTimeIsGreaterThan** 要求変換は、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される[検証技術プロファイル](validation-technical-profile.md)から常に実行する必要があります。 **DateTimeGreaterThan** セルフアサート技術プロファイル メタデータでは、技術プロファイルによってユーザーに表示されるエラー メッセージを制御します。

![AssertStringClaimsAreEqual の実行](./media/date-transformations/assert-execution.png)

次の例では、`currentDateTime` 要求を `approvedDateTime` 要求と比較します。 `currentDateTime` が `approvedDateTime` より後の場合にエラーがスローされます。 変換で、値が 5 分間 (30,000 ミリ秒) の差以内である場合に等価として扱われます。

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

`login-NonInteractive` 検証技術プロファイルによって `AssertApprovedDateTimeLaterThanCurrentDateTime` 要求変換が呼び出されます。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

セルフアサート技術プロファイルによって **login-NonInteractive** 検証技術プロファイルが呼び出されます。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>例

- 入力要求:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- 結果:エラーがスローされます

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

**Date** ClaimType を **DateTime** ClaimType に変換します。 要求変換によって時間形式が変換され、日付に午前 12 時 00 分 00 秒が追加されます。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | 変換される ClaimType。 |
| OutputClaim | outputClaim | dateTime | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

次の例では、要求 `dateOfBirth` (date データ型) から別の要求 `dateOfBirthWithTime` (dateTime データ型) への変換を示します。

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**:2019-06-01
- 出力要求:
    - **outputClaim**:1559347200 (2019 年 6 月 1 日午前 12 時 00 分 00 秒)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

現在の UTC 日時を取得し、値を ClaimType に追加します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

* 出力要求:
    * **currentDateTime**: 1534418820 (2018 年 8 月 16 日午前 11 時 27 分 00 秒)

## <a name="datetimecomparison"></a>DateTimeComparison

ある dateTime が別の dateTime より後であるか、前であるか、または等しいかを確認します。 結果は、`true` または `false` の値を含む新しい ClaimType ブール値です。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | 2 番目の dateTime よりも前か後かを比較する最初の dateTime。 Null 値の場合は例外がスローされます。 |
| InputClaim | secondDateTime | dateTime | 最初の dateTime よりも前か後かを比較する 2 番目の dateTime。 Null 値は、現在の datetTime として扱われます。 |
| InputParameter | operator | string | 次のいずれかの値: 同じ、より後、より前。 |
| InputParameter | timeSpanInSeconds | int | 最初の datetime に timespan を追加します。 |
| OutputClaim | result | ブール値 | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この要求変換を使用すると、2 つの ClaimType がお互いにとって等しいか、後か、前かを判断できます。 たとえば、ユーザーがサービス使用条件 (TOS) に同意した最終時刻を格納できます。 3 か月後に、TOS にもう一度アクセスするようユーザーに要求できます。
要求変換を実行するには、まず現在の dateTime と、ユーザーが TOS に同意した最終時刻を取得する必要があります。

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- 入力パラメーター:
    - **演算子**: later than
    - **timeSpanInSeconds**: 7776000 (90 日間)
- 出力要求:
    - **result**: true
