---
title: Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能 | Microsoft Docs
description: " Azure Security Center のアダプティブ ネットワークのセキュリティ強化機能を有効にする方法を説明します。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 2f82f3fe6f5cb6808ba606125ee0869475a60274
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295885"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能
Azure Security Center のアダプティブ ネットワークのセキュリティ強化機能を構成する方法を説明します。

## <a name="what-is-adaptive-network-hardening"></a>アダプティブ ネットワークのセキュリティ強化機能とは
[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) を適用してリソースとの間でやり取りされるトラフィックをフィルター処理することにより、ネットワーク セキュリティ ポスチャを向上させることができます。 ただし、NSG を通過する実際のトラフィックが、定義される NSG ルールのサブセットとなる場合もあります。 このような場合は、実際のトラフィック パターンに基づいて NSG ルールを強化することによって、セキュリティ ポスチャをさらに向上させることができます。

アダプティブ ネットワークのセキュリティ強化機能によって、NSG ルールをさらに強化するための推奨事項が提供されます。 実際のトラフィック、既知の信頼された構成、脅威インテリジェンス、および侵害に関するその他のインジケーターを考慮する機械学習アルゴリズムを使用し、特定の IP/ポート タプルからのトラフィックのみを許可する推奨事項を提示します。

たとえば、既存の NSG ルールがポート 22 の 140.20.30.10/24 からのトラフィックを許可するとします。 アダプティブ ネットワークのセキュリティ強化機能の推奨事項は、分析に基づいて、範囲を狭め、140.23.30.10/29 からのトラフィックを許可します。これは、より狭い IP 範囲で、そのポートへのその他すべてのトラフィックを拒否します。

