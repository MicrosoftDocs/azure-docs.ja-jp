---
title: "Azure CDN ルール エンジンを使用した HTTP 動作のオーバーライド | Microsoft Docs"
description: "規則エンジンでは、Azure CDN における HTTP 要求の処理方法 (特定種類のコンテンツの配信のブロックなど) のカスタマイズのほか、キャッシュ ポリシーの定義、HTTP ヘッダーの変更などを行えます。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.lasthandoff: 01/25/2017


---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンを使用した HTTP 動作のオーバーライド
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概要
規則エンジンでは、特定の種類のコンテンツの配信のブロック、キャッシュ ポリシーの定義、HTTP ヘッダーの変更など、HTTP 要求の処理方法をカスタマイズできます。  このチュートリアルでは、CDN 資産のキャッシュ動作を変更するルールの作成について説明します。  また、「[関連項目](#see-also)」セクションではビデオ コンテンツもご覧いただけます。

   > [!TIP] 
   > 構文の詳細については、「[ルール エンジンのリファレンス](cdn-rules-engine-reference.md)」を参照してください。
   > 


## <a name="tutorial"></a>チュートリアル
1. [CDN プロファイル] ブレードで、 **[管理]** をクリックします。
   
    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
2. **[HTTP ラージ]** タブ、**[ルール エンジン]** の順にクリックします。
   
    新しい規則のオプションが表示されます。
   
    ![CDN の新しい規則オプション](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > 複数の規則が表示される順序は、これらの規則の処理方法に影響します。 前の規則で指定したアクションは、後続の規則でオーバーライドされます。
   > 
   > 
3. **[名前 / 説明]** テキストボックスに名前を入力します。
4. 規則を適用する要求の種類を指定します。  既定では、一致条件 **[常に]** が選択されています。  このチュートリアルでは **[常に]** を使用するため、選択されたままにしておきます。
   
   ![CDN の一致条件](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > ドロップダウンには多くの種類の一致条件が用意されています。  一致条件の左側の青い情報アイコンをクリックすると、現在選択している状態の詳細が表示されます。
   > 
   >  条件式の詳しい完全な一覧については、「[ルール エンジンの条件式](cdn-rules-engine-reference-match-conditions.md)」を参照してください。
   >  
   > 一致条件の詳しい完全な一覧については、「[ルール エンジンの一致条件](cdn-rules-engine-reference-match-conditions.md)」を参照してください。
   > 
   > 
5. **[機能]** の横にある **+** をクリックして、新しい機能を追加します。  左側のドロップダウンで、 **[内部の最長期間を強制する]**を選択します。  表示されるテキスト ボックスに、「 **300**」と入力します。  他の既定値はそのまま使用します。
   
   ![CDN の機能](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > 一致条件と同様に、新しい機能の左側にある青い情報アイコンをクリックすると、その機能に関する詳細が表示されます。  **[Force Internal Max-Age (内部の最長期間を強制する)]** では、**Cache-Control** および **Expires** ヘッダーをオーバーライドして、CDN エッジ ノードが配信元からの資産を更新するタイミングを制御します。  今回の 300 秒のサンプルでは、その配信元から資産を更新する前に、CDN エッジ ノードは 5 分間、資産をキャッシュします。
   > 
   > 機能の詳しい完全な一覧については、「[ルール エンジンの機能詳細](cdn-rules-engine-reference-features.md)」を参照してください。
   > 
   > 
6. **[追加]** をクリックして、新しい規則を保存します。  次に、新しい規則は承認を待機します。 承認されると、ステータスが**保留中の XML** から**アクティブな XML** に変わります。
   
   > [!IMPORTANT]
   > 規則の変更が CDN に反映されるまでに最大 90 分かかる場合があります。
   > 
   > 

## <a name="see-also"></a>関連項目
* [Azure CDN の概要](cdn-overview.md)
* [ルール エンジンのリファレンス](cdn-rules-engine-reference.md)
* [ルール エンジンの一致条件](cdn-rules-engine-reference-match-conditions.md)
* [ルール エンジンの条件式](cdn-rules-engine-reference-conditional-expressions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)
* [Azure Friday: Azure CDN に新しく追加された強力な Premium 機能](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (ビデオ)
