---
title: Application Gateway 用の Web アプリケーション ファイアウォール (WAF) ポリシーの作成
description: Application Gateway 用の Web アプリケーション ファイアウォール ポリシーの作成方法について説明します。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518823"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Application Gateway 用の Web アプリケーション ファイアウォール ポリシーの作成

WAF ポリシーをリスナーに関連付けると、1 つの WAF の背後にある複数のサイトを異なるポリシーで保護できるようになります。 たとえば、WAF の背後に 5 つのサイトがある場合、5 つの個別の WAF ポリシー (リスナーごとに 1 つ) を設けて、それぞれ 1 つのサイトの除外、カスタム ルール、マネージド ルール セットをカスタマイズし、他の 4 つに影響させないようにできます。 1 つのポリシーをすべてのサイトに適用する場合は、ポリシーを個々のリスナーではなくアプリケーション ゲートウェイに関連付けて、グローバルに適用できます。 ポリシーを、パスベースのルーティング規則に適用することもできます。 

ポリシーは、必要な数だけ作成できます。 作成したポリシーは、有効にするために 1 つのアプリケーション ゲートウェイに関連付ける必要がありますが、アプリケーション ゲートウェイとリスナーの任意の組み合わせに関連付けることもできます。 

アプリケーション ゲートウェイにポリシーが適用されていて、そのアプリケーション ゲートウェイの 1 つのリスナーに別のポリシーを適用した場合、リスナーのポリシーは有効になりますが、割り当てられているリスナーに対してのみ有効になります。 特定のポリシーが割り当てられていない他のすべてのリスナーには、依然として Application Gateway ポリシーが適用されます。 

   > [!NOTE]
   > ファイアウォール ポリシーが WAF に関連付けられている場合は、その WAF に関連付けられているポリシーが常に存在している必要があります。 このポリシーは上書きできますが、WAF からのポリシーの関連付け解除は完全にはサポートされていません。 

