---
title: What If ツールを使用して条件付きアクセスをトラブルシューティングする - Azure Active Directory
description: どのような条件付きアクセス ポリシーが適用されたか、およびその理由を見つけるための場所
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d463bc889385f8f157ebb9892acc7a4a8b2a2e
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278628"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>What If ツールを使用した条件付きアクセスのトラブルシューティング

条件付きアクセスの [What If ツール](what-if-tool.md)は、あるポリシーが特定の状況でユーザーに適用されたりされなかったりした理由や、あるポリシーが既知の状態で適用されるかどうかを理解しようとしている場合の強力なツールです。

What If ツールは、 **[Azure portal]**  >  **[Azure Active Directory]**  >  **[条件付きアクセス]**  >  **[What If]** にあります。

![既定の状態にある条件付きアクセスの What If ツール](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

## <a name="gathering-information"></a>情報の収集

What If ツールの使用を開始するために必要なのは**ユーザー**だけです。 

次の追加情報はオプションですが、特定のケースで範囲を絞り込むのに役立ちます。

* クラウド アプリまたはアクション
* IP アドレス 
* Country
* デバイスのプラットフォーム
* クライアント アプリ (プレビュー)
* デバイス状態 (プレビュー) 
* サインイン リスク

これらの情報は、ユーザー、そのデバイス、または Azure AD サインイン ログから収集できます。

## <a name="generating-results"></a>結果の生成

前のセクションで収集された条件を入力し、 **[What If]** を選択して結果の一覧を生成します。 

いつでも、 **[Reset] (リセット)** を選択して条件の入力をすべてクリアし、既定の状態に戻ることができます。

## <a name="evaluating-results"></a>結果の評価

### <a name="policies-that-will-apply"></a>[Policies that will apply] (適用されるポリシー)

この一覧は、条件が与えられたときに、どの条件付きアクセス ポリシーが適用されるかを示します。 この一覧には、適用される許可とセッション制御の両方が含まれます。 例として、特定のアプリケーションにアクセスするための多要素認証の要求があります。

### <a name="policies-that-will-not-apply"></a>[Policies that will not apply] (適用されないポリシー)

この一覧は、条件が適用された場合に適用されない条件付きアクセス ポリシーを示します。 この一覧には、すべてのポリシーとそれらが適用されない理由が含まれます。 例として、ポリシーから除外される可能性があるユーザーやグループがあります。

## <a name="use-case"></a>ユース ケース

多くの組織では、ネットワークの場所、信頼できる場所の許可、およびアクセスが行われてはいけない場所のブロックに基づいてポリシーが作成されます。

構成が適切に作成されていることを検証するために、管理者は What If ツールを使用して、許可されるべき場所や拒否されるべき場所からアクセスを模倣できます。

![[アクセスのブロック] での結果を示す What If ツール](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

この例では、Contoso が北朝鮮からのアクセスをブロックしたため、ユーザーはその場所への旅行ですべてのクラウド アプリへのアクセスをブロックされます。

このテストは、範囲を絞り込むための他のデータ ポイントを組み込むように拡張できます。

## <a name="next-steps"></a>次の手順

* [条件付きアクセスとは](overview.md)
* [Azure Active Directory Identity Protection とは](../identity-protection/overview-v2.md)
* [デバイス ID とは](../devices/overview.md)
* [動作のしくみ: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)」を参照してください。
