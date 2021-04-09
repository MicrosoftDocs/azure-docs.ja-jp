---
title: チュートリアル:Azure Front Door 用の WAF ポリシーを作成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して Web アプリケーション ファイアウォール (WAF) ポリシーを作成する方法について説明します。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: victorh
ms.openlocfilehash: 8b1d1007e817bafe3d75f0f0d7c3fc6eb5470854
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729472"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure Front Door で Web アプリケーション ファイアウォール ポリシーを作成する

このチュートリアルでは、基本的な Azure Web Application Firewall (WAF) ポリシーを作成し、Azure Front Door でフロントエンド ホストに適用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAF ポリシーを作成する
> * フロントエンド ホストに関連付ける
> * WAF 規則を構成する

## <a name="prerequisites"></a>前提条件

Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](../../frontdoor/quickstart-create-front-door.md)で説明されている手順に従います。 

## <a name="create-a-web-application-firewall-policy"></a>Web アプリケーション ファイアウォールのポリシーを作成します

最初に、ポータルを使用して管理された既定の規則セット (DRS) で基本的な WAF ポリシーを作成します。 

1. 画面の左上で **[リソースの作成]** を選択し、**WAF** を検索して、 **[Web application firewall (Preview)]\(Web アプリケーション ファイアウォール (プレビュー)\)** を選択して、 **[作成]** を選択します。
2. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | サブスクリプション            |Front Door のサブスクリプションの名前を選択します。|
    | Resource group          |Front Door のリソース グループの名前を選択します。|
    | ポリシー名             |WAF ポリシーの一意の名前を入力します。|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="[Create a W A F policy]\(W A F ポリシーの作成\) ページのスクリーンショット。[確認と作成] ボタンのほか、サブスクリプション、リソース グループ、ポリシー名の各リスト ボックスが表示されています。" border="false":::

3. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[関連付け]** タブで、 **[フロントエンド ホストの追加]** を選択し、次の設定を入力して、 **[追加]** を選択します。

    | 設定                 | Value                                              |
    | ---                     | ---                                                |
    | フロント ドア              | Front Door プロファイルの名前を選択します。|
    | フロントエンド ホスト           | Front Door ホストの名前を選択し、 **[追加]** を選択します。|
    
    > [!NOTE]
    > フロントエンド ホストが WAF ポリシーに関連付けられている場合は、淡色表示になります。最初に関連付けられているポリシーからフロントエンド ホストを削除してから、新しい WAF ポリシーにフロントエンド ホストを再度関連付ける必要があります。
1. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

## <a name="configure-web-application-firewall-rules-optional"></a>Web アプリケーション ファイアウォール規則を構成する (省略可能)

### <a name="change-mode"></a>モードを変更する

WAF ポリシーを作成するとき、既定では WAF ポリシーは **検出** モードになります。 **検出** モードでは、WAF はすべての要求をブロックせず、代わりに、WAF 規則に一致する要求は WAF ログに記録されます。
WAF の動作を確認するには、モードの設定を **[検出]** から **[防止]** に変更できます。 **防止** モードでは、既定の規則セット (DRS) で定義されている規則に一致する要求はブロックされ、WAF ログに記録されます。

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="[ポリシー設定] セクションのスクリーンショット。[モード] トグルが [防止] に設定されています。" border="false":::

### <a name="custom-rules"></a>カスタム規則

**[カスタム ルール]** セクションの下の **[Add custom rule]\(カスタム ルールの追加\)** を選択することで、カスタム ルールを作成できます。 これで、カスタム ルールの構成ページが起動されます。 次に示すのは、クエリ文字列に **blockme** が含まれているときに要求をブロックするというカスタム ルールの構成方法の例です。

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="カスタム ルールの構成ページのスクリーンショット。QueryString 変数に blockme という値が含まれているかどうかを調べるルールの設定が表示されています。" border="false":::

### <a name="default-rule-set-drs"></a>既定の規則セット (DRS)

Azure で管理される既定の規則セットは既定で有効になります。 現在の既定のバージョンは DefaultRuleSet_1.0 です。 WAF の **[管理されているルール]** 、 **[割り当て]** のドロップダウン リストで、最近提供されたルール セット Microsoft_DefaultRuleSet_1.1 を使用できます。

規則グループ内の個々の規則を無効にするには、その規則グループ内の規則を展開し、規則番号の前の **チェック ボックス** をオンにして、上のタブの **[無効]** を選択します。 規則セット内の個々の規則のアクションの種類を変更するには、規則番号の前にあるチェック ボックスをオンにして、上のタブの **[Change action]\(アクションの変更\)** を選択します。

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="[Managed rules]\(管理されているルール\) ページのスクリーンショット。ルール セット、ルール グループ、ルールのほか、[有効]、[無効]、[アクションを変更する] の各ボタンが表示されています。1 つのルールのチェック ボックスがオンになっています。" border="false":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Front Door の詳細](../../frontdoor/front-door-overview.md)を確認する