新しい Web アプリケーション ファイアウォールの WAF 設定 (カスタム ルール、マネージド ルール セットの構成、除外など) はすべて WAF ポリシーの内部にあります。 既存の WAF がある場合、これらの設定がまだ WAF 構成にある可能性があります。新しい WAF ポリシーに移行する手順については、この記事の後半の「[WAF 構成の WAF ポリシーへの移行](#migrate)」を参照してください。 

## <a name="create-a-policy"></a>ポリシーの作成

まず、Azure portal を使用して、マネージド既定のルール セット (DRS) を持つ基本の WAF ポリシーを作成します。

1. ポータルの左上で、 **[リソースの作成]** を選択します。 **WAF** を検索し、 **[Web アプリケーション ファイアウォール]** 、 **[作成]** の順に選択します。
2. **[WAF ポリシーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

   |設定  |値  |
   |---------|---------|
   |次に対するポリシー     |リージョンの WAF (Application Gateway)|
   |サブスクリプション     |サブスクリプション名を選択します|
   |Resource group     |リソース グループを選択します|
   |ポリシー名     |WAF ポリシーの一意の名前を入力します。|
3. **[関連付け]** タブで、次の設定のいずれかを入力し、 **[追加]** を選択します。

   |設定  |値  |
   |---------|---------|
   |Application Gateway の関連付け     |Application Gateway プロファイル名を選択します。|
   |リスナーの関連付け     |Application Gateway リスナーの名前を選択し、 **[追加]** を選択します。|

   > [!NOTE]
   > ポリシーが既に設定されているアプリケーション ゲートウェイ (またはリスナー) にポリシーを割り当てた場合、元のポリシーは上書きされ、新しいポリシーに置き換えられます。
4. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

   ![WAF ポリシーの基本](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF 規則を構成する (省略可能)

WAF ポリシーを作成するとき、既定では *検出* モードになります。 検出モードでは、WAF で要求がブロックされることはありません。 代わりに、一致する WAF ルールが WAF ログに記録されます。 WAF の動作を確認するには、モードの設定を *[防止]* に変更できます。 防止モードでは、選択した CRS ルールセットで定義されている照合ルールがブロックされるか、WAF ログに記録されます (または両方とも行われます)。

## <a name="managed-rules"></a>マネージド ルール

Azure マネージド OWASP ルールは、既定で有効になっています。 ルール グループ内の個々のルールを無効にするには、そのルール グループ内のルールを展開し、ルール番号の前のチェック ボックスをオンにして、上のタブの **[無効]** を選択します。

[![マネージド ルール](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>カスタム規則

カスタム ルールを作成するには、 **[カスタム ルール]** タブの下の **[カスタム ルールの追加]** を選択します。これで、カスタム ルールの構成ページが開きます。 次のスクリーンショットは、クエリ文字列に "*blockme*" というテキストが含まれている場合に要求をブロックするように構成されたカスタム ルールの例を示しています。

[![カスタム ルールの編集](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>WAF 構成の WAF ポリシーへの移行

既存の WAF をお持ちの場合、ポータルのいくつかの変更点にお気付きかもしれません。 まず、WAF で有効にしたポリシーの種類を識別する必要があります。 考えられる状態は 3 つあります。

- WAF ポリシーなし
- カスタム ルールのみのポリシー
- WAF ポリシー

WAF の状態を確認するには、ポータルでそれを参照します。 WAF 設定が表示されており、Application Gateway ビュー内から変更できる場合、WAF は状態 1 です。

[![WAF 構成](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

**[Web アプリケーション ファイアウォール]** を選択すると、関連付けられているポリシーが表示される場合、WAF は状態 2 または状態 3 です。 ポリシーに移動した後、カスタム ルール、および関連付けられているアプリケーション ゲートウェイ **のみ** が表示される場合は、カスタム ルールのみのポリシーです。

![WAF カスタム規則](../media/create-waf-policy-ag/waf-custom-rules.png)

ポリシー設定とマネージド ルールも表示される場合は、完全な Web アプリケーション ファイアウォール ポリシーです。 

![WAF ポリシーの設定](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>WAF ポリシーへの移行

カスタム ルールのみの WAF ポリシーの場合、新しい WAF ポリシーに移行することができます。 今後、ファイアウォール ポリシーでは、WAF ポリシーの設定、マネージド ルール セット、除外、無効になっているルール グループがサポートされます。 基本的に、アプリケーション ゲートウェイの内部で以前に実行されたすべての WAF 構成は、WAF ポリシーを使って実行されます。 

カスタム ルールのみの WAF ポリシーの編集は無効になっています。 ルールの無効化、除外の追加など、WAF の設定を編集するには、新しいトップ レベルのファイアウォール ポリシー リソースに移行する必要があります。

これを行うには、*Web アプリケーション ファイアウォール ポリシー* を作成し、選択したアプリケーション ゲートウェイおよびリスナーにそれを関連付けます。 この新しいポリシーは、現在の WAF 構成とまったく同じにする必要があります。つまり、すべてのカスタム ルール、除外、無効になっているルールなどを、作成する新しいポリシーにコピーする必要があります。 ポリシーをアプリケーション ゲートウェイに関連付けたら、WAF のルールと設定の変更に進むことができます。 Azure PowerShell でこの操作を行うこともできます。 詳細については、「[WAF ポリシーを既存のアプリケーション ゲートウェイに関連付ける](associate-waf-policy-existing-gateway.md)」を参照してください。

必要な場合は、移行スクリプトを使用して WAF ポリシーに移行できます。 詳しくは、「[Azure PowerShell を使用して Web アプリケーションのファイアウォール ポリシーを移行する](migrate-policy.md)」をご覧ください。

## <a name="force-mode"></a>強制モード

現在の構成とまったく同じポリシーにすべてをコピーしない場合は、WAF を "強制" モードに設定できます。 次の Azure PowerShell コードを実行すると、WAF は force モードになります。 その後、設定が構成とまったく同じではない場合でも、WAF ポリシーを WAF に関連付けることができます。 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

次に、WAF ポリシーをアプリケーション ゲートウェイに関連付ける手順に進みます。 詳細については、「[WAF ポリシーを既存のアプリケーション ゲートウェイに関連付ける](associate-waf-policy-existing-gateway.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Web アプリケーション ファイアウォールの CRS ルール グループとルール](application-gateway-crs-rulegroups-rules.md)の詳細をご確認ください。
