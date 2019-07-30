---
title: Azure CDN エンドポイントの消去 | Microsoft Docs
description: Azure CDN エンドポイントからキャッシュされたすべてのコンテンツを消去する方法について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: ff877810cb32d22cffd2af79880b6223c41d7d73
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593523"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Azure CDN エンドポイントの消去
## <a name="overview"></a>概要
Azure CDN エッジ ノードは、アセットの Time-to-Live (TTL) が期限切れになるまで、そのアセットをキャッシュします。  アセットの TTL の期限が切れた後に、クライアントがエッジ ノードからアセットを要求すると、エッジ ノードはアセットの最新コピーを取得し、クライアント要求に対応して、更新されたキャッシュを格納します。

ユーザーが常にアセットの最新コピーを取得するのを確実にするベスト プラクティスは、更新ごとにアセットにバージョンを付け、新しい URL として発行することです。  CDN では、次のクライアント要求のための新しいアセットを直ちに取得します。  必要に応じて、すべてのエッジ ノードのキャッシュされたコンテンツを消去し、すべてのエッジ ノードが新しい更新されたアセットを取得するように強制することもできます。  たとえば、Web アプリケーションの更新に対応する場合や、正しくない情報を含むアセットをすばやく更新する場合などです。

> [!TIP]
> 消去処理では、CDN エッジ サーバーにキャッシュされているコンテンツのみが消去される点に注意してください。  ダウンストリームのキャッシュ (プロキシ サーバーやローカル ブラウザーのキャッシュなど) がある場合、キャッシュされたファイルのコピーが残る可能性があります。  ファイルの有効期間を設定するときに、この点に気を付けることが重要です。  ダウンストリーム クライアントが最新バージョンのファイルを要求するように強制するには、更新するたびに一意の名前を付けるか、 [クエリ文字列のキャッシュ](cdn-query-string.md)を利用します。  
> 
> 

このチュートリアルでは、エンドポイントのすべてのエッジ ノードからアセットを消去する方法について説明します。

## <a name="walkthrough"></a>チュートリアル
1. [Azure Portal](https://portal.azure.com) で、消去するエンドポイントを含む CDN プロファイルを開きます。
2. CDN プロファイル ブレードで、[消去] ボタンをクリックします。
   
    ![CDN プロファイル ブレード](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    [消去] ブレードが開きます。
   
    ![CDN の [消去] ブレード](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. [消去] ブレードで、[URL] ドロップダウンから消去するサービス アドレスを選択します。
   
    ![[消去] フォーム](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > [消去] ブレードを開くには、CDN エンドポイント ブレードの **[消去]** ボタンをクリックします。  この例では、そのエンドポイントのサービス アドレスが既定値として **[URL]** フィールドに表示されるようになります。
   > 
   > 
4. エッジ ノードから消去するアセットを選択します。  すべてのアセットを消去する場合は、 **[すべて消去]** チェックボックスをクリックします。  それ以外の場合は、消去する各アセットのパスを **[パス]** テキストボックスに入力します。 パスには次の形式がサポートされています。
    1. **単一 URL の消去**: 完全な URL (ファイル拡張子の有無を問わず) を指定することによって、個別のアセットを消去します (例: `/pictures/strasbourg.png`、`/pictures/strasbourg`)。
    2. **ワイルドカードによる消去**: アスタリスク (\*) をワイルドカードとして使用できます。 パスに `/*` を付けてエンドポイント下のすべてのフォルダー、サブフォルダー、およびファイルを消去するか、または `/*` が付いたフォルダー (たとえば `/pictures/*`) を指定して特定のフォルダー下のすべてのサブフォルダーおよびファイルを消去します。  ワイルドカードによる消去は、現在 Azure CDN from Akamai ではサポートされていないので注意してください。 
    3. **ルート ドメインの消去**: パスに "/" を付けてエンドポイントのルートを削除します。
   
   > [!TIP]
   > 消去にはパスの指定が必要であり、パスは次の[正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に準拠する相対 URL にする必要があります。 **すべての消去**および**ワイルドカードによる消去**は、現在 **Azure CDN from Akamai** ではサポートされていないので注意してください。
   > > 単一 URL の消去 `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > クエリ文字列 `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > ワイルドカードによる消去 `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` 
   > 
   > テキストを入力すると、 **[パス]** テキストボックスが追加され、複数のアセットの一覧を作成できます。  一覧からアセットを削除するには、省略記号 (...) ボタンをクリックします。
   > 
5. **[消去]** ボタンをクリックします。
   
    ![[消去] ボタン](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> 消去要求の処理にかかる時間は、**Azure CDN from Microsoft** と **Azure CDN from Verizon** (Standard と Premium) で約 2 分、**Azure CDN from Akamai** で約 10 秒です。  Azure CDN には、プロファイル レベルで常に同時の消去要求が 50 件という上限があります。 
> 
> 

## <a name="see-also"></a>関連項目
* [Azure CDN エンドポイント上のアセットを事前に読み込む](cdn-preload-endpoint.md)
* [Azure CDN REST API リファレンス - エンドポイントの消去または事前読み込み](/rest/api/cdn/endpoints)

