---
title: マッピング データ フローの複数の分岐
description: 複数の分岐を持つマッピング データ フローでのデータ ストリームのレプリケート
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606394"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>マッピング データ フローでの新しい分岐の作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

新しい分岐を追加して、同じデータ ストリームに対して複数の操作と変換を行います。 新しい分岐を追加すると、複数のシンクや自己結合データに対して同じソースをまとめて使用する場合に便利です。

他の変換と同様に、変換リストから新しい分岐を追加できます。 **[New Branch]** (新しい分岐) は、分岐しようとしている変換の後に既存の変換が行われる場合にのみ、アクションとして利用可能になります。

![新しい分岐の追加](media/data-flow/new-branch2.png "新しい分岐の追加")

次の例では、データ フローがタクシー乗車データを読み取っています。 曜日とベンダーの両方で集計された出力が必要です。 同じソースから読み取る 2 つの異なるデータ フローを作成するのではなく、新しい分岐を追加できます。 これにより、両方の集計を同じデータ フローの一部として実行できます。 

![新しい分岐の追加](media/data-flow/new-branch.png "新しい分岐の追加")
