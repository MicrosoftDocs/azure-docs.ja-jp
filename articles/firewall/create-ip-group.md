---
title: Azure Firewall で IP グループを作成する
description: IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602535"
---
# <a name="create-ip-groups"></a>IP グループの作成

IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。 1 つの IP アドレス、複数の IP アドレス、または 1 つ以上の IP アドレス範囲を設定できます。

## <a name="create-an-ip-group"></a>IP グループを作成する

1. Azure portal のホーム ページから **[リソースの作成]** を選択します。
2. 検索テキスト ボックスに「**IP グループ**」と入力し、 **[IP Groups]\(IP グループ\)** を選択します。
3. **［作成］** を選択します
4. サブスクリプションを選択します。
5. リソース グループを選択するか、新しく作成します。
6. IP グループの一意の名前を入力し、リージョンを選択します。

6. **Next:次へ: IP アドレス** を選択します。
7. 1 つの IP アドレス、複数の IP アドレス、または IP アドレス範囲を入力します。

   IP アドレスを入力するには、2 つの方法があります。
   - 手動で入力できます
   - ファイルからインポートできます

   ファイルからインポートするには、 **[ファイルからインポート]** を選択します。 ファイルをボックスにドラッグするか、 **[ファイルの参照]** を選択します。 必要であれば、アップロードした IP アドレスを確認して、編集することができます。

   IP アドレスを入力すると、ポータルによって検証が行われ、オーバーラップ、重複、書式の問題が確認されます。

5. 終わったら、 **[確認と作成]** を選択します。
6. **［作成］** を選択します


## <a name="next-steps"></a>次のステップ

- [IP グループについてさらに確認する](ip-groups.md)