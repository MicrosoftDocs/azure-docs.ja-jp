---
title: "Azure Machine Learning ワークベンチを使用して JSON 変換を展開します"
description: "\"JSON の展開\" 変換の参照ドキュメント"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 57f461f9a39e8069654985d86bfe3ae23dd0855a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="expand-json-transformation"></a>JSON の展開変換
**JSON の展開**変換を使用すると、ユーザーは、有効な JSON テキストが含まれている既存の列を複数の列に展開できます。

## <a name="how-to-perform-this-transformation"></a>この変換を実行する方法

この変換を実行するには、次の手順に従います。
1. JSON テキストを含むソース列を選択します。
2. **[Transforms] (変換)** メニューから **[Expand JSON] (JSON の展開)** を選択します。 または、ソース列のヘッダーを右クリックして、コンテキスト メニューから **[Expand JSON] (JSON の展開)** を選択します。 
3. **[OK]**をクリックします。 
 
新しい列は、ソース列の横に追加されます。 これらの列には、JSON テキストにおける次のレベルの階層のプロパティが含まれます。 プロパティ キーは、存在する場合、対応する列の名前の作成に使用されます。 入れ子になったプロパティは、必要に応じてユーザーが繰り返し展開したり破棄したりできる JSON テキストとして保持されます。

## <a name="examples"></a>例

ソース列 *Customer* は *Customer.Name* と *Customer.Phone* の 2 つの列に展開されます。

| 顧客                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

