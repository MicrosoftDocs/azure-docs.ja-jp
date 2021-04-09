---
title: Azure Web アプリケーション ファイアウォールと Azure Policy
description: Azure Policy と組み合わせた Azure Web アプリケーション ファイアウォール (WAF) は、組織の標準を適用し、WAF リソースの大規模なコンプライアンスを評価するのに役立ちます
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589459"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web アプリケーション ファイアウォールと Azure Policy

Azure Policy と組み合わせた Azure Web アプリケーション ファイアウォール (WAF) は、組織の標準を適用し、WAF リソースの大規模なコンプライアンスを評価するのに役立ちます。 Azure Policy は、環境の全体的な状態を評価するための集計ビューを提供するガバナンス ツールであり、リソースごと、およびポリシーごとの粒度でドリルダウンできる機能が備わっています。 Azure Policy は、既存のリソースの一括修復と新しいリソースの自動修復を使用して、お客様のリソースでコンプライアンスを実現するのにも役立ちます。

## <a name="azure-policy-for-web-application-firewall"></a>Web アプリケーション ファイアウォール用の Azure Policy

WAF リソースを管理するための組み込みの Azure Policy 定義がいくつかあります。 ポリシー定義とその機能の内訳は、次のとおりです。

1. **Azure Front Door Service に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある**:Azure Front Door Service は、リソースの作成時に WAF が存在するかどうかで評価されます。 このポリシーには、3 つの効果 (Audit、Deny、Disabled) があります。 Audit は、Azure Front Door Service に WAF がない場合に追跡し、Azure Front Door Service が準拠していないものをユーザーが確認できるようにします。 Deny は、WAF がアタッチされていない場合に、Azure Front Door Service を作成できないようにします。 Disabled はこのポリシーをオフにします。

2. **Application Gateway に対して Web アプリケーション ファイアウォール (WAF) を有効にする必要がある**:Application Gateway は、リソースの作成時に WAF が存在するかどうかで評価されます。 このポリシーには、3 つの効果 (Audit、Deny、Disabled) があります。 Audit は、Application Gateway に WAF がない場合に追跡し、Application Gateway が準拠していないものをユーザーが確認できるようにします。 Deny は、WAF がアタッチされていない場合に、Application Gateway を作成できないようにします。 Disabled はこのポリシーをオフにします。

3. **Web アプリケーション ファイアウォール (WAF) で Azure Front Door Service の指定されたモードを使用する必要がある**:Azure Front Door Service のすべての Web アプリケーション ファイアウォール ポリシーで、[検出] または [防止] モードの使用をアクティブにするように要求します。 このポリシーには、3 つの効果 (Audit、Deny、Disabled) があります。 Audit は、WAF が指定されたモードに合わない場合に追跡します。 Deny は、WAF が正しいモードになっていない場合は作成されないようにします。 Disabled はこのポリシーをオフにします。

4. **Web アプリケーション ファイアウォール (WAF) で Application Gateway の指定されたモードを使用する必要がある**:Application Gateway のすべての Web アプリケーション ファイアウォール ポリシーで、[検出] または [防止] モードの使用をアクティブにするように要求します。 このポリシーには、3 つの効果 (Audit、Deny、Disabled) があります。 Audit は、WAF が指定されたモードに合わない場合に追跡します。 Deny は、WAF が正しいモードになっていない場合は作成されないようにします。 Disabled はこのポリシーをオフにします。

## <a name="launch-an-azure-policy"></a>Azure Policy を起動する

1.  Azure のホーム ページで、検索バーに「Policy」と入力し、[Azure Policy] アイコンをクリックします。

2.  Azure Policy サービスの **[作成]** で **[割り当て]** を選択します。

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Azure Policy 内の [割り当て] タブ":::

3.  [割り当て] ページで、上部にある **[ポリシーの割り当て]** アイコンを選択します。

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="[ポリシーの割り当て] ページの [基本] タブ":::

4.  [ポリシーの割り当て] ページの [基本] タブで、次のフィールドを更新します。
    1.  **[スコープ]** : ポリシー定義によって影響を受ける Azure サブスクリプションとリソース グループを選択します。
    2.  **除外**:ポリシーの割り当てから除外するリソースをスコープから選択します。
    3.  **ポリシーの定義**:除外対象のスコープに適用するポリシー定義を選択します。 検索バーに「Web アプリケーション ファイアウォール」と入力して、関連する Web アプリケーション ファイアウォール Azure Policy を選択します。

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="[使用可能な定義] ページの [ポリシー定義] タブのスクリーンショット。":::

5.  **[パラメーター]** タブを選択し、ポリシーの割り当てのパラメーターを更新します。 パラメーター名の横にある情報アイコンをポイントすると、パラメーターの機能がさらに明確になります。

6.  **[確認と作成]** を選択して、ポリシーの割り当てを完了させます。 ポリシーの割り当てが新しいリソースに対してアクティブになるまで、約 15 分かかります。
