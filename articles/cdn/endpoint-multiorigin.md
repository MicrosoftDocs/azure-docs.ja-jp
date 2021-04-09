---
title: Azure CDN エンドポイントの複数配信元 (プレビュー)
description: Azure CDN エンドポイントで複数の配信元を使用します。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685773"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN エンドポイントの複数配信元

複数の配信元のサポートによって、ダウンタイムが解消し、グローバルな冗長性が確立されます。 

Azure CDN エンドポイント内で複数の配信元を選択すると、提供される冗長性によって、各配信元の正常性が調査され、必要に応じてフェールオーバーされることによって、リスクが分散されます。

1 つまたは複数の配信元グループを設定して、既定の配信元グループを選択します。 各配信元グループは、類似したワークロードを受け取ることができる 1 つ以上の配信元のコレクションです。

> [!NOTE]
> 現在、この機能は Microsoft の Azure CDN からのみ使用できます。 

## <a name="create-the-origin-group"></a>配信元グループを作成する

1. [Azure ポータル](https://portal.azure.com)

2. Azure CDN プロファイルを選択した後、複数配信元用に構成するエンドポイントを選択します。

3. エンドポイントの構成で **[設定]** の **[配信元]** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN エンドポイント" border="true":::

4. 複数配信元を有効にするには、少なくとも 1 つの配信元グループが必要です。 **[Create Origin Group]\(配信元グループの作成\)** を選択 ます。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="配信元の設定" border="true":::

5. **[Add Origin Group]\(配信元グループの追加\)** の構成で、次の情報を入力または選択します。

   | 設定           | 値                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Origin group name (配信元グループ名) | 配信元グループの名前を入力します。                                   |
   | プローブの状態      | **[Enabled]** を選択します。 </br> Azure CDN では、世界中のさまざまなポイントから正常性プローブを実行して、配信元の正常性を判断します。 追加コストを回避するために現在の配信元グループがアクティブになっていない場合は、有効にしないでください。
   | プローブ パス        | 正常性を判断するために使用される、配信元内のパス。 |
   | Probe interval (プローブ間隔)    | プローブの間隔を 1、2、または 4 分から選択します。                        |
   | プローブ プロトコル    | **[HTTP]** または **[HTTPS]** を選択します。                                         |
   | プローブ メソッド      | **[Head]** または **[Get]** を選択します。                                           |
   | 既定の配信元グループ | 既定の配信元グループとして設定するボックスを選択します。
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="配信元グループを追加する" border="true":::

6. **[追加]** を選択します。

## <a name="add-multiple-origins"></a>複数の配信元を追加する

1. エンドポイントの配信元の設定で、 **[+ Create Origin]\(+ 配信元の作成\)** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="配信元を作成する" border="true":::

2. **[Add Origin]\(配信元の追加\)** の構成で、次の情報を入力または選択します。

   | 設定           | 値                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 名前        | 配信元の名前を入力します。        |
   | 配信元の種類 | **[ストレージ]** 、 **[クラウド サービス]** 、 **[Web アプリ]** 、または **[カスタムの配信元]** を選択します。                                   |
   | 配信元のホスト名        | 配信元のホスト名を選択または入力します。  ドロップダウンに、前の設定で指定したすべての利用可能な配信元の種類が一覧表示されます。 配信元の種類として **[カスタムの配信元]** を選択した場合は、顧客の配信元サーバーのドメインを入力します。 |
   | 配信元のホスト ヘッダー    | Azure CDN の各要求で送信するホスト ヘッダーを入力するか、既定値をそのまま使用します。                        |
   | HTTP ポート   | HTTP ポートを入力します。                                         |
   | HTTPS ポート     | HTTPS ポートを入力します。                                           |
   | 優先度    | 1 から 5 の範囲の値を入力します。       |
   | Weight      | 1 から 1000 の範囲の値を入力します。   |

    > [!NOTE]
    > 配信元を配信元グループに作成する場合は、優先順位と重みを一致させる必要があります。 配信元グループに配信元が 1 つしかない場合、既定の優先順位と重みは 1 に設定されます。 トラフィックは最も優先順位の高い配信元にルーティングされます (配信元が正常な場合)。 配信元が異常であると判断された場合、接続は優先順位の順に別の配信元に転送されます。 同じ優先順位の配信元が複数ある場合、トラフィックは配信元に対して指定された重みに従って分散されます 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="追加の配信元を追加する" border="true":::

3. **[追加]** を選択します。

4. すべての配信元に配信元パスを設定するには、 **[Configure origin]\(配信元の構成\)** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="配信元のパスを構成する" border="true":::

5. **[OK]** を選択します。

## <a name="configure-origins-and-origin-group-settings"></a>配信元と配信元グループの設定を構成する

複数の配信元と配信元グループがある場合は、配信元を別のグループに追加したり、別のグループから削除したりできます。 同じグループ内の配信元は、同種のワークロードを提供する必要があります。 トラフィックは、正常性の状態、優先順位、重みの値に基づいて、これらの配信元に分散されます。 

1. Azure CDN エンドポイントの配信元の設定で、構成する配信元グループの名前を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="配信元と配信元グループの設定を構成する" border="true":::

2. **[Update origin group]\(配信元グループの更新\)** で、 **[+ Select Origin]\(配信元の選択\)** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="配信元グループを更新する" border="true":::

4. プルダウン ボックスでグループに追加する配信元を選択し、 **[OK]** を選択します。

5. 配信元がグループに追加されたことを確認し、 **[保存]** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="グループに追加された追加の配信元を確認する" border="true":::

## <a name="remove-origin-from-origin-group"></a>配信元グループから配信元を削除する

1. Azure CDN エンドポイントの配信元の設定で、配信元グループの名前を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="グループから配信元を削除する" border="true":::

2. 配信元グループから配信元を削除するには、配信元の横にあるごみ箱アイコンを選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="配信元グループの更新と配信元の削除" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>ルール エンジンを使用した配信元グループのオーバーライド

標準ルール エンジンを使用して、さまざまな配信元グループにトラフィックを分散する方法をカスタマイズします。

要求 URL に基づいて、トラフィックを別のグループに分散します。

1. CDN エンドポイントの **[設定]** で **[ルール エンジン]** を選択します。

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="ルール エンジン" border="true":::

2. **[+ ルールの追加]** を選択します。

3. **[名前]** にルールの名前を入力します。

4. **[+ 条件]** を選択し、 **[URL パス]** を選択します。

5. **[演算子]** プルダウンで、 **[含む]** を選択します。

6. **[値]** に「 **/images**」と入力します。

7. **[+ アクションの追加]** を選択し、 **[配信元グループのオーバーライド]** を選択します。

8. **[配信元グループ]** のプルダウン ボックスで配信元グループを選択します。

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="ルール エンジンの条件" border="true":::

いずれの受信要求についても、URL パスに「 **/images**」が含まれる場合、要求はアクション セクション **(myorigingroup)** の配信元グループに割り当てられます。 

## <a name="next-steps"></a>次の手順
この記事では、Azure CDN エンドポイントの複数配信元を有効にしました。

Azure CDN とこの記事で言及しているその他の Azure サービスの詳細については、次をご覧ください。

* [Azure CDN](./cdn-overview.md)
* [Azure CDN 製品の機能を比較する](./cdn-features.md)
