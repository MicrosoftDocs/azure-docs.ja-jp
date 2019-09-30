---
title: 条件付きアクセス - 場所ごとにアクセスをブロックする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、IP の場所によってリソースへのアクセスをブロックします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b31fc3aed0d412646d9924e87170dffcd5145409
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575990"
---
# <a name="conditional-access-block-access-by-location"></a>条件付きアクセス:場所ごとにアクセスをブロックする

条件付きアクセスで場所の条件を使用すると、ユーザーのネットワークの場所に基づいて、クラウド アプリへのアクセスを制御できます。 場所の条件は、一般に、トラフィックの発信元として不適切であると組織が認識している国からのアクセスをブロックするために使用されます。

## <a name="define-locations"></a>場所を定義する

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しい場所]** を選択します。
1. 場所に名前を付けます。
1. その場所または**国/地域**を構成する、外部からアクセス可能な特定の IPv4 アドレス範囲がわかっている場合は、 **[IP 範囲]** を選択します。
   1. 指定しようとしている場所の **[IP 範囲]** を指定するか、 **[国/地域]** を選択します。
      * 国/地域を選択した場合、必要に応じて不明な領域を含めることもできます。
1. **[保存]** を選択します。

条件付きアクセスにおける場所の条件の詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](location-condition.md)」の記事を参照してください。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[完了]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択し、 **[完了]** を選択します。
1. **[条件]**  >  **[Location]\(場所\)** で
   1. **[Configure]\(構成する\)** を **[はい]** に設定します
   1. **[Include]\(含める\)** で **[選択された場所]** を選択します
   1. 組織に対して作成したブロック対象の場所を選択します。
   1. **[Select]\(選択する\)**  >  **[Done]\(完了\)**  >  **[Done]\(完了\)** の順にクリックします。
1. **[アクセス制御]**  >  **[Block]\(ブロック\)** で、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

## <a name="next-steps"></a>次の手順

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
