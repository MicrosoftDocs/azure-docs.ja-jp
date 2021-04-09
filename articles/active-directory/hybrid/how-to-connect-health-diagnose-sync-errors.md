---
title: Azure AD Connect Health - 重複する属性の同期エラーの診断 | Microsoft Docs
description: このドキュメントでは、重複属性同期エラーの診断プロセスと、孤立したオブジェクトのシナリオを Azure portal から直接修正できる可能性について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fe6af43c9ca44095c328356e8171da10717875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728239"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>重複属性同期エラーを診断して修正する

## <a name="overview"></a>概要
Azure Active Directory (Azure AD) Connect Health では、同期エラーの解明をさらに一歩進めて、セルフサービスで修復することができます。 重複属性同期エラーのトラブルシューティングを行い、Azure AD から孤立したオブジェクトを修正します。
診断機能には次のような利点があります。
- 重複属性同期エラーを絞り込む診断手順を提供します。 そして、具体的な修正方法を提供します。
- Azure AD から専用のシナリオに修正を適用して、1 ステップでエラーを解決します。
- この機能を有効にするために、アップグレードや構成の必要がない。
Azure AD の詳細については、「[ID 同期と重複属性の回復性](how-to-connect-syncservice-duplicate-attribute-resiliency.md)」をご覧ください。

## <a name="problems"></a>問題が発生した場合
### <a name="a-common-scenario"></a>一般的なシナリオ
**QuarantinedAttributeValueMustBeUnique** および **AttributeValueMustBeUnique** 同期エラーが発生した場合、一般的には、**UserPrincipalName** または **プロキシ アドレス** の競合を Azure AD で確認します。 オンプレミス側から競合するソース オブジェクトを更新することで、同期エラーを解決できる可能性があります。 同期エラーは、次の同期の後に解決されます。たとえば、次の図は 2 人のユーザーの **UserPrincipalName** が競合していることを示しています。 両方とも **Joe.J\@contoso.com** です。 競合するオブジェクトは、Azure AD で検査されます。

