---
title: "Operations Management Suite (OMS) のソリューション | Microsoft Docs"
description: "ソリューションは、お客様の OMS ワークスペースに追加できるパッケージの管理シナリオを提供することで、 Operations Management Suite (OMS) の機能を拡張します。  この記事では、お客様およびパートナーによるカスタム ソリューションの作成方法の詳細を説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.lasthandoff: 03/22/2017


---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Operations Management Suite (OMS) での管理ソリューションの操作 (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である OMS の管理ソリューションに関する暫定版ドキュメントです。    
> 
> 

管理ソリューションでは、お客様の環境に追加可能な管理シナリオ パッケージを提供して、Operations Management Suite (OMS) の機能を拡張します。  パートナーとお客様は、[Microsoft 提供のソリューション](../log-analytics/log-analytics-add-solutions.md)とは別に、自身の環境で使用する管理ソリューションを作成したり、作成したソリューションをコミュニティを通じて公開したりすることができます。

## <a name="finding-and-installing-management-solutions"></a>管理ソリューションの検索とインストール
管理ソリューションを検索してインストールする方法は、以下の各セクションで示すように複数あります。

### <a name="azure-marketplace"></a>Azure Marketplace
Microsoft および信頼されたパートナーが提供する管理ソリューションは、Azure Portal の Azure Marketplace でインストールできます。

1. Azure ポータルにログインします。
2. 左側のウィンドウで、**[その他のサービス]** を選択します。
3. **[ソリューション]** までスクロールするか、**[フィルター]** ダイアログに「*ソリューション*」と入力します。
4. **[+ 追加]** ボタンをクリックします。
5. 興味のあるソリューションを検索します。検索は、ブラウジングするか、**[フィルター]** ボタンをクリックするか、または **[Search Everthing]** (全ソリューションを検索) ボックスに入力して行います。
6. Marketplace のアイテムをクリックして詳細情報を確認します。
7. **[作成]** をクリックして、**[ソリューションの追加]** ウィンドウを開きます。
8. ソリューションのパラメーターの値に加えて、[OMS ワークスペースや Automation アカウントaccount](#oms-workspace-and-automation-account) などの必須情報を入力するように求められます。
9. **[作成]** をクリックして、ソリューションをインストールします。

### <a name="oms-portal"></a>OMS ポータル
Microsoft 提供の管理ソリューションは、OMS ポータルのソリューション ギャラリーからインストールすることもできます。

1. OMS ポータルにログインします。
2. **[ソリューション ギャラリー]** タイルをクリックします。
3. OMS ソリューション ギャラリー ページでは、利用可能な各ソリューションの詳細を確認できます。 OMS に追加するソリューションの名前をクリックします。
4. 選択したソリューションのページに、ソリューションに関する詳細情報が表示されます。 **[追加]**をクリックします。
5. 追加したソリューションの新しいタイルが、OMS の [概要] ページに表示されます。OMS サービスによってデータが処理されると、そのタイルが使用できるようになります。

### <a name="azure-quickstart-templates"></a>Azure のクイック スタート テンプレート
コミュニティのメンバーは、Azure クイック スタート テンプレートに管理ソリューションを提出できます。  こうしたテンプレートは、ダウンロードしてインストールすることも、[独自のソリューションを作成する](#creating-a-solution)方法の参考にすることもできます。

1. 「[OMS ワークスペースと Automation アカウント](#oms-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。
2. [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)にアクセスします。  
3. 興味のあるソリューションを検索します。
4. 検索結果のソリューションを選択して、詳細を確認します。
5. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。
6. ソリューションのパラメーターの値に加えて、リソース グループや場所などの必須情報を入力するように求められます。
7. **[購入]** をクリックして、ソリューションをインストールします。

### <a name="deploy-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのデプロイ
コミュニティで入手したソリューションや[独自に作成した](#creating-a-solution)ソリューションは、Resource Manager テンプレートとして実装されるため、標準的な[テンプレートのデプロイ方法](../azure-resource-manager/resource-group-template-deploy-portal.md)のいずれかを使用できます。  ソリューションをインストールする前に、[OMS ワークスペースと Automation アカウント](#oms-workspace-and-automation-account)を作成してリンクする必要があることに注意してください。

## <a name="oms-workspace-and-automation-account"></a>OMS ワークスペースと Automation アカウント
ほとんどの管理ソリューションでは、ビューを格納するために [OMS ワークスペース](../log-analytics/log-analytics-manage-access.md)が、Runbook と関連リソースを格納するために [Automation アカウント](../automation/automation-security-overview.md#automation-account-overview)が必要です。 ワークスペースとアカウントは、次の要件を満たす必要があります。

* ソリューションで使用できる OMS ワークスペースと Automation アカウントはそれぞれ 1 つのみです。  
* ソリューションで使用する OMS ワークスペースと Automation アカウントは、互いにリンクさせる必要があります。 1 つの Automation アカウントにリンクできる OMS ワークスペースは 1 つのみであり、同様に 1 つの OMS ワークスペースにリンクできる Automation アカウントは 1 つのみです。
* OMS ワークスペースと Automation アカウントをリンクさせるには、これらのリソース グループとリージョンが同じである必要があります。  例外として、米国東部リージョンの OMS ワークスペースと米国東部 2 の Automation アカウントはリンク可能です。

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>OMS ワークスペースと Automation アカウント間のリンクの作成
OMS ワークスペースと Automation アカウントを指定する方法は、ソリューションのインストール方法によって異なります。

* OMS ポータルから Microsoft のソリューションをインストールする場合、ソリューションは現在の OMS ワークスペースにインストールされるため、Automation アカウントは求められません。
* Azure Marketplace からソリューションをインストールする場合は、OMS ワークスペースと Automation アカウントが求められますが、これらの間のリンクは自動で作成されます。  
* Azure Marketplace 外部のソリューションについては、ソリューションのインストール前に OMS ワークスペースと Automation アカウントをリンクさせる必要があります。  これを行うには、Azure Marketplace で任意のソリューションを選択してから、OMS ワークスペースと Automation アカウントを選択します。  OMS ワークスペースと Automation アカウントを選択するとすぐにリンクが作成されるため、実際にソリューションをインストールする必要はありません。  リンクが作成されると、すべてのソリューションで選択した OMS ワークスペースと Automation アカウントを使用できるようになります。 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>OMS ワークスペースと Automation アカウント間のリンクの検証
OMS ワークスペースと Automation アカウント間のリンクは、次の方法で確認できます。

1. Azure Portal で Automation アカウントを選択します。
2. **[設定]** ウィンドウの一番下までスクロールします。
3. **[設定]** ウィンドウに **[OMS Resources]**(OMS リソース) というセクションがあれば、このアカウントは OMS ワークスペースに関連付けられています。
4. **[ワークスペース]** を選択して、この Automation アカウントにリンクされている OMS ワークスペースの詳細を確認します。

## <a name="listing-management-solutions"></a>管理ソリューションの一覧表示
Azure サブスクリプションにリンクされているワークスペース内の管理ソリューションを確認するには、次の手順に従います。

1. Azure ポータルにログインします。
2. 左側のウィンドウで、**[その他のサービス]** を選択します。
3. **[ソリューション]** までスクロールするか、**[フィルター]** ダイアログに「*ソリューション*」と入力します。
4. すべてのワークスペースにインストールされているソリューションが表示されます。

表示されるのは、OMS ポータルを使用して現在のワークスペースにインストールされた Microsoft ソリューションのみであることに注意してください。

## <a name="removing-a-management-solution"></a>管理ソリューションの削除
管理ソリューションが削除されると、そのソリューション内のすべてのリソースも削除されます。  

1. [ソリューションの一覧表示](#listing-solutions)に関するセクションの手順に従って、削除するソリューションを Azure Portal で検索します。
2. 削除するソリューションを選択します。
3. **[削除]** ボタンをクリックします。

## <a name="creating-a-management-solution"></a>管理ソリューションの作成
管理ソリューションの作成に関する完全なガイダンスについては、「[Creating solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)」(Operations Management Suite (OMS) でのソリューションの作成) をご覧ください。 

## <a name="next-steps"></a>次のステップ
* Resource Manager テンプレートの様々なサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates) で検索できます。
* 独自の[管理ソリューション](operations-management-suite-solutions-creating.md)を作成する。


