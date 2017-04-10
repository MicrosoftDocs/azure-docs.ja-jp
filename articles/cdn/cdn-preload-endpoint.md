---
title: "Azure CDN エンドポイント上のアセットを事前に読み込む | Microsoft Docs"
description: "Azure CDN エンドポイント上のキャッシュされたコンテンツを事前に読み込む方法について説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 242c3a6bf656da9b029a780e8b80667405b7b92f
ms.lasthandoff: 03/04/2017


---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Azure CDN エンドポイント上の資産を事前に読み込む
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

既定では、資産は要求されたときに初めてキャッシュされます。 つまり、各リージョンからの最初の要求は時間がかかる場合があります。これは、エッジ サーバーにキャッシュされたコンテンツがなく、配信元サーバーに要求を転送する必要があるためです。 コンテンツを事前に読み込んでおくと、この最初のヒットの待機時間を回避できます。

カスタマー エクスペリエンスの向上に加え、キャッシュされた資産を事前に読み込んでおくと、配信元サーバーのネットワーク トラフィックを減少させることもできます。

> [!NOTE]
> 資産の事前読み込みは、大規模なイベントや多数のユーザーが同時に利用できるコンテンツで役立ちます。たとえば、新しいムービーのリリースやソフトウェアの更新などがあります。
> 
> 

このチュートリアルでは、すべての Azure CDN エッジ ノード上のキャッシュされたコンテンツを事前に読み込む方法について説明します。

## <a name="walkthrough"></a>チュートリアル
1. [Azure ポータル](https://portal.azure.com)で、事前に読み込むエンドポイントを含む CDN プロファイルを参照します。  プロファイル ブレードが開きます。
2. リスト内のエンドポイントをクリックします。  エンドポイント ブレードが開きます。
3. CDN エンドポイント ブレードで、[読み込み] ボタンをクリックします。
   
    ![CDN エンドポイント ブレード](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    [読み込み] ブレードが開きます。
   
    ![CDN 読み込みブレード](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. 読み込む各資産の完全パスを **[パス]** テキストボックスに入力します (`/pictures/kitten.png` など)。
   
   > [!TIP]
   > テキストを入力すると、 **[パス]** テキストボックスが追加され、複数の資産の一覧を作成できます。  一覧から資産を削除するには、省略記号 (...) ボタンをクリックします。
   > 
   > パスは、次の[正規表現](https://msdn.microsoft.com/library/az24scfc.aspx)に準拠する相対 URL にする必要があります。  
   > >1 つのファイルの読み込み: `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`  
   > >クエリ文字列を使用した&1; つのファイルの読み込み: `@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > 資産ごとに独自のパスが必要です。  資産を事前に読み込むためのワイルドカード機能はありません。
   > 
   > 
   
    ![[読み込み] ボタン](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. **[読み込み]** ボタンをクリックします。
   
    ![[読み込み] ボタン](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> 読み込み要求は、CDN プロファイルごとに 1 分あたり 10 件に制限されています。
> 
> 

## <a name="see-also"></a>関連項目
* [Azure CDN エンドポイントの消去](cdn-purge-endpoint.md)
* [Azure CDN REST API リファレンス - エンドポイントの消去または事前読み込み](https://msdn.microsoft.com/library/mt634451.aspx)