![同期エラーの一般的なシナリオを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>孤立したオブジェクトのシナリオ
既存のユーザーの **ソース アンカー** が失われていることに気付く場合があります。 オンプレミスの Active Directory で、ソース オブジェクトの削除が発生しました。 しかし、削除信号の変更が Azure AD に同期されませんでした。 このような喪失は、同期エンジンの問題やドメインの移行などの理由で発生します。 同じオブジェクトが復元されたり再作成されたりするとき、論理的には、既存のユーザーが **ソース アンカー** から同期するユーザーである必要があります。 

既存のユーザーがクラウドのみのオブジェクトである場合、競合しているユーザーが Azure AD に同期されることもあります。 ユーザーを既存のオブジェクトに同期して一致させることはできません。 **ソース アンカー** を再マップする直接的な方法はありません。 詳細については、[既存のサポート技術情報](https://support.microsoft.com/help/2647098)をご覧ください。 

たとえば、Azure AD の既存のオブジェクトが、Joe のライセンスを保持しているものとします。 異なる **ソース アンカー** のオブジェクトが新しく同期されると、Azure AD 内で重複属性状態が発生します。 オンプレミスの Active Directory での Joe の変更は、Azure AD での Joe の元のユーザー (既存のオブジェクト) には適用されません。  

![同期エラーで孤立したオブジェクトのシナリオを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health における診断とトラブルシューティングの手順 
診断機能では、次のような重複属性を持つユーザー オブジェクトがサポートされています。

| 属性名 | 同期エラーの種類|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique または AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique または AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> この機能にアクセスするには、**全体管理者** のアクセス許可、または Azure RBAC の **共同作成者** のアクセス許可が必要です。
>

同期エラーの詳細を絞り込み、さらに具体的な解決策を表示するには、Azure portal から次の手順のようにします。

![同期エラーの診断手順](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Azure portal から次の手順を実行して、修正可能な固有のシナリオを特定します。  
1.  **[Diagnose status]\(診断の状態\)** 列を確認します。 状態では、Azure Active Directory から直接同期エラーを修正する方法があるかどうかが示されます。 つまり、エラー ケースを絞り込んで修正できる可能性があるトラブルシューティング フローが存在します。

| Status | これはどういうことですか? |
| ------------------ | -----------------|
| 未開始 | この診断プロセスをまだ使用していません。 診断結果に応じて、ポータルから直接同期エラーを修正する潜在的な方法があります。 |
| 手動修正が必要 | エラーが、ポータルから使用可能な修正の条件に適合しません。 競合するオブジェクトの種類がユーザーではないか、または既に診断手順を完了しポータルから使用可能な修正の解決策がありません。 後者の場合、オンプレミス側からの修正は依然として有効な解決策の 1 つです。 [オンプレミスでの修正](https://support.microsoft.com/help/2647098)に関するページをご覧ください。 | 
| 同期保留中 | 修正が適用されました。 ポータルは、次の同期サイクルでエラーが解消されるのを待機しています。 |

  >[!IMPORTANT]
  > 診断の状態の列は、同期サイクルごとにリセットされます。 
  >

1. エラーの詳細の下にある **[診断]** ボタンを選択します。 いくつかの質問に答えて、同期エラーの詳細を明らかにします。 質問に回答すると、孤立オブジェクトのケースの特定に役立ちます。

1. 診断の最後に **[閉じる]** ボタンが表示される場合は、回答に基づいてポータルから使用可能な簡易的な修正はありません。 最後の手順に示したソリューションを参照してください。 オンプレミスからの修正はまだ有効です。 **[閉じる]** ボタンを選択します。 現在の同期エラーの状態が、 **[手動修正が必要]** に切り替わります。 現在の同期サイクルの間、状態は変わりません。

1. 孤立オブジェクトを識別した後は、重複属性同期エラーをポータルから直接修正できます。 プロセスを開始するには、 **[修正の適用]** ボタンを選択します。 現在の同期エラーの状態が更新され、 **[同期を保留にしています]** になります。

1. 次の同期サイクルの後で、エラーが一覧から削除されています。

## <a name="how-to-answer-the-diagnosis-questions"></a>診断の質問に回答する方法 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>オンプレミスの Active Directory にユーザーは存在しますか。

この質問は、オンプレミスの Active Directory から既存ユーザーのソース オブジェクトを特定しようとするものです。  
1. 指定された **UserPrincipalName** を持つオブジェクトが Active Directory に存在するかどうかを確認してください。 存在しない場合は、 **[いいえ]** と回答します。
2. 存在する場合は、オブジェクトがまだ同期のスコープ内にあるかどうかを確認します。  
   - DN を使用して Azure AD のコネクタ スペースを検索します。
   - **[保留中の追加]** 状態のオブジェクトが見つかった場合は、 **[いいえ]** と回答します。 Azure AD Connect は適切な Azure AD オブジェクトにオブジェクトを接続できません。
   - オブジェクトが見つからない場合は、 **[はい]** と回答します。

これらの例の質問は、オンプレミスの Active Directory に **Joe Jackson** がまだ存在するかどうかを確認しようとしています。
**一般的なシナリオ** では、**Joe Johnson** と **Joe Jackson** 両方のユーザーがオンプレミスの Active Directory に存在します。 検査されているオブジェクトは、2 つの異なるユーザーです。

![同期エラーの一般的なシナリオを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

**孤立したオブジェクトのシナリオ** では、オンプレミスの Active Directory から単一のユーザーのみ (**Joe Johnson**) が表示されます。

![同期エラーの孤立ユーザー オブジェクトが "存在する" シナリオを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>これら両方のアカウントは同じユーザーに属していますか。
質問では、競合している受信ユーザーと Azure AD の既存のユーザー オブジェクトをチェックして、同じユーザーに属しているかどうかをチェックしています。  
1. 競合しているオブジェクトは、Azure Active Directory に新しく同期されます。 オブジェクトの属性を比較します。  
   - 表示名
   - ユーザー プリンシパル名
   - Object ID
2. Azure AD が比較できなかった場合は、指定された **UserPrincipalNames** を持つオブジェクトが Active Directory に存在するかどうかを確認します。 両方見つかった場合は、 **[いいえ]** と回答します。

次の例では、2 つのオブジェクトが同じユーザーである **Joe Johnson** に属しています。

![同期エラーで孤立オブジェクトが "同じユーザー" であるシナリオを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>孤立したオブジェクトのシナリオで修正の適用後に発生する処理
前の質問への回答に基づいて、Azure AD から使用可能な修正がある場合は、 **[修正の適用]** ボタンが表示されます。 このケースでは、オンプレミスのオブジェクトが予期しない Azure AD オブジェクトと同期しています。 2 つのオブジェクトは、**ソース アンカー** を使用してマップされます。 **[修正の適用]** の変更は、次の手順または同様の手順で行われます。
1. Azure AD で適切なオブジェクトへの **ソース アンカー** を更新します。
2. 存在する場合、Azure AD で競合するオブジェクトを削除します。

![修正後に同期エラーを診断する](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **[修正の適用]** による変更は、孤立オブジェクトのケースにのみ適用されます。
>

前の手順の後、ユーザーは既存オブジェクトへのリンクである元のリソースにアクセスできます。 リスト ビューにある **[Diagnose status]\(診断の状態\)** の値は、 **[同期を保留にしています]** に更新されます。同期エラーは、次の同期の後に解決されます。Connect Health では、解決済みの同期エラーがリスト ビューに表示されなくなります。

## <a name="failures-and-error-messages"></a>障害とエラー メッセージ
**競合する属性を持つユーザーは、Azure Active Directory では論理的に削除されます。再試行する前に、ユーザーが実際に削除されていることを確認してください。**  
Azure AD で競合する属性を持つユーザーは、修正プログラムを適用する前にクリーンアップする必要があります。 修正を再度試みる前に、[Azure AD でユーザーを完全に削除する方法](../fundamentals/active-directory-users-restore.md)に関するページを確認してください。 論理的削除状態になったユーザーは、30 日経過した場合も自動的に完全に削除されます。 

**Updating source anchor to cloud-based user in your tenant is not supported.** (ソース アンカーのテナント内のクラウド ベースのユーザーへの更新は、サポートされていません。)  
Azure AD のクラウド ベースのユーザーは、ソース アンカーを持つことはできません。 この場合、ソース アンカーの更新はサポートされていません。 オンプレミスから手動で修正する必要があります。 

**修正プロセスで値を更新できませんでした。**
[UserWriteback in Azure AD Connect](./how-to-connect-preview.md#user-writeback) などの特定の設定はサポートされていません。 この設定を無効にしてください。 

## <a name="faq"></a>よく寄せられる質問
**Q.** **[修正の適用]** の実行が失敗した場合はどうなりますか。  
**A.** 実行が失敗した場合、Azure AD Connect はエクスポート エラーになっている可能性があります。 次の同期の後に、ポータル ページを更新して再試行してください。既定の同期サイクルは 30 分です。 


**Q.** **既存のオブジェクト** が、削除する必要があるオブジェクトの場合はどうなりますか。  
**A.** **既存オブジェクト** を削除する必要がある場合、プロセスでは **ソース アンカー** の変更は行われません。 通常は、オンプレミスの Active Directory から修正することができます。 


**Q.** 修正を適用するには、ユーザーにどのようなアクセス許可が必要ですか。  
**A.** **全体管理者**、または RBAC の **共同作成者** には、診断とトラブルシューティングのプロセスにアクセスするためのアクセス許可があります。


**Q.** Azure AD Connect を構成するか、またはこの機能の Azure AD Connect Health エージェントを更新する必要はありますか。  
**A.** いいえ、診断プロセスは、完全なクラウド ベース機能です。


**Q.** 既存のオブジェクトが論理的に削除された場合、診断プロセスはオブジェクトを再度アクティブにしますか。  
**A.** いいえ、修正では、**ソース アンカー** 以外のオブジェクト属性を更新しません。