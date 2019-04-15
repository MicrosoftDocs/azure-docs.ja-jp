---
title: 国に応じて Azure CDN コンテンツへのアクセスを制限 | Microsoft Docs
description: geo フィルタリング機能を使用して Azure CDN コンテンツへのアクセスを国に応じて制限する方法について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: f6efec64b4e6659b822b76e0fd7f9cc71a164094
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917755"
---
# <a name="restrict-azure-cdn-content-by-country"></a>国に応じて Azure CDN コンテンツへのアクセスを制限

## <a name="overview"></a>概要
ユーザーがコンテンツを要求した場合、既定では、要求を行っているユーザーの場所に関係なく、コンテンツが提供されます。 ただし、場合によっては、国によってコンテンツへのアクセスを制限することができます。 *geo フィルタ リング*機能を使用して、CDN エンドポイントの特定のパスに適用される特定のルールを作成して、選択された国のコンテンツへのアクセスを許可するかブロックできます。

> [!IMPORTANT]
> **Azure CDN Standard from Microsoft** プロファイルでは、パス ベースの geo フィルタリングはサポートされません。
> 

## <a name="standard-profiles"></a>Standard プロファイル
このセクションの手順は、**Azure CDN Standard from Akamai** プロファイルと **Azure CDN Standard from Verizon** プロファイルにのみ適用されます。 

**Azure CDN Premium from Verizon** プロファイルの場合、geo フィルタリングをアクティブにするには、**管理**ポータルを使用する必要があります。 詳細については、「[Azure CDN Premium from Verizon プロファイル](#azure-cdn-premium-from-verizon-profiles)」を参照してください。

### <a name="define-the-directory-path"></a>ディレクトリ パスを定義する
geo フィルタ リング機能にアクセスするには、ポータル内で CDN エンドポイントを選択し、左側のメニューの [設定] の下の **[geo フィルタ リング]** を選択します。 

![geo フィルタリングの標準](./media/cdn-filtering/cdn-geo-filtering-standard.png)

**[パス]** ボックスに、ユーザーのアクセスを許可または拒否する場所の相対パスを指定します。 

geo フィルタリングは、スラッシュ (/) を指定してすべてのファイルに適用することも、ディレクトリ パス を指定して特定のフォルダーに適用することもできます (例: */pictures/*)。 geo フィルタリングは、1 つのファイルに適用することもできます (例: */pictures/city.png*)。 複合ルールは許可されません。１ つのルールを入力すると、次のルールを入力するための空の行が表示されます。

たとえば、次のディレクトリ パス フィルターはすべて有効です。   
*/*                                 
*/Photos/*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>アクションの種類を定義する

**[アクション]** の一覧から、**[許可]** または **[ブロック]** を選択します。 

- **許可**: 指定した国のユーザーだけが、その再帰パスから要求された資産へのアクセスを許可されます。

- **ブロック**: 指定した国のユーザーは、その再帰パスから要求された資産へのアクセスを拒否されます。 その場所に対して他の国フィルター オプションが構成されていない場合、他のすべてのユーザーはアクセスを許可されます。

たとえば、*/Photos/Strasbourg/* パスをブロックする geo フィルタリング ルールは、次のファイルへのアクセスをブロックします。     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>国を定義する
**[国番号]** の一覧から、パスを許可またはブロックする国を選択します。 

国を選択したら、**[保存]** を選択して、新しい geo フィルタリング ルールをアクティブにします。 

![geo フィルタリング ルール](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>リソースのクリーンアップ
ルールを削除するには、**[geo フィルタリング]** ページでルールを選択し、**[削除]** を選択します。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium from Verizon プロファイル
**Azure CDN Premium from Verizon** プロファイルには、geo フィルタリング ルールを作成するためのユーザー インターフェイスが別にあります。

1. Azure CDN プロファイルの上部のメニューから、**[管理]** を選択します。

2. Verizon ポータルで、**[HTTP ラージ]** を選択し、**[国のフィルタ リング]** を選択します。

    ![geo フィルタリングの標準](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. **[国フィルターの追加]** を選択します。

    **[手順 1:]** ページが表示されます。

4. ディレクトリ パスを入力し、**[ブロック]** または **[追加]** を選択し、**[次へ]** を選択します。

    **[手順 2:]** ページが表示されます。 

5. 一覧から 1 つまたは複数の国を選択し、**[終了]** を選択してルールをアクティブにします。 
    
    新しい規則が、**[国のフィルタ リング]** ページの表に表示されます。

    ![geo フィルタリング ルール](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>リソースのクリーンアップ
国のフィルタリング ルール の表で、ルールの横にある削除アイコンを選択してルールを削除するか、編集アイコンを選択してルールを変更します。

## <a name="considerations"></a>考慮事項
* geo フィルタリング構成の変更は、すぐには有効になりません。
   * **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
   * **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
   * **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。 
 
* この機能では、ワイルドカード文字 (例: *) はサポートされていません。

* 相対パスに関連付けられている geo フィルタリング構成は、そのパスに再帰的に適用されます。

* 同じ相対パスに適用できるのは、1 つのルールのみです。 つまり、同じ相対パスを指す複数の国フィルターを作成することはできません。 ただし、国フィルターの再帰的な性質のため、フォルダーには複数の国フィルターを含めることができます。 つまり、以前に構成されているフォルダーのサブフォルダーに、別の国フィルターを割り当てることができます。

* geo フィルタリング機能では、国番号を使用して、保護されたディレクトリに対する要求が許可またはブロックされる国を定義します。 Akamai プロファイルと Verizon プロファイルは、ほぼ同一の国番号をサポートしますが、いくつか違いがあります。 詳細については、「[Azure CDN Country Codes](/previous-versions/azure/mt761717(v=azure.100))」(Azure CDN 国コード) を参照してください。 

