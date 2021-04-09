---
title: Azure ロールベースのアクセス制御 - Custom Vision
titleSuffix: Azure Cognitive Services
description: この記事では、Custom Vision プロジェクトに対して Azure ロールベースのアクセス制御を構成する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616894"
---
# <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Custom Vision では、Azure リソースへの個々のアクセスを管理するための認可システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Azure RBAC を使用すると、チーム メンバーによって異なる Custom Vision プロジェクトに対するアクセス許可レベルを割り当てることができます。 Azure RBAC の詳細については、「[Azure RBAC のドキュメント](../../role-based-access-control/index.yml)」を参照してください。

## <a name="add-role-assignment-to-custom-vision-resource"></a>Custom Vision リソースへのロールの割り当ての追加

Azure RBAC は、Custom Vision リソースに割り当てることができます。 Azure リソースへのアクセス権を付与するには、ロールの割り当てを追加します。
1. [Azure portal](https://ms.portal.azure.com/) で **[すべてのサービス]** を選択します。 
1. 次に、 **[Cognitive Services]** を選択し、特定の Custom Vision トレーニング リソースに移動します。
   > [!NOTE]
   > また、リソース グループ、サブスクリプション、または管理グループ全体に対して Azure RBAC を設定することもできます。 これを行うには、目的のスコープ レベルを選択し、目的の項目に移動します (たとえば、 **[リソース グループ]** を選択し、目的のリソース グループをクリックします)。
1. 左側のナビゲーション ウィンドウで **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当て]** タブを選択して、このスコープのロールの割り当てを表示します。
1. **[追加]**  ->  **[ロール割り当ての追加]** の順に選択します。
1. **[ロール]** ドロップダウン リストで追加するロールを選択します。
1. **[選択]** 一覧で、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、[選択] ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。
1. **[保存]** を選択してロールを割り当てます。

数分以内に、選択したスコープで選択したロールがターゲットに割り当てられます。

## <a name="custom-vision-role-types"></a>Custom Vision ロールの種類

次の表を使用して、Custom Vision リソースのアクセス ニーズを判断してください。

|Role  |アクセス許可  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | プロジェクトの作成、編集、または削除を含む、プロジェクトへのフル アクセス。        |
|`Cognitive Services Custom Vision Trainer`     | プロジェクトの作成または削除を除くフル アクセス。 トレーナーは、プロジェクトの表示と編集、モデルのトレーニング、公開、非公開、エクスポートを行うことができます。        |
|`Cognitive Services Custom Vision Labeler`     | トレーニング画像のアップロード、編集、または削除、タグの作成、追加、削除、または削除を行うことができます。 ラベラーはプロジェクトを表示できますが、トレーニング画像とタグ以外は更新できません。         |
|`Cognitive Services Custom Vision Deployment`     | モデルの公開、非公開、またはエクスポートができます。 デプロイ担当者はプロジェクトを表示できますが、プロジェクト、トレーニング画像、タグを更新することはできません。        |
|`Cognitive Services Custom Vision Reader`     | プロジェクトを表示できます。 閲覧者は変更を加えることはできません。        |