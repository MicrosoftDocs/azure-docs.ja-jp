---
title: ポータルで HTTP の要求および応答ヘッダーを書き換える - Azure Application Gateway
description: Azure portal を使用して、ゲートウェイを通過する要求と応答の HTTP ヘッダーを書き換えるように Azure Application Gateway を構成する方法について説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708936"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway で HTTP の要求および応答ヘッダーを書き換える - Azure portal

この記事では、Azure portal を使用して、要求と応答の HTTP ヘッダーを書き換えるように [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) インスタンスを構成する方法を説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには Application Gateway v2 SKU インスタンスが必要です。 ヘッダーの書き換えは v1 SKU ではサポートされていません。 v2 SKU を持っていない場合は、始める前に [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) インスタンスを作成してください。

## <a name="create-required-objects"></a>必要なオブジェクトを作成する

HTTP ヘッダーの書き換えを構成するには、次の手順のようにする必要があります。

1. HTTP ヘッダーの書き換えに必要なオブジェクトを作成します。

   - **書き換えアクション**:書き換えようとしている要求フィールドと要求ヘッダー フィールド、およびヘッダーの新しい値を指定するために使用されます。 1 つ以上の書き換え条件を 1 つの書き換えアクションに関連付けることができます。

   - **書き換え条件**:オプションの構成。 書き換え条件では、HTTP(S) の要求と応答の内容が評価されます。 HTTP(S) の要求または応答が書き換え条件に一致する場合、書き換えアクションが発生します。

     複数の条件を 1 つのアクションと関連付けた場合は、すべての条件が満たされた場合にのみアクションが発生します。 つまり、操作は論理 AND 操作です。

   - **書き換え規則**:複数の書き換えアクション/書き換え条件の組み合わせが含まれます。

   - **規則のシーケンス**:書き換え規則の実行順序を決定するのに役立ちます。 この構成は、書き換えセットに複数の書き換え規則がある場合に便利です。 規則のシーケンスの値が小さい書き換え規則から先に実行されます。 2 つの書き換え規則に同じ規則のシーケンスの値を割り当てた場合、実行順序は非決定論的となります。

   - **書き換えセット**:要求ルーティング規則に関連付けられる複数の書き換え規則が含まれます。

2. 書き換えセットをルーティング規則にアタッチします。 書き換え構成が、ルーティング規則によってソース リスナーにアタッチされます。 基本ルーティング規則を使うと、ヘッダー書き換え構成はソース リスナーに関連付けられ、グローバルなヘッダーの書き換えになります。 パスベースのルーティング規則を使うと、ヘッダー書き換え構成は URL パス マップで定義されます。 その場合、サイトの特定のパス領域にのみ適用されます。

HTTP ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="configure-header-rewrite"></a>ヘッダーの書き換えを構成する

この例では、バックエンド アプリケーションから送信された HTTP 応答の場所ヘッダーを書き換えることによってリダイレクト URL を変更します。

1. **[すべてのリソース]** を選択して、アプリケーション ゲートウェイを選択します。

2. 左側のウィンドウで **[Rewrites]\(書き換え\)** を選択します。

3. **[Rewrite set]\(書き換えセット\)** を選択します。

   ![書き換えセットを追加する](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 書き換えセットの名前を指定し、ルーティング規則に関連付けます。

   - **[名前]** ボックスに、書き換えセットの名前を入力します。
   - **[関連付けられているルーティング規則]** の一覧に表示されている規則の 1 つ以上を選択します。 他の書き換えセットに関連付けられていない規則のみを選択できます。 既に他の書き換えセットに関連付けられている規則は淡色表示されます。
   - **[次へ]** を選択します。
   
     ![名前の関連付けを追加する](media/rewrite-http-headers-portal/name-and-association.png)

5. 書き換えルールを作成します。

   - **[書き換え規則の追加]** を選択します。

     ![書き換え規則を追加する](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 書き換え規則の名前を **[書き換えルール名]** ボックスに入力します。 **[規則のシーケンス]** ボックスに値を入力します。

     ![書き換え規則名を追加する](media/rewrite-http-headers-portal/rule-name.png)

6. この例では、azurewebsites.net への参照が含まれている場合にのみ、場所ヘッダーを書き換えます。 これを行うには、応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件を追加します。

   - **[条件の追加]** を選択し、 **[If]** 命令がを含まれるボックスを選択して展開します。

     ![条件の追加](media/rewrite-http-headers-portal/add-condition.png)

   - **[チェックする変数の型]** ボックスの一覧で、**[HTTP ヘッダー]** を選択します。

   - **[ヘッダーの種類]** ボックスの一覧で、**[応答]** を選択します。

   - この例では場所ヘッダーを評価していて、それは共通ヘッダーなので、**[ヘッダー名]** で **[共通ヘッダー]** を選択します。

   - **[共通ヘッダー]** ボックスの一覧で、**[場所]** を選択します。

   - **[大文字と小文字を区別する]** で、 **[いいえ]** を選択します。

   - **[演算子]** ボックスの一覧で、 **[等しい (=)]** を選択します。

   - 正規表現パターンを入力します。 この例では、パターン `(https?)://.*azurewebsites.net(.*)$` を使用します。

   - **[OK]** を選択します。

     ![If 条件を構成する](media/rewrite-http-headers-portal/condition.png)

7. 場所ヘッダーを書き換えるアクションを追加します。

   - **[アクションの種類]** ボックスの一覧で、 **[設定]** を選択します。

   - **[ヘッダーの種類]** ボックスの一覧で、**[応答]** を選択します。

   - **[ヘッダー名]** で、**[共通ヘッダー]** を選択します。

   - **[共通ヘッダー]** ボックスの一覧で、**[場所]** を選択します。

   - ヘッダー値を入力します。 この例では、ヘッダー値として `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` を使います。 この値により、場所ヘッダーの *azurewebsites.net* が *contoso.com* に置き換えられます。

   - **[OK]** を選択します。

     ![アクションを追加する](media/rewrite-http-headers-portal/action.png)

8. **[作成]** を選択して、書き換えセットを作成します。

   ![[作成] を選択します](media/rewrite-http-headers-portal/create.png)

9. [書き換えセット] ビューが開きます。 作成した書き換えセットが、書き換えセットの一覧に含まれることを確認します。

   ![[書き換えセット] ビュー](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>次のステップ

一般的なユース ケースの設定方法の詳細については、[共通ヘッダーの書き換えシナリオ](./rewrite-http-headers.md)に関する記事をご覧ください。