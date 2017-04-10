---
title: "Azure Portal を使用した Azure Search インデックスの作成 | Microsoft Docs"
description: "Azure ポータルを利用してインデックスを作成する"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: cc439b5da8753b41f8d65781c567b4808f385078


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Azure ポータルを利用して Azure Search インデックスを作成する
> [!div class="op_single_selector"]
> * [概要](search-what-is-an-index.md)
> * [ポータル](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST ()](search-create-index-rest-api.md)
> 
> 

この記事では、Azure ポータルを使用して Azure Search の [インデックス](search-what-is-an-index.md) を作成するプロセスについて説明します。

このガイドに従ってインデックスを作成する前に、既に [Azure Search サービスを作成済み](search-create-service-portal.md)です。

## <a name="go-to-your-azure-search-blade"></a>Azure Search ブレードに移動する
1. [Azure ポータル](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)の左側のメニューの [すべてのリソース] をクリックします。
2. Azure Search サービスを選択します。

## <a name="add-and-name-your-index"></a>インデックスを追加し、名前を付ける
1. [インデックスの追加] ボタンをクリックします。
2. Azure Search インデックスに名前を付けます。 このガイドではホテルを検索するインデックスを作成するので、"hotels" という名前をインデックスに付けました。
   * インデックス名は文字で始め、小文字、数字、ダッシュ ("-") のみで作成します。
   * サービス名と同様に、選択したインデックス名は、Azure Search API の HTTP 要求を送信するエンドポイント URL の一部にもなります。
3. "Fields" エントリをクリックし、新しいブレードを開く

![](./media/search-create-index-portal/add-index.png)

## <a name="create-and-define-the-fields-of-your-index"></a>インデックスのフィールドを作成し、定義する
1. "Fields" エントリを選択すると、インデックスの定義を入力するフォームを含む新しいブレードが開きます。
2. フォームを利用し、インデックスにフィールドを追加します。
   
   * Edm.String 型の *キー* フィールドはすべての Azure Search インデックスで必須です。 既定では、このキー フィールドはフィールド名 "id" で作成されます。 このガイドでは、それを "hotelId" に変更しています。
   * インデックス スキーマの特定のプロパティは&1; 回だけ設定可能であり、将来更新することはできません。 そのような理由から、初回の構成後は、フィールドの種類の変更といったインデックスの再作成を必要とするスキーマ更新は現在のところ不可能です。
   * 各フィールドのプロパティの値は、アプリケーションでどのように使用されるかに応じて、慎重に選択されています。 各フィールドには [適切なプロパティ](https://msdn.microsoft.com/library/azure/dn798941.aspx)を割り当てる必要があるため、インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意してください。 これらのプロパティでは、どのフィールドにどの検索機能 (フィルター、ファセット、全文検索の並べ替えなど) が適用されるかを制御します。 そのため、"Searchable" プロパティを設定してそのフィールドの全文検索を有効にします。
     * ブレードの上部にある [アナライザー] タブをクリックし、フィールド別に [言語アナライザー](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) を設定することもできます。 下の図では、インデックスのフィールドにフランス語テキスト用のフランス語アナライザーを選択していることがわかります。
3. "Fields" ブレードで **[OK]** をクリックし、フィールド定義を確定します。
4. "Add index" ブレードで **[OK]** をクリックし、定義したインデックスを保存し、作成します。

下のスクリーンショットでは、"hotels" インデックスのフィールドに名前を付け、定義していることがわかります。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>次のステップ
Azure Search インデックスを作成すると、データの検索を開始できるように [インデックスにコンテンツをアップロードする](search-what-is-data-import.md) 準備が完了します。




<!--HONumber=Jan17_HO2-->


