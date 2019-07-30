---
title: Azure portal を使用して Azure Front Door 用の Web アプリケーション ファイアウォール ポリシーを作成する
titlesuffix: Azure web application firewall
description: Azure portal を使用して Web アプリケーション ファイアウォール (WAF) ポリシーを作成する方法について説明します。
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846384"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Azure portal を使用して Azure Front Door 用の WAF ポリシーを作成する

この記事では、基本的な Azure Web アプリケーション ファイアウォール (WAF) ポリシーを作成し、Azure Front Door でフロントエンド ホストに適用する方法について説明します。

## <a name="prerequisites"></a>前提条件

Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います。 

## <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

最初に、ポータルを使用して管理された既定の規則セット (DRS) で基本的な WAF ポリシーを作成します。 

1. 画面の左上で **[リソースの作成]** を選択し、**WAF** を検索して、 **[Web application firewall (Preview)]\(Web アプリケーション ファイアウォール (プレビュー)\)** を選択して、 **[作成]** を選択します。
2. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | Subscription            |Front Door のサブスクリプションの名前を選択します。|
    | Resource group          |Front Door のリソース グループの名前を選択します。|
    | ポリシー名             |WAF ポリシーの一意の名前を入力します。|

   ![WAF ポリシーを作成する](./media/waf-front-door-create-portal/basic.png)

3. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[関連付け]** タブで、 **[フロントエンド ホストの追加]** を選択し、次の設定を入力して、 **[追加]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | フロント ドア              | Front Door プロファイルの名前を選択します。|
    | フロントエンド ホスト           | Front Door ホストの名前を選択し、 **[追加]** を選択します。|
    
    > [!NOTE]
    > フロントエンド ホストが WAF ポリシーに関連付けられている場合は、淡色表示になります。最初に関連付けられているポリシーからフロントエンド ホストを削除してから、新しい WAF ポリシーにフロントエンド ホストを再度関連付ける必要があります。
1. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

## <a name="configure-waf-rules-optional"></a>WAF 規則を構成する (省略可能)

### <a name="change-mode"></a>モードを変更する

WAF ポリシーを作成するとき、既定では WAF ポリシーは**検出**モードになります。 **検出**モードでは、WAF はすべての要求をブロックせず、代わりに、WAF 規則に一致する要求は WAF ログに記録されます。
WAF の動作を確認するには、モードの設定を **[検出]** から **[防止]** に変更できます。 **防止**モードでは、既定の規則セット (DRS) で定義されている規則に一致する要求はブロックされ、WAF ログに記録されます。

 ![WAF ポリシー モードを変更する](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>カスタム規則

**[カスタム ルール]** セクションの下の **[Add custom rule]\(カスタム ルールの追加\)** を選択することで、カスタム ルールを作成できます。 これで、カスタム ルールの構成ページが起動されます。 次に示すのは、クエリ文字列に **blockme** が含まれているときに要求をブロックするというカスタム ルールの構成方法の例です。

![WAF ポリシー モードを変更する](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>既定の規則セット (DRS)

Azure で管理される既定の規則セットは既定で有効になります。 規則グループ内の個々の規則を無効にするには、その規則グループ内の規則を展開し、規則番号の前の**チェック ボックス**をオンにして、上のタブの **[無効]** を選択します。 規則セット内の個々の規則のアクションの種類を変更するには、規則番号の前にあるチェック ボックスをオンにして、上のタブの **[Change action]\(アクションの変更\)** を選択します。

 ![WAF 規則セットを変更する](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>次の手順

- [Azure Web アプリケーション ファイアウォール](waf-overview.md)について学習します。
- [Azure Front Door](front-door-overview.md) の詳細を確認します。
