---
title: Azure Application Gateway で HTTP の要求および応答ヘッダーを書き換える - Azure portal | Microsoft Docs
description: Azure portal を使用して、ゲートウェイを通過する要求と応答の HTTP ヘッダーを書き換えるように Azure Application Gateway を構成する方法について説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994607"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway で HTTP の要求および応答ヘッダーを書き換える - Azure portal

この記事では、Azure portal を使用して、要求と応答の HTTP ヘッダーを書き換えるように [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) を構成する方法を示します。

> [!IMPORTANT]
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

v1 SKU ではヘッダーの書き換え機能がサポートされていないため、Application Gateway v2 SKU が必要です。 v2 SKU をお持ちでない場合は、開始する前に [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) を作成してください。

## <a name="what-is-required-to-rewrite-a-header"></a>ヘッダーの書き換えに必要なもの

HTTP ヘッダーの書き換えを構成するには、次のようにする必要があります。

1. http ヘッダーの書き換えに必要な新しいオブジェクトを作成します。

   - **書き換えアクション**: 書き換えようとしている要求および要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。 1 つ以上の書き換え条件を書き換えアクションに関連付けるように選択できます。

   - **書き換え条件**: これはオプション構成です。 書き換え条件が追加された場合、HTTP(S) 要求と応答のコンテンツが評価されます。 書き換え条件に関連付けられた書き換えアクションの実行は、HTTP(S) 要求または応答が書き換え条件と一致するかどうかに基づいて決定されます。 

     アクションに複数の条件が関連付けられている場合、そのアクションが実行されるのは、すべての条件が満たされている場合のみです。つまり、論理 AND 演算が実行されます。

   - **書き換えルール**: 書き換えルールには、書き換えアクションと書き換え条件の複数の組み合わせが含まれます。

   - **ルール順序**: さまざまな書き換えルールの実行順序を決定するのに役立ちます。 これは、書き換えセットに複数の書き換えルールがある場合に便利です。 ルール順序の値が小さい書き換えルールから先に実行されます。 2 つの書き換えルールに同じルール順序を指定した場合、実行順序は非決定性となります。

   - **書き換えセット**: 要求ルーティング規則に関連付けられる複数の書き換えルールが含まれます。

2. 書き換えセットをルーティング規則にアタッチする必要があります。 これは、書き換え構成がルーティング規則によってソース リスナーにアタッチされるためです。 基本ルーティング規則の使用時には、書き換えの構成は、ソース リスナーに関連付けられている、グローバルなヘッダーの書き換えとなります。 パスベースのルーティング規則を使用する場合、ヘッダーの書き換えの構成は、URL パス マップで定義されています。 そのため、サイトの特定のパス領域にのみ適用されます。

http ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 しかし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="configure-header-rewrite"></a>ヘッダーの書き換えを構成する

この例では、バックエンド アプリケーションから送信された HTTP 応答の場所ヘッダーを書き換えることによってリダイレクト URL を変更します。 

1. **[すべてのリソース]** を選択して、アプリケーション ゲートウェイを選択します。

2. 左側のメニューから **[Rewrites]**(書き換え) を選択します。

3. **[+Rewrite set]** (+ 書き換えセット) をクリックします。 

   ![書き換えセットを追加する](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 書き換えセットに名前を付け、ルーティング規則に関連付けます。

   - **[名前]** テキストボックスに、書き換えセットの名前を入力します。
   - 表示されている 1 つまたは複数のルールの選択、**[関連付けられているルーティング規則]** リストに一覧表示された 1 つ以上の規則を選択します。 他の書き換えセットに関連付けられていない規則のみ選択できます。 既に他の書き換えセットに関連付けられている規則は淡色表示されます。
   - [次へ] をクリックします。
   
     ![名前の関連付けを追加する](media/rewrite-http-headers-portal/name-and-association.png)

5. 書き換えルールを作成します。

   - **[+ 書き換え規則の追加]** をクリックします。![書き換え規則の追加](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - [Rewrite rule name]\(書き換え規則名) テキストボックスに書き換えルールの名前を入力し、ルール順序を指定します。![規則名を追加する](media/rewrite-http-headers-portal/rule-name.png)

6. この例では、"azurewebsites.net" への参照が含まれている場合にのみ、場所ヘッダーを書き換えます。 これを行うには、応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件を追加します。

   - **[+ 条件の追加]** をクリックし、続いて **If** 命令を持つセクションをクリックして展開します。![[Add rule name]](media/rewrite-http-headers-portal/add-condition.png)(ルール名の追加)

   - **[チェックする変数の型]** ドロップダウンから、**[HTTP ヘッダー]** を選択します。 

   - **[応答]** として **[ヘッダーの種類]** を選択します。

   - この例では、たまたま共通ヘッダーである場所ヘッダーを評価しているので、**[ヘッダー名]** として **[Common header]**(共通ヘッダー) ラジオ ボタンを選択します。

   - **[Common header]**(共通ヘッダー) ドロップダウンから **[場所]** を選択します。

   - **[大文字と小文字の区別]** 設定として **[いいえ]** を選択します。

   - **[演算子]** ドロップダウンから **[等号 (=)]** を選択します。

   - 正規表現パターンを入力します。 この例では、`(https?):\/\/.*azurewebsites\.net(.*)$` のパターンを使用します。

   - Click **OK**.

     ![場所ヘッダーを変更する](media/rewrite-http-headers-portal/condition.png)

7. 場所ヘッダーを書き換えるアクションを追加します。

   - **[アクションの種類]** として **[設定]** を選択します。

   - **[ヘッダーの種類]** として **[応答]** を選択します。

   - **[ヘッダー名]** として **[Common header]**(共通ヘッダー) を選択します。

   - **[Common header]**(共通ヘッダー) ドロップダウンから **[場所]** を選択します。

   - ヘッダー値を入力します。 この例では、ヘッダー値として `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` を使用します。 これにより、場所ヘッダーの *azurewebsites.net* が *contoso.com* に置き換えられます。

   - Click **OK**.

     ![場所ヘッダーを変更する](media/rewrite-http-headers-portal/action.png)

8. **[作成]** をクリックして、書き換えセットを作成します。

   ![場所ヘッダーを変更する](media/rewrite-http-headers-portal/create.png)

9. 書き換えセット ビューに移動します。 上記で作成した書き換えセットが書き換えセットの一覧に存在することを確認します。

   ![場所ヘッダーを変更する](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>次の手順

一般的ないくつかのユース ケースを実行するのに必要な構成についてさらに学習する場合は、[一般的なヘッダーの書き換えシナリオ](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)に関するページを参照してください。

   
