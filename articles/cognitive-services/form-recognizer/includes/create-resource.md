---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467310"
---
Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します </a>。 **[作成]** ウィンドウには以下の情報が表示されます。

| プロジェクトの詳細   | 説明   |
|--|--|
| **サブスクリプション** | アクセスが許可されている Azure サブスクリプションを選択します。 |
| **リソース グループ** | リソースを含む [Azure リソース グループ](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |
| **リージョン** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
| **名前** | リソースのわかりやすい名前。 わかりやすい名前 (*MyNameFaceAPIAccount* など) を使用することをお勧めします。 |
| **価格レベル** | リソースのコストは、選択した価格レベルと使用量に依存します。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。

> [!NOTE]
> 通常、Azure portal で Cognitive Service リソースを作成するときに、マルチ サービスのサブスクリプション キー (複数の Cognitive Services で使用) または 単一サービスのサブスクリプション キー (特定の Cognitive Services でのみ使用) を作成するオプションがあります。 ただしマルチサービス サブスクリプションには、現在 Form Recognizer は含まれていません。

Form Recognizer リソースがデプロイが完了すると、ポータルの **[すべてのリソース]** の一覧からこれを検索して選択します。 キーとエンドポイントは、リソースの [key and endpoint]\(キーとエンドポイント\) ページの [リソース管理] にあります。 これらの両方を一時的な場所に保存してから、先に進んでください。