![ネットワーク強化のビュー](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> アダプティブ ネットワークのセキュリティ強化機能の推奨事項は、次のポートでサポートされます。22、3389、21、23、445、4333、3306、1433、1434、53、20、5985、5986、5432、139、66、1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>アダプティブ ネットワークのセキュリティ強化機能のアラートとルールの表示

1. Security Center で、 **[ネットワーク]**  ->  **[Adaptive Network Hardening]** \(アダプティブ ネットワークのセキュリティ強化機能\) を選択します。 ネットワーク VM は 3 つの別個のタブの下に表示されます。
   * **異常なリソース**:アダプティブ ネットワークのセキュリティ強化アルゴリズムを実行することによってトリガーされた推奨事項とアラートが存在する VM。 
   * **正常なリソース**:アラートと推奨事項がない VM。
   * **スキャンされていないリソース**:以下のいずれかの理由でアダプティブ ネットワークのセキュリティ強化アルゴリズムを実行できない VM。
      * **VM がクラシック VM である**:Azure Resource Manager VM のみがサポートされています。
      * **使用できる十分なデータがない**:トラフィック強化に関する正確な推奨事項を生成するには、Security Center に少なくとも 30 日間のトラフィック データが必要です。
      * **VM が ASC 標準によって保護されていない**:Security Center の Standard 価格レベルに設定された VM のみ、この機能を利用できます。

     ![異常なリソース](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. **[異常なリソース]** タブから、そのアラートと適用する推奨されるセキュリティ強化ルールを表示する VM を選択します。

    ![強化アラート](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>アダプティブ ネットワークのセキュリティ強化機能の推奨されるルールの確認と適用

1. **[異常なリソース]** タブから、VM を選択します。 アラートと推奨される強化ルールが一覧表示されます。

     ![強化ルール](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **[ルール]** タブには、アダプティブ ネットワークのセキュリティ強化機能が推奨するルールがリストされます。 **[アラート]** タブには、推奨されるルールで許可されている IP 範囲内ではないリソースを通過するトラフィックのために生成されたアラートが一覧表示されます。

2. ルールのパラメーターの一部を変更する場合は、「[ルールの変更](#modify-rule)」で説明されているように、変更できます。
   > [!NOTE]
   > ルールを[削除](#delete-rule)することも[追加](#add-rule)することもできます。

3. NSG を適用する VM を選択し、 **[適用]** をクリックします。

      > [!NOTE]
      > 適用されるルールは、VM を保護する NSG に追加されます。 (VM は、その NIC に関連付けられている NSG、VM が置かれているサブネット、またはその両方によって保護されます)

    ![ルールの適用](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### ルールの変更<a name ="modify-rule"> </a>

推奨されたルールのパラメーターを変更することもできます。 たとえば、推奨された IP 範囲を変更することができます。

アダプティブ ネットワークのセキュリティ強化機能のルールを変更するための重要なガイドライン:

* "許可" ルールのパラメーターのみを変更できます。 
* "許可" ルールを "拒否" ルールに変更することはできません。 

  > [!NOTE]
  > "拒否" ルールの作成と変更は、NSG で直接行います。詳細については、「[ネットワーク セキュリティ グループの作成、変更、削除](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)」をご覧ください。

* **"すべてのトラフィックを拒否"** ルールは、ここで表示される唯一の "拒否" ルールのタイプで、変更することはできません。 ただし、そのルールを削除することはできます (「[ルールの削除](#delete-rule)」をご覧ください)。
  > [!NOTE]
  > 既存の NSG 構成に基づいて許可すべきトラフィックが Security Center で識別されなかった場合、アルゴリズムを実行した結果として、 **"すべてのトラフィックを拒否"** ルールが推奨されます。 そのため、推奨されるルールは、指定されたポートに届くすべてのトラフィックを拒否します。 このタイプのルールの名前は、"*システム生成*" として表示されます。 このルールを適用した後は、NSG の実際の名前は、プロトコル、トラフィックの方向、"DENY"、およびランダムな数値から成る文字列になります。

*アダプティブ ネットワークのセキュリティ強化機能ルールを変更するには:*

1. ルールのパラメーターの一部を変更するには、 **[ルール]** タブで、ルールの行の最後にある 3 つのドット (...) をクリックし、 **[編集]** をクリックします。

   ![ルールの編集](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. **[ルールの編集]** ウィンドウで、変更する詳細を更新し、 **[保存]** をクリックします。

   > [!NOTE]
   > **[保存]** をクリックすると、ルールは正常に変更されます。 *ただし、NSG にはまだ適用されていません。* そのルールを適用するには、一覧でそのルールを選択し、 **[適用]** をクリックする必要があります (次の手順で説明します)。

   ![ルールの編集](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 更新されたルールを適用するには、一覧から更新されたルールを選択し、 **[適用]** をクリックします。

    ![ルールの適用](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### 新しいルールの追加<a name ="add-rule"> </a>

Security Center によって推奨されていない "許可" ルールを追加することができます。

> [!NOTE]
> ここでは、"許可" ルールのみを追加できます。 "拒否" ルールを追加する場合は、NSG で直接行うことができます。 詳細については、「[ネットワーク セキュリティ グループを作成、変更、削除](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)」をご覧ください。

*アダプティブ ネットワークのセキュリティ強化機能ルールを追加するには:*

1. **[ルールの追加]** (左上隅にあります) をクリックします。

   ![ルールの追加](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. **[新しいルール]** ウィンドウで、詳細を入力し、 **[追加]** をクリックします。

   > [!NOTE]
   > **[追加]** をクリックすると、ルールは正常に追加され、その他の推奨されているルールと一緒に一覧表示されます。 ただし、NSG にはまだ適用されていません。 そのルールをアクティブにするには、一覧でそのルールを選択し、 **[適用]** をクリックする必要があります (次の手順で説明します)。

3. 新しいルールを適用するには、一覧から新しいルールを選択し、 **[適用]** をクリックします。

    ![ルールの適用](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### ルールを削除する<a name ="delete-rule"> </a>

推奨されるルールが不要な場合は、削除できます。 たとえば、推奨されるルールを適用すると、正当なトラフィックがブロックされると判断することがあるかもしれません。

*アダプティブ ネットワークのセキュリティ強化機能ルールを削除するには:*

1. **[ルール]** タブで、ルールの行の最後にある 3 つのドット (...) をクリックし、 **[削除]** をクリックします。  

    ![強化ルール](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

