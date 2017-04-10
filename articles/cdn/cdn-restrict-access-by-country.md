---
title: "国に応じて Azure CDN コンテンツへのアクセスを制限 | Microsoft Docs"
description: "地理のフィルタリング機能を使用して Azure CDN コンテンツへのアクセスを制限する方法について説明します。"
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b


---
# <a name="restrict-azure-cdn-content-by-country"></a>国に応じて Azure CDN コンテンツへのアクセスを制限

## <a name="overview"></a>概要
ユーザーがコンテンツを要求した場合、既定では、ユーザーが要求を行った場所に関係なく、コンテンツは提供されます。 場合によっては、国によってコンテンツへのアクセスを制限することがあります。 このトピックでは、**地理のフィルタリング**機能を使用して、国別にアクセスを許可またはブロックするようにサービスを構成する方法について説明します。

> [!IMPORTANT]
> Verizon 製品と Akamai 製品では同じ地理フィルタリング機能が提供されますが、サポートされる国コードが少し異なります。 この違いを確認するには、手順 3 のリンクをご確認ください。


このような制限を構成する際に考慮する事項については、トピックの最後にある「 [考慮事項](cdn-restrict-access-by-country.md#considerations) 」セクションを参照してください。  

![国のフィルタリング](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>手順 1: ディレクトリのパスを定義する
ポータル内のエンドポイントを選択し、左側のナビゲーションで [Geo-Filtering (地理フィルタリング)] タブを探して、この機能を確認します。

国フィルターを構成するときは、ユーザーのアクセスを許可または拒否する場所への相対パスを指定する必要があります。 "/" を指定してすべてのファイルに地理フィルターを適用することも、ディレクトリ パス "/pictures/" を指定して選択したフォルダーに適用することもできます。 また、"/pictures/city.png" のようにファイルを指定して後のスラッシュを除外して、1 つのファイルに地理フィルターを適用することもできます。

ディレクトリ パスのフィルターの例:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>手順 2: ブロックまたは許可アクションを定義する
**ブロック** : 指定した国のユーザーは、その再帰パスから要求された資産へのアクセスを拒否されます。 その場所に対して他の国フィルター オプションが構成されていない場合、他のすべてのユーザーはアクセスを許可されます。

**許可** : 指定した国のユーザーだけが、その再帰パスから要求された資産へのアクセスを許可されます。

## <a name="step-3-define-the-countries"></a>手順 3: 国を定義する
パスに対してブロックまたは許可する国を選択します。 

たとえば、/Photos/Strasbourg/ をブロックするルールは、次のようなファイルをフィルターします。

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>国コード
**地理フィルタリング**機能では、国コードを使用して、保護されたディレクトリに対する要求が許可またはブロックされる国を定義します。 国コードは、「[Azure CDN Country Codes (Azure CDN の国コード)](https://msdn.microsoft.com/library/mt761717.aspx)」に記載されています。 

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>考慮事項
* 国フィルタリング構成の変更が有効になるまでには、Verizon の場合は最大で 90 分、Akamai の場合は数分かかることがあります。
* この機能では、ワイルドカード文字 (例: *) はサポートされていません。
* 相対パスに関連付けられている地理フィルタリング構成は、そのパスに再帰的に適用されます。
* 同じ相対パスに適用できるルールは&1; つだけです (同じ相対パスを参照する複数の国フィルターを作成することはできません)。 ただし、フォルダーには複数の国フィルターを適用できます。 これは、国フィルターの再帰的な性質のためです。 つまり、以前に構成されているフォルダーのサブフォルダーに、別の国フィルターを割り当てることができます。




<!--HONumber=Jan17_HO4-->


