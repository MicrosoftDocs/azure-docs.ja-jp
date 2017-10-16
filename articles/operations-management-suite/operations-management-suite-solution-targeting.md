---
title: "OMS でのソリューションのターゲット設定 | Microsoft Docs"
description: "ソリューションのターゲット設定は Operations Management Suite (OMS) 管理ソリューションの機能で、この機能により、管理ソリューションを特定のエージェント セットに制限することができます。  この記事では、スコープの構成を作成して、ソリューションに適用する方法について説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: cb73a2d7ae57a5a11869259dbe913ae83ffb2b01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-solution-targeting-in-operations-management-suite-oms-to-scope-management-solutions-to-specific-agents-preview"></a>Operations Management Suite (OMS) のソリューションのターゲット設定を使用して、管理ソリューションのスコープを特定のエージェントに制限する (プレビュー)
ソリューションを OMS に追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。  ソリューションを特定のエージェント セットに制限することで、コストを管理し、ソリューション用に収集されるデータの量を制限することもできます。  この記事では、OMS の機能、**ソリューションのターゲット設定**を使用する方法について説明します。この機能を使用すると、ソリューションにスコープを適用することができます。

## <a name="how-to-target-a-solution"></a>ソリューションにターゲットを設定する方法
ソリューションにターゲットを設定するには、次のセクションで説明する 3 つの手順に従います。  それぞれの手順に OMS ポータルと Azure Portal の両方が必要です。


### <a name="1-create-a-computer-group"></a>1.コンピューター グループの作成
スコープに含めるコンピューターを指定するには、Log Analytics で[コンピューター グループ](../log-analytics/log-analytics-computer-groups.md)を作成します。  コンピューター グループは、ログ検索に基づいて作成したり、Active Directory、WSUS グループなどのソースからインポートしたりできます。 [以下で説明](#solutions-and-agents-that-cant-be-targeted)するように、スコープに追加されるのは、Log Analytics に直接接続されているコンピューターのみです。

ワークスペースにコンピューター グループが作成されたら、1 つ以上のソリューションに適用できるスコープ構成に、そのグループを追加します。
 
 
 ### <a name="2-create-a-scope-configuration"></a>手順 2.スコープ構成の作成
 1 つ以上のコンピューター グループが含まれる**スコープ構成**を、1 つ以上のソリューションに適用できます。 
 
 次のプロセスを使用して、スコープ構成を作成します。  

 1. Azure Portal で **Log Analytics** に移動し、ワークスペースを選択します。
 2. ワークスペースのプロパティにある **[ワークスペースのデータソース]** で、**[スコープ構成]** を選択します。
 3. **[追加]** をクリックして、新しいスコープ構成を作成します。
 4. スコープ構成の**名前**を入力します。
 5. **[コンピューター グループの選択]** をクリックします。
 6. 作成したコンピューター グループと、構成に追加するその他のグループを必要に応じて選択します。  **[選択]**をクリックします。  
 6. **[OK]** をクリックして、スコープ構成を作成します。 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3.ソリューションへのスコープ構成の適用。
作成したスコープ構成は、1 つ以上のソリューションに適用できます。  1 つのスコープ構成を複数のソリューションで使用することはできますが、ソリューションが使用できるスコープ構成は、ソリューションごとに 1 つだけです。

次のプロセスを使用して、スコープ構成を適用します。  

 1. Azure Portal で **Log Analytics** に移動し、ワークスペースを選択します。
 2. ワークスペースのプロパティで、**[ソリューション]** を選択します。
 3. スコープを設定するソリューションを選択します。
 4. ソリューションのプロパティにある **[ワークスペースのデータソース]** で、**[Solution Targeting (ソリューションのターゲット設定)]** を選択します。  オプションを利用できない場合、[このソリューションはターゲット設定できません](#solutions-and-agents-that-cant-be-targeted)。
 5. **[スコープ構成の追加]** をクリックします。  このソリューションに構成が既に適用されている場合、このオプションは使用できません。  構成を追加する前に、既存の構成を削除する必要があります。
 6. 作成したスコープ構成をクリックします。
 7. 構成の**状態**を監視して、**[成功]** が表示されることを確認します。  エラーが示される場合は、構成の右側にある省略記号ボタンをクリックして、**[スコープ構成の編集]** を選択して変更を加えます。

## <a name="solutions-and-agents-that-cant-be-targeted"></a>ターゲット設定できないソリューションとエージェント
ソリューションのターゲット設定で使用できないエージェントとソリューションの条件を次に示します。

- ソリューションのターゲット設定は、エージェントにデプロイされたソリューションにのみ適用されます。
- ソリューションのターゲット設定は、Microsoft 提供のソリューションにのみ適用されます。  [自作の、またはパートナーが作成した](operations-management-suite-solutions-creating.md)ソリューションには適用されません。
- Log Analytics に直接接続するエージェントのみを除外できます。  ソリューションは、スコープ構成に含まれているかどうかに関係なく、接続されている Operations Manager 管理グループのすべてのエージェントに自動的にデプロイされます。

### <a name="exceptions"></a>例外
前述の条件を満たしていても、次のソリューションでは、ソリューションのターゲット設定を使用することはできません。

- エージェントの正常性評価

## <a name="next-steps"></a>次のステップ
- 「[Azure Log Analytics 管理ソリューションをワークスペースに追加する](../log-analytics/log-analytics-add-solutions.md)」で管理ソリューション (環境にインストールできるソリューションなど) について学習する。
- 「[Log Analytics のログ検索におけるコンピューター グループ](../log-analytics/log-analytics-computer-groups.md)」でコンピューター グループの作成について学習する。