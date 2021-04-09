---
title: サポートされていない言語の配置 - カスタム翻訳ツール
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Custom Translator で未サポートの言語ペアをデプロイする方法について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898072"
---
# <a name="unsupported-language-deployments"></a>サポートされていない言語の展開

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

今後は Microsoft Translator Hub が廃止され、ハブを通じて現在展開されているすべてのモデルが展開されなくなります。 現在サポートされていない言語の組み合わせをカスタム翻訳ツールでハブで展開しているモデルは多くあります。  弊社では、そうしたコンテンツを翻訳するためのリソースがないという状況になることは望ましくありません。

カスタム翻訳ツールにより、サポートされていないモデルを展開できるようになりました。  このプロセスにより、最新の V3 API を使用して引き続きコンテンツを翻訳できます。  これらのモデルは、展開解除を選択するか、言語のペアがカスタム翻訳ツールで使用可能になるまでホスティングされます。  この記事では、サポートされていない言語の組み合わせを使用したモデルを展開するプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

自社モデルを展開の対象とするには、次の条件を満たしている必要があります。
* モデルを含むプロジェクトは、移行ツールを使用してハブからカスタム翻訳ツールに移行されていること。  移行するプロジェクトとワークスペースのプロセスは[ここ](how-to-migrate.md)にあります。
* モデルは、移行が発生したときに展開された状態でなければなりません。  
* モデルの言語ペアは、カスタム翻訳ツールで、サポートされていない言語のペアである必要があります。  言語のペアの片方が英語をサポートしているもののその言語は英語では無い場合、サポートされていない言語の展開の対象となります。  たとえば、フランス語とドイツ語の言語ペアにおいてフランス語から英語がサポートされていて英語からドイツ語がサポートされていても、この言語ペアのハブ モデルはサポートされていない言語の組み合わせとなります。

## <a name="process"></a>Process
展開の対象となるハブからモデルを移行すると、ワークスペースの **設定** ページに表示できます。ページ下部までスクロールすると **Unsupported Translator Hub Trainings** (サポートされていない翻訳ツールのハブ トレーニング) セクションが表示されます。  このセクションは、上記の前提条件を満たすプロジェクトの場合のみ表示されます。

![[Unsupported Translator Hub Trainings]\(サポートされていない翻訳ツールのハブ トレーニング\) セクションが強調表示されているスクリーンショット。](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

**Unsupported Translator Hub Trainings** (サポートされていない翻訳ツールのハブ トレーニング) において **[Unrequested trainings]** (要求されていないトレーニング) タブには、展開にふさわしいモデルが表示されます。  展開するモデルを選択し要求を送信します。   4 月 30 日の展開期限まで、展開を行おうとするモデルをできる限り多く選択できます。
 
![[Unrequested trainings]\(要求されていないトレーニング\) タブのスクリーンショット。](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

送信後はモデルは **[Unrequested trainings]** (要求されていないトレーニング) タブではなく **[Requested trainings]** (要求されたトレーニング) タブに表示されます。いつでも、要求されたトレーニングを表示できます。

![Hub から移行する方法](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>次の操作

ハブの使用を中止し、すべてのモデルの展開を解除すると、展開用に選択したモデルが保存されます。  サポートされていないモデルを展開するための要求は 5 月 24日までに送信してください。  これらのモデルは 6 月 15 日に展開し、翻訳ツールの V3 API を通じてアクセスできます。  さらに、V2 API は 7 月 1 日まで利用できます。  

ハブ チェックの使用停止に関する重要な日付については、[ここ](https://www.microsoft.com/translator/business/hub/)をクリックしてください。
展開後、通常のホスティング料金が適用されます。  詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)のページを参照してください。  

標準のカスタム翻訳ツール モデルとは異なりハブ モデルは単一の領域でのみ利用できるため、複数領域のホスティング料金は適用されません。  展開した後は、移行したカスタム翻訳ツール プロジェクトを通じていつでも、ハブのモデルを展開解除することができます。

## <a name="next-steps"></a>次のステップ

- [モデルをトレーニングします](how-to-train-model.md)。
- [Microsoft Translator Text API V3](../reference/v3-0-translate.md?tabs=curl) を介してデプロイされた翻訳モデルを使い始めます。