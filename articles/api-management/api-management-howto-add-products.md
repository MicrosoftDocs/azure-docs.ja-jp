---
title: "Azure API Management で成果物を作成して発行する方法"
description: "Azure API Management で成果物を作成して発行する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 1568bbba999018e26493654e0286a6b8d93f3ae7


---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Azure API Management で成果物を作成して発行する方法
Azure API Management の成果物には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。 成果物が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。 このトピックでは、成果物を作成し、API を追加し、発行して開発者が利用できるようにする方法について説明します。

## <a name="create-product"> </a>成果物の作成
操作を API に追加して構成するには、パブリッシャー ポータルを使用します。 発行者ポータルにアクセスするには、API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

> まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の概要][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。
> 
> 

左側のメニューの **[成果物]** をクリックして **[成果物]** ページを表示し、**[成果物の追加]** をクリックします。

![[成果物]][api-management-products]

![新しい成果物][api-management-add-new-product]

成果物のわかりやすい名前を **[名前]** フィールドに入力し、成果物の説明を **[説明]** フィールドに入力します。

API Management の成果物は、**[開く]** ことや **[保護]** することが可能です。 保護された成果物を使用するには、事前にサブスクライブする必要があります。一方、オープンな成果物は、サブスクライブせずに使用できます。 **[サブスクリプションが必要]** チェックボックスをオンにし、サブスクリプションが必要な保護された成果物を作成します。 これは、既定の設定です。

この成果物に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、 **[サブスクリプションの承認を必須とする]** チェック ボックスをオンにします。 チェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。 サブスクリプションの詳細については、「[成果物のサブスクライバーの表示][View subscribers to a product]」をご覧ください。

開発者のアカウントで複数回成果物にサブスクライブできるようにするには、 **[複数のサブスクリプションを許可する]** チェック ボックスをオンにします。 このボックスがオンになっていない場合、各開発者アカウントは、成果物に 1 回だけサブスクライブできます。

![無制限の複数サブスクリプション][api-management-unlimited-multiple-subscriptions]

同時サブスクリプションの数を制限するには、 **[同時サブスクリプション数を制限する]** チェック ボックスをオンにし、サブスクリプションの上限を入力します。 次に示す例では、同時サブスクリプションが開発者アカウントにつき 4 までに制限されています。

![4 つの複数サブスクリプション][api-management-four-multiple-subscriptions]

成果物のすべての新しいオプションを構成したら **[保存]** をクリックし、新しい成果物を作成します。

![[成果物]][api-management-products-page]

> 新しい成果物は既定で発行されず、**Administrators** グループのみに表示されます。
> 
> 

成果物を構成するには、 **[成果物]** タブで成果物の名前をクリックします。

## <a name="add-apis"> </a>成果物への API の追加
**[成果物]** ページには、**[概要]**、**[設定]**、**[可視性]**、**[サブスクライバー]** の 4 つの構成用リンクが用意されています。 **[概要]** タブでは、API を追加したり、成果物の発行や発行の取り消しをしたりできます。

![[概要]][api-management-new-product-summary]

成果物を発行するには、事前に 1 つまたは複数の API を追加する必要があります。 そのためには、 **[成果物への API の追加]**をクリックします。

![API の追加][api-management-add-apis-to-product]

目的の API を選択し、 **[保存]**をクリックします。

## <a name="add-description"> </a>成果物への説明情報の追加
**[設定]** タブでは、その目的、アクセスできる API、その他の有益な情報など、成果物に関する詳しい情報を入力できます。 このタブでは、API を呼び出す開発者を対象に、プレーンテキストまたは HTML マークアップ形式で情報を入力できます。

![成果物の設定][api-management-product-settings]

**[サブスクリプションが必要]** をオンにすることにより、サブスクリプションを使用する必要がある保護成果物を作成するか、または、このチェックボックスをオフにすることにより、サブスクリプションなしで呼び出すことができるオープンな成果物を作成します。

すべての成果物へのサブスクリプション要求を手動で承認する場合は、 **[サブスクリプションの承認を必須とする]** をオンにします。 既定では、成果物のサブスクリプションはすべて自動的に許可されます。

開発者のアカウントで複数回成果物にサブスクライブできるようにするには、 **[複数のサブスクリプションを許可する]** チェック ボックスをオンにし、必要に応じて上限を指定します。 このボックスがオンになっていない場合、各開発者アカウントは、成果物に 1 回だけサブスクライブできます。

オプションで、 **[使用条件]** に、サブスクライバーが成果物を使用するにあたって同意する必要がある成果物の使用条件を入力します。

## <a name="publish-product"> </a>成果物の発行
成果物に含まれる API を呼び出すには、あらかじめ成果物を発行しておく必要があります。 成果物の **[概要]** タブで、**[発行]** をクリックし、**[はい。発行します]** をクリックして確認します。 以前に発行した成果物をプライベートに設定するには、 **[発行の取り消し]**をクリックします。

![成果物の発行][api-management-publish-product]

## <a name="make-visible"> </a>開発者への成果物の公開
**[可視性]** タブでは、開発者ポータルで成果物を表示してサブスクライブすることができるロールを選択できます。

![成果物の可視性][api-management-product-visiblity]

グループ内の開発者に対して成果物の可視性を有効または無効にするには、グループの横のチェック ボックスをオンまたはオフにし、 **[保存]**をクリックします。

> 詳細については、「[Azure API Management でグループを作成および使用して開発者アカウントを管理する方法][How to create and use groups to manage developer accounts in Azure API Management]」をご覧ください。
> 
> 

## <a name="view-subscribers"> </a>成果物のサブスクライバーの表示
**[サブスクライバー]** タブには、成果物をサブスクライブしている開発者の一覧が表示されます。 開発者の名前をクリックすると、それぞれの開発者の詳細および設定が表示されます。 この例では、成果物をサブスクライブしている開発者は 1 人もいません。

![開発者][api-management-developer-list]

## <a name="next-steps"> </a>次のステップ
目的の API を追加して成果物を発行すると、開発者が成果物をサブスクライブして API を呼び出せるようになります。 これらの操作とより高度な成果物の構成を説明するチュートリアルについては、「[Azure API Management で成果物を作成して詳細設定を行う方法][How create and configure advanced product settings in Azure API Management]」をご覧ください。

製品の操作の詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 



<!--HONumber=Dec16_HO3-->


