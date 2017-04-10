---
title: "プライベート テンプレートを使ってみる | Microsoft Docs"
description: "Azure ポータル、Azure CLI、または PowerShell を使ってプライベート テンプレートを追加、管理、共有します。"
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565


---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Azure ポータルでプライベート テンプレートを使ってみる
[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) テンプレートは、デプロイの定義に使う宣言型のテンプレートです。 ソリューションでデプロイするリソースを定義し、さまざまな環境に応じた値を入力できるパラメーターと変数を指定できます。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。

この新しい**テンプレート**機能は、[Azure Marketplace](https://azure.microsoft.com/marketplace/) の拡張機能として、[Azure Portal](https://portal.azure.com) 内で **Microsoft.Gallery** リソース プロバイダーと共に使用できます。この機能を使用すると、ユーザーは個人用ライブラリからプライベート テンプレートを作成、管理、デプロイすることができます。

このドキュメントでは、Azure ポータルを使ってプライベート **テンプレート** を追加、管理、共有する方法について説明します。

## <a name="guidance"></a>ガイダンス
次の推奨事項は、ソリューションを操作する際に **テンプレート** を最大限に活用するのに役立ちます。

* **テンプレート** は、Resource Manager テンプレートと追加のメタデータを保持する、カプセル化したリソースです。 その動作は、Marketplace のアイテムとよく似ています。 主な違いは、これがパブリックの Marketplace アイテムではなく、プライベートなアイテムであるという点です。
* **テンプレート** ライブラリは、デプロイをカスタマイズする必要があるユーザーに役立ちます。
* **テンプレート** は、Azure 内に単純なリポジトリを必要とするユーザーに役立ちます。
* まずは、既存の Resource Manager テンプレートから使用します。 テンプレートは、[GitHub](https://github.com/Azure/azure-quickstart-templates) で探すか、既存のリソース グループから[エクスポート](../azure-resource-manager/resource-manager-export-template.md)してください。
* **テンプレート** は、そのテンプレートを発行したユーザーに関連付けられます。 発行者の名前は、そのテンプレートへの読み取りアクセス権を持つすべてのユーザーに表示されます。
* **テンプレート** は Resource Manager のリソースであるため、発行後に名前を変更することはできません。

## <a name="add-a-template-resource"></a>テンプレート リソースの追加
Azure ポータルで **テンプレート** リソースを作成する方法は 2 とおりあります。

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>方法 1: 実行中のリソース グループから新しいテンプレート リソースを作成する
1. Azure ポータルで既存のリソース グループに移動します。 **[設定]** で **[テンプレートのエクスポート]** を選択します。
2. Resource Manager テンプレートをエクスポートしたら、**[テンプレートの保存]** ボタンをクリックして、そのテンプレートを **[テンプレート]** リポジトリに保存します。 [テンプレートのエクスポート] の詳細については、 [こちら](../azure-resource-manager/resource-manager-export-template.md)を参照してください。
   <br /><br />
   ![リソース グループのエクスポート](media/rg-export-portal1.PNG)  <br />
3. **[Save to Template]** (テンプレートに保存) ボタンをクリックします。
   <br /><br />
4. 次の情報を入力します。
   
   * [名前] – テンプレート オブジェクトの名前 (注: これは、Azure Resource Manager に基づく名前です。 名前付けの制限がすべて適用されており、作成後に変更することはできません)。
   * [説明] – テンプレートの簡単な概要。
     
     ![Save Template](media/save-template-portal1.PNG)  <br />
5. **[保存]**をクリックします。
   
   > [!NOTE]
   > エクスポートされた Resource Manager テンプレートでエラーが発生している場合は、[テンプレートのエクスポート] ブレードに通知が表示されますが、その Resource Manager テンプレートを引き続き [テンプレート] に保存できます。 確実に Resource Manager テンプレートの問題を確認して修正してから、エクスポートされた Resource Manager テンプレートを再デプロイしてください。
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>方法 2: [参照] から新しいテンプレート リソースを追加する
新しい**テンプレート**を一から追加することもできます。これには、**[参照]、[テンプレート]** の順に選択して [追加] ボタンをクリックします。 ここでは、名前、説明、Resource Manager テンプレート JSON を入力する必要があります。

![Add Template](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery は、テナント ベースの Azure リソース プロバイダーです。 テンプレート リソースは、それを作成したユーザーに関連付けられます。 特定のサブスクリプションには関連付けられません。 サブスクリプションを選ぶ必要があるのは、テンプレートをデプロイするときだけです。
> 
> 

## <a name="view-template-resources"></a>テンプレート リソースの表示
**[参照]、[テンプレート]** の順に選択すると、使用可能な**テンプレート**すべてが表示されます。 ここには、自身が作成した **テンプレート** のほか、さまざまなレベルのアクセス許可で共有しているテンプレートが含まれます。 詳細については、下の [アクセス制御](#access-control-for-a-tenant-resource-provider) に関するセクションを参照してください。

![View Template](media/view-template-portal1.PNG)  <br />

**テンプレート** の詳細は、一覧にある項目をクリックすると確認できます。

![View Template](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>テンプレート リソースの編集
**テンプレート** の編集フローを開始するには、参照リストで項目を右クリックするか、[編集] ボタンをクリックします。

![Edit Template](media/edit-template-portal1a.PNG)  <br />

説明または Resource Manager テンプレートのテキストを編集できます。 名前は、Resource Manager のリソース名であるため編集できません。 Resource Manager テンプレート JSON を編集すると、それが有効な JSON であるかどうかを確認するための検証が行われます。 **[OK]**、**[保存]** の順にクリックして、更新したテンプレートを保存します。

![Edit Template](media/edit-template-portal2a.PNG)  <br />

**テンプレート** を保存すると、確認通知が表示されます。

![Edit Template](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>テンプレート リソースのデプロイ
**テンプレート**に対する**読み取り**アクセス許可があれば、そのテンプレートをデプロイできます。 デプロイのフローでは、まず、標準的な Azure テンプレートのデプロイ ブレードを開きます。 Resource Manager テンプレートのパラメーターの値を入力して、デプロイを続行します。

![Deploy Template](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>テンプレート リソースの共有
**テンプレート** リソースは、仲間と共有できます。 共有の動作は、 [Azure 上のリソースへのロールの割り当て](../active-directory/role-based-access-control-configure.md)に似ています。 **テンプレート** の所有者は、他のユーザーにアクセス許可を付与し、テンプレート リソースを操作できるようにします。 **テンプレート** の共有相手であるユーザーまたはユーザーのグループは、Resource Manager テンプレートとそのギャラリーのプロパティを参照できます。

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery リソースのアクセス制御
| ロール | アクセス許可 |
| --- | --- |
| 所有者 |テンプレート リソースに対するフル コントロール (共有を含む) を許可します。 |
| 閲覧者 |テンプレート リソースの読み取りと実行 (デプロイ) を許可します。 |
| 共同作成者 |テンプレート リソースの編集と削除を許可します。 他のユーザーとテンプレートを共有することはできません。 |

**[共有]** は、参照するアイテムを右クリックして選択するか、特定のアイテムを表示しているブレードで選択します。 これにより、共有が開始されます。

![Share Template](media/share-template-portal1a.png)  <br />

 これで、特定の **テンプレート**へのアクセスを提供するロールやユーザーまたはグループを選択できるようになりました。 利用可能なロールは、所有者、閲覧者、共同作成者です。 詳細については、前述の [アクセス制御](#access-control-for-a-tenant-resource-provider) に関するセクションを参照してください。

![Share Template](media/share-template-portal2b.png)  <br />

![Share Template](media/share-template-portal3b.png)  <br />

**[選択]**、**[OK]** の順にクリックします。 これで、リソースに追加したユーザーまたはグループが表示されます。

![Share Template](media/share-template-portal4b.png)  <br />

> [!NOTE]
> テンプレートは、同じ Azure Active Directory テナント内のユーザーとグループのみと共有できます。 テンプレートの共有相手の電子メール アドレスがテナントに登録されていない場合は、ゲストとしてテナントに参加することをユーザーに求める招待が送信されます。
> 
> 

## <a name="next-steps"></a>次のステップ
* Resource Manager テンプレートの作成の詳細については、 [テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* Resource Manager テンプレートで使用できる関数を理解するには、 [テンプレートの関数](../azure-resource-manager/resource-group-template-functions.md)
* テンプレートのデザインのガイダンスについては、 [Azure リソース マネージャー テンプレートを設計するためのベスト プラクティス](../azure-resource-manager/best-practices-resource-manager-design-templates.md)




<!--HONumber=Feb17_HO3-->


