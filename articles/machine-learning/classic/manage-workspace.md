---
title: 'ML Studio (classic): ワークスペースを管理する - Azure'
description: Azure Machine Learning Studio (クラシック) ワークスペースへのアクセスを管理し、Machine Learning API Web サービスをデプロイおよび管理します
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: c41c5350e29555a11bb74eeca9af62a6b87cfced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094273"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (クラシック) ワークスペースの管理

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> Machine Learning Web サービス ポータルでの Web サービスの管理については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。
> 
> 

Machine Learning Studio (クラシック) ワークスペースは、Azure portal で管理できます。



## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Studio (クラシック) ワークスペースを Azure portal で 管理するには:

1. Azure サブスクリプション管理者アカウントを使用して [Azure Portal](https://portal.azure.com/) にサインインします。
2. ページ上部の検索ボックスに「machine learning studio workspaces (クラシック)」と入力した後、 **[Machine Learning Studio (クラシック) ワークスペース]** を選択します。
3. 管理するワークスペースをクリックします。

標準のリソース管理情報と利用可能なオプションに加え、以下のことも可能です。

- **[プロパティ]** の表示 - このページではワークスペースとリソースの情報が表示され、このワークスペースに接続されているサブスクリプションとリソース グループが変更できます。
- **[ストレージの再同期]** - ワークスペースによってキーがストレージ アカウントに維持されます。 ストレージ アカウントによってキーが変更された場合は、 **[キーの再同期]** をクリックして、ワークスペースでキーを同期できます。

この Studio (クラシック) ワークスペースに関連付けられた Web サービスを管理するには、Machine Learning Web サービス ポータルを使用します。 詳細な情報については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。

> [!NOTE]
> New Web サービスをデプロイして管理するには、Web サービスがデプロイされているサブスクリプションで共同作成者または管理者ロールが割り当てられている必要があります。 別のユーザーを Machine Learning Studio (クラシック) ワークスペースに招待したら、Web サービスをデプロイまたは管理する前に、そのユーザーをサブスクリプションの共同作成者または管理者ロールに割り当てる必要があります。 
> 
>アクセス許可の設定の詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Resource Manager テンプレートを使用した Machine Learning のデプロイ](deploy-with-resource-manager-template.md)の詳細を確認する。