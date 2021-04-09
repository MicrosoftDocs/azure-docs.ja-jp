---
title: Azure AD Connect:宣言型プロビジョニングについて | Microsoft Docs
description: Azure AD Connect における宣言型のプロビジョニングの構成モデルについて説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313622"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect 同期: 宣言型のプロビジョニングについて
このトピックでは、Azure AD Connect の構成モデルについて説明します。 このモデルは宣言型のプロビジョニングと呼ばれ、構成の変更を簡単に実行することができます。 このトピックで説明する多くの内容は高度であるため、ほとんどの顧客シナリオで必要ありません。

## <a name="overview"></a>概要
宣言型のプロビジョニングでは、ソースの接続されたディレクトリからのオブジェクトを処理します。また、オブジェクトと属性をソースからターゲットに変換する方法を決定します。 オブジェクトは同期パイプラインで処理されます。このパイプラインは受信規則と送信規則とで同じです。 受信規則はコネクタ スペースからメタバースに対する規則であり、送信規則はメタバースからコネクタ スペースに対する規則です。

![同期パイプラインの例を示す図。](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

パイプラインには何種類かのモジュールがあります。 各モジュールは、オブジェクトの同期における各概念に対応しています。

![パイプライン内のモジュールを示す図。](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Source (ソース): ソース オブジェクト
* [Scope (スコープ)](#scope): スコープ内のすべての同期規則を特定
* [Join (結合)](#join): コネクタ スペースとメタバースの間の関係を決定
* Transform (変換): 属性の変換とフローの方法を計算
* [Precedence (優先順位)](#precedence): 属性のコントリビューションの競合を解決
* Target (ターゲット): ターゲット オブジェクト

## <a name="scope"></a>Scope
スコープ モジュールはオブジェクトを評価し、スコープ内にあり、処理に含める必要のある規則を決定します。 オブジェクトの属性値に応じて、各種同期規則がスコープ内にあるかどうか評価されます。 たとえば、Exchange のメールボックスを持たない無効なユーザーには、メールボックスを持つ有効なユーザーとは異なる規則が適用されます。  
![オブジェクトのスコープ モジュールを示す図。](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

スコープはグループおよび句として定義されます。 句はグループ内にあります。 グループ内のすべての句の間で論理 AND が使用されます。 たとえば、(department = IT AND country = Denmark) などです。 グループ間では 論理 OR が使用されます。

![Scope](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
この図のスコープは、(department = IT AND country = Denmark) OR (country=Sweden) となっています。 グループ 1 とグループ 2 のいずれかが true に評価される場合、規則はスコープに含まれています。

スコープ モジュールでは、次の演算がサポートされています。

| 操作 | 説明 |
| --- | --- |
| EQUAL、NOTEQUAL |値が属性内の値と等しいかどうかを評価する文字列の比較。 複数値の属性については、ISIN と ISNOTIN を参照してください。 |
| LESSTHAN、LESSTHAN_OR_EQUAL |値が属性内の値未満であるかどうかを評価する文字列の比較。 |
| CONTAINS、NOTCONTAINS |値が属性内の値の中で見つかるかどうかを評価する文字列の比較。 |
| STARTSWITH、NOTSTARTSWITH |値が属性内の値の先頭にあるかどうかを評価する文字列の比較。 |
| ENDSWITH、NOTENDSWITH |値が属性内の値の末尾にあるかどうかを評価する文字列の比較。 |
| GREATERTHAN、GREATERTHAN_OR_EQUAL |値が属性内の値より大きいかどうかを評価する文字列の比較。 |
| ISNULL、ISNOTNULL |オブジェクトに属性が存在しないかどうかを評価します。 属性が存在しない場合は null となり、規則はスコープに含まれます。 |
| ISIN、ISNOTIN |定義されている属性に値が存在するかどうかを評価します。 この演算は EQUAL と NOTEQUAL の複数値バージョンです。 属性は複数値の属性であることが想定され、値がいずれかの属性値内に見つかる場合、規則はスコープに含まれます。 |
| ISBITSET、ISNOTBITSET |特定のビットが設定されているかどうかを評価します。 たとえば、userAccountControl 内のビットを評価して、ユーザーが有効であるか無効であるかを確認するために使用できます。 |
| ISMEMBEROF、ISNOTMEMBEROF |この値には、コネクタ スペース内のグループに対する DN が含まれている必要があります。 オブジェクトが指定されたグループのメンバーである場合、規則はスコープに含まれます。 |

## <a name="join"></a>Join
同期パイプライン内の結合モジュールは、ソース内のオブジェクトとターゲット内のオブジェクトの関係を特定するためのものです。 受信規則では、この関係は、メタバース内のオブジェクトに対する関係を見つけるためのコネクタ スペース内のオブジェクトです。  
![Join between cs and mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
その目的は、関連付ける必要のある、別のコネクタによって作成されたオブジェクトが既にメタバースに存在するかどうかを確認することです。 たとえば、account-resource フォレストでは、account フォレストのユーザーを resource フォレストのユーザーと結合する必要があります。

結合は、コネクタ スペース オブジェクトを同じメタバース オブジェクトに結合するために、主に受信規則で使用されます。

結合は 1 つ以上のグループとして定義されます。 グループの中には句があります。 グループ内のすべての句の間で論理 AND が使用されます。 グループ間では 論理 OR が使用されます。 グループは上から下へ順番に処理されます。 1 つのグループに対してターゲット内に 1 つだけ一致するオブジェクトがあった場合、他の結合規則は評価されません。 オブジェクトが 1 つも見つからないか、複数のオブジェクトが見つかった場合は、次の規則のグループが処理されます。 そのため、規則を作成するときは、最もはっきりしたものを最初に作成し、最もあいまいなものを最後に作成してください。  
![Join definition](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
この図の結合は、上から下へ処理されます。 最初に同期パイプラインが employeeID で一致するものがあるかどうかを確認します。 一致するものがない場合は、2 番目の規則によってアカウント名をオブジェクトの結合に使用できるかどうかが確認されます。 それでも一致するものがない場合は、最後の 3 番目の規則により、ユーザー名を使ってよりあいまいな照合が行われます。

すべての結合規則が評価されても一致するものが 1 つでない場合は、 **[説明]** ページの **[リンクの種類]** が使用されます。 このオプションが **[プロビジョニング]** に設定されている場合は、ターゲットの新しいオブジェクトが作成されます。  
![開いた [リンクの種類] ドロップダウン メニューを示すスクリーンショット。](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

オブジェクトには、スコープ内の結合規則を持つ同期規則が 1 つだけ必要です。 結合が定義されている複数の同期規則がある場合、エラーが発生します。 優先順位は、結合の競合の解決には使用されません。 同じ受信/送信方向で属性をフローさせるためには、オブジェクトにスコープ内の結合規則が必要です。 同じオブジェクトに送受信双方向で属性をフローさせる必要がある場合は、結合が設定された受信と送信両方の同期規則が必要です。

ターゲット コネクタ スペースへのオブジェクトのプロビジョニングを試みる際、送信結合では特殊な処理を行います。 最初に、DN 属性を使って逆結合が試されます。 同じ DN のオブジェクトがターゲット コネクタ スペースに既に存在する場合、オブジェクトは結合されます。

結合モジュールは、新しい同期規則がスコープに入ってきた場合にのみ評価されます。 いったんオブジェクトが結合されると、結合条件が満たされなくなっても、切断されることはありません。 オブジェクトを切断するには、そのオブジェクトを結合した同期規則がスコープから出て行く必要があります。

### <a name="metaverse-delete"></a>メタバースの削除
**[リンクの種類]** が **[プロビジョニング]** または **[StickyJoin]** に設定された同期規則がスコープに 1 つ存在している限り、メタバース オブジェクトはそのままとなります。 StickyJoin は、コネクタによるメタバースへの新しいオブジェクトのプロビジョニングが許可されていない場合に使用されますが、オブジェクトが結合されている場合、ソースの側で先に削除しておかないと、メタバース オブジェクトを削除できません。

メタバース オブジェクトが削除されると、出力同期規則に関連付けられているすべてのオブジェクト ( **プロビジョニング** とマークされているもの) が削除とマークされます。

## <a name="transformations"></a>変換
変換は、ソースからターゲットへの属性のフローの方法を定義するために使用されます。 フローには、直接、定数、式のいずれかの **フローの種類** を設定できます。 直接フローの場合、属性値は変換が行われることなく、そのままフローされます。 定数値の場合、指定の値を設定します。 式の場合、宣言型のプロビジョニングの式言語を使用して変換の方法を指定します。 式言語の詳細については、 [宣言型のプロビジョニングの式言語](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) に関するトピックを参照してください。

![Provision or join](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

**[Apply once (1 度だけ適用する)]** チェック ボックスをオンにすると、オブジェクトの作成当初にのみ属性が設定されるように定義できます。 たとえば、この構成を使用すると、新しいユーザー オブジェクトの初期パスワードを設定できます。

### <a name="merging-attribute-values"></a>属性値の結合
属性フローには、複数値の属性をいくつかの異なるコネクタから結合する必要があるかどうかを判断する設定があります。 既定値は **Update** です。これは、優先順位の最も高い同期規則が優先的に実行されることを示します。

![[マージの種類] ドロップダウン メニューが開いた [変換の追加] セクションを示すスクリーンショット。](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

**Merge** と **MergeCaseInsensitive** もあります。 これらのオプションを使用すると、異なるソースの値を結合できます。 たとえば、それを使用して、いくつかの異なるフォレストのメンバー属性や proxyAddresses 属性を結合できます。 このオプションを使用する場合、オブジェクトのスコープ内にあるすべての同期規則では、同じ結合の種類を使用する必要があります。 あるコネクタから **Update** を定義し、別のコネクタから **Merge** を定義することはできません。 試した場合は、エラーが発生します。

**Merge** と **MergeCaseInsensitive** では、重複する属性値の処理方法が異なります。 同期エンジンは、重複する値がターゲット属性に挿入されていないことを確認します。 **MergeCaseInsensitive** を使用すると、大文字小文字のみが異なる重複する値は表示されなくなります。 たとえば、"SMTP:bob@contoso.com" と "smtp:bob@contoso.com" は両方ともターゲット属性に表示されなくなります。 **Merge** は、正確な値だけを調べるため、大文字小文字のみが異なる複数の値は表示される可能性があります。

オプション **Replace** は **Update** と同じですが、使用されていません。

### <a name="control-the-attribute-flow-process"></a>属性フローの処理の制御
複数の受信同期規則が同じメタバース属性に提供されるよう構成されると、その後、優先順位は、優先される規則を決定するために使用されます。 優先順位の最も高い (数値が最も小さい) 同期規則は値を提供します。 送信規則についても同じことになります。 優先順位の最も高い同期規則が優先され、接続されたディレクトリに値を提供します。

場合によっては、同期規則は、値を提供するのではなく、他の規則の動作を決定する必要があります。 この例では、いくつかの特殊なリテラルが使用されています。

受信同期規則の場合、リテラルの **NULL** を使用すると、提供する値がフローにないことを示すことができます。 優先順位の低い別の規則は値を提供できます。 値を提供するルールがない場合、メタバース属性は削除されます。 送信規則では、すべての同期規則が処理された後の最終的な値が **NULL** が場合、接続されたディレクトリからこの値が削除されます。

リテラル **AuthoritativeNull** は **NULL** に似ていますが、優先順位の低い規則は値を提供できないという点が異なります。

また、属性フローでは **IgnoreThisFlow** も使用できます。 これは、提供するものがないことを示す意味では NULL に似ています。 違いは、ターゲットに既に存在する値を削除しないことです。 属性フローがそこにあったことがないようなものです。

たとえば次のようになります。

*Out to AD - User Exchange hybrid* では、次のフローが見つかります。  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
この式は、ユーザーのメールボックスが Azure AD にある場合に属性を Azure AD から AD へフローするという意味になります。 Azure AD にない場合は、Active Directory に何もフローされません。 この場合、既存の値が AD で保持されます。

### <a name="importedvalue"></a>ImportedValue
ImportedValue 関数は、属性名を角かっこではなく引用符で囲む必要がある点で、他のすべての関数とは異なっています。次に例を示します。  
`ImportedValue("proxyAddresses")`.

通常、同期する際は、まだエクスポートされていない場合であっても、エクスポート中にエラーが発生した場合であっても、属性では予想される値を使用します ("top of the tower")。 受信同期では、接続されたディレクトリにまだ届いていない属性も最終的には届くと見なされます。 また、接続されたディレクトリで確認された値のみを同期することが重要です ("hologram and delta import tower")。

この関数の例は、標準の同期規則 *In from AD – User Common from Exchange* にあります。 ハイブリッド Exchange の場合、Exchange Online によって追加された値を同期する必要があるのは、その値が正常エクスポートされたことが確認されたときのみです。  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>優先順位
複数の同期規則が同じ属性値をターゲットに渡そうとしたときに、優先順位の値によってどれが選ばれるかが決まります。 優先順位の最も高い (数値が最も小さい) 同期規則が、競合している属性を渡すことになります。

![Merge Types](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

この順序の指定方法を利用すると、オブジェクトの小さいサブセットについて、より正確な属性フローを定義することができます。 たとえば、そのまま使用できる規則により、有効なアカウント (**User AccountEnabled**) の属性は、他のアカウントの属性より優先されます。

優先順位はコネクタ間で定義できます。 そのため、より適切なデータを持つコネクタが値を先に渡せます。

### <a name="multiple-objects-from-the-same-connector-space"></a>同じコネクタ スペースからの複数のオブジェクト
同じコネクタ スペース内の複数のオブジェクトを同じメタバース オブジェクトに結合する場合、優先順位を調整する必要があります。 複数のオブジェクトが同じ同期規則のスコープに含まれる場合、同期エンジンは優先順位を決めることができません。 どのソース オブジェクトからメタバースに値を渡すかが明確ではないのです。 この構成は、ソース内の属性の値が同じであっても、あいまいとしてレポートされます。  
![透明な赤の X オーバーレイを持つ同じ mv オブジェクトに結合された複数のオブジェクトを示す図。 ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

このシナリオでは、同期規則のスコープを変更して、ソース オブジェクトがスコープ内に異なる同期規則を持つようにする必要があります。 そうすれば、異なる優先順位を定義できます。  
![Multiple objects joined to the same mv object](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>次のステップ
* 式言語について詳しくは、「 [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)」をご覧ください。
* 宣言型のプロビジョニングをすぐに使用する方法については、「 [既定の構成について](concept-azure-ad-connect-sync-default-configuration.md)」をご覧ください。
* 宣言型のプロビジョニングを使用して現実に即した変更を実施する方法については、「 [既定の構成を変更する方法](how-to-connect-sync-change-the-configuration.md)」をご覧ください。
* ユーザーと連絡先がどのように連携して動作するのかについてさらに詳しく知りたい場合には、「 [ユーザーと連絡先について](concept-azure-ad-connect-sync-user-and-contacts.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

**参照トピック**

* [Azure AD Connect 同期:関数参照](reference-connect-sync-functions-reference.md)
