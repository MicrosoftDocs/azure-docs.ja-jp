---
title: 国/地域別の Azure CDN コンテンツの制限
description: Geo フィルタリング機能を使用して、国/地域別に Azure CDN コンテンツへのアクセスを制限する方法について説明します。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539507"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>国/地域別の Azure CDN コンテンツの制限

## <a name="overview"></a>概要
ユーザーからあなたのコンテンツに対して要求があった場合、そのコンテンツはすべての場所のユーザーに提供されます。 国/地域で自分のコンテンツへのアクセスを制限したい場合があります。 

*geo フィルタリング* 機能を使用すると、お使いの CDN エンドポイント上の特定のパスに特定のルールを作成できます。 選択した国または地域に対し、コンテンツを許可またはブロックするルールを設定できます。

> [!IMPORTANT]
> **Azure CDN Standard from Microsoft** プロファイルでは、パス ベースの geo フィルタリングはサポートされません。
> 

## <a name="standard-profiles"></a>Standard プロファイル

これらの手順は、**Azure CDN Standard from Akamai** と **Azure CDN Standard from Verizon** プロファイル用です。

**Azure CDN Premium from Verizon** プロファイルの場合、geo フィルタリングをアクティブにするには、**管理** ポータルを使用する必要があります。 詳細については、「[Azure CDN Premium from Verizon プロファイル](#azure-cdn-premium-from-verizon-profiles)」を参照してください。

### <a name="define-the-directory-path"></a>ディレクトリ パスを定義する
geo フィルタ リング機能にアクセスするには、ポータル内で CDN エンドポイントを選択し、左側のメニューの [設定] の下の **[geo フィルタ リング]** を選択します。 

![エンドポイントのメニューから [geo フィルタ リング] が選択されていることを示すスクリーンショット。](./media/cdn-filtering/cdn-geo-filtering-standard.png)

**[パス]** ボックスに、ユーザーのアクセスを許可または拒否する場所の相対パスを指定します。 

geo フィルタリングは、スラッシュ (/) を指定してすべてのファイルに適用することも、ディレクトリ パス を指定して特定のフォルダーに適用することもできます (例: */pictures/* )。 geo フィルタリングは、1 つのファイルに適用することもできます (例: */pictures/city.png*)。 ルールは複数許可されています。 1 つのルールを入力すると、次のルールを入力する空の行が表示されます。

たとえば、次のディレクトリ パス フィルターはすべて有効です。   
*/*                                 
*/Photos/*      
*/Photos/Strasbourg/*      
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>アクションの種類を定義する

**[アクション]** の一覧から、 **[許可]** または **[ブロック]** を選択します。 

- **許可**: 指定された国/地域からのユーザーのみが、再帰パスから要求された資産へのアクセスを許可されます。

- **ブロック**: 指定された国/地域からのユーザーは、再帰パスから要求された資産へのアクセスを拒否されます。 その場所への他の国/地域のフィルター オプションが構成されていない場合、その他のすべてのユーザー アクセスが許可されます。

たとえば、 */Photos/Strasbourg/* パスをブロックする geo フィルタリング ルールは、次のファイルへのアクセスをブロックします。     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>国/地域の定義

**国コード** 一覧で、パスに対してブロックまたは許可する国/地域を選択します。 

国/地域を選択したら、 **[保存]** を選択して、新しい Geo フィルタリング ルールを有効にします。 

![国または地域をブロックまたは許可するために使用する国コードを示しているスクリーンショット。](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>リソースをクリーンアップする

ルールを削除するには、 **[geo フィルタリング]** ページでルールを選択し、 **[削除]** を選択します。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium from Verizon プロファイル

**Azure CDN Premium from Verizon** プロファイルでは、geo フィルタリング ルールを作成するユーザー インターフェイスが別にあります。

1. Azure CDN プロファイルの上部のメニューから、 **[管理]** を選択します。

2. Verizon ポータルで、 **[HTTP ラージ]** を選択し、 **[国のフィルタ リング]** を選択します。

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Azure CDN で国のフィルタリングを選択する方法を示すスクリーンショット" border="true":::
  
3. **[国フィルターの追加]** を選択します。

4. **[手順 1]** で、ディレクトリ パスを入力します。 **[ブロック]** または **[追加]** を選択し、次いで **[次へ]** を選択します。

    > [!IMPORTANT]
    > パスにはエンドポイント名が必要です。  例: **/myendpoint8675/myfolder**。  **myendpoint8675** を自分のエンドポイント名に置き換えます。
    > 
    
5. **[手順 2]** で、一覧から 1 つ以上の国または地域を選択します。 **[完了]** を選択して、ルールをアクティブにします。 
    
    新しい規則が、 **[国のフィルタ リング]** ページの表に表示されます。
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="国のフィルタリングでルールがどこに表示されるかを示すスクリーンショット。" border="true":::
 
### <a name="clean-up-resources"></a>リソースをクリーンアップする
国/地域フィルタリング ルール テーブルでは、ルールの横にある削除アイコンを選択して削除するか、または編集アイコンを選択して変更します。

## <a name="considerations"></a>考慮事項
* 自分の geo フィルタリング構成の変更は、すぐには有効になりません。
   * **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
   * **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
   * **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。 
 
* この機能では、ワイルドカード文字 (例: *) はサポートされていません。

* 相対パスに関連付けられている geo フィルタリング構成は、そのパスに再帰的に適用されます。

* 同じ相対パスに適用できるのは、1 つのルールのみです。 つまり、同じ相対パスを参照する国/地域フィルターを複数作成することはできません。 ただし、国/地域フィルターは再帰的であるため、1 つのフォルダーが複数の国/地域フィルターを持つことができます。 つまり、以前に構成したフォルダーのサブフォルダーには、さまざまな国/地域フィルターを代入できます。

* Geo フィルタリング機能では、国番号を使用して、セキュリティで保護されたディレクトリに対する要求を許可またはブロックする元の国/地域を定義します。 Akamai プロファイルと Verizon プロファイルは、ほぼ同一の国番号をサポートしますが、いくつか違いがあります。 詳細については、「[Azure CDN Country Codes](/previous-versions/azure/mt761717(v=azure.100))」(Azure CDN 国コード) を参照してください。 

