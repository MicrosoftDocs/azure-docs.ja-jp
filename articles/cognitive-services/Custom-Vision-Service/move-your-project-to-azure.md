---
title: 期間限定試用版プロジェクトを Azure に移行する
titlesuffix: Azure Cognitive Services
description: 期間限定試用版プロジェクトを Azure に移行する方法について説明します。
services: cognitive-services
author: anrothmsft
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816522"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>期間限定試用版のプロジェクトを Azure に移行する方法

Custom Vision Service の Azure への移行を完了すると、Azure の外部での期間限定試用版プロジェクトのサポートは終了します。 このドキュメントでは、Custom Vision API を使用して、期間限定試用版プロジェクトを Azure リソースにコピーする方法について説明します。

[Custom Vision Web サイト](https://customvision.ai)上で期間限定試用版プロジェクトを表示する機能のサポートは、2019 年 3 月 25 日に終了しました。 このドキュメントでは、GitHub 上の[移行 Python スクリプト](https://github.com/Azure-Samples/custom-vision-move-project)と共に Custom Vision API を使用して、プロジェクトを Azure リソースに複製する方法について説明します。

期間限定試用版の廃止プロセスの主な期限など、詳細については、[リリース ノート](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019)のページ、または期間限定試用版プロジェクトの所有者に送信されるメールのお知らせを参照してください。

[移行スクリプト](https://github.com/Azure-Samples/custom-vision-move-project)を使用すると、現在のイテレーションのすべてのタグ、リージョン、およびイメージをダウンロードしてからアップロードすることで、プロジェクトを再作成できます。 新しいサブスクリプションに新しいプロジェクトが作成され、それをトレーニングすることができます。

## <a name="prerequisites"></a>前提条件

- [Custom Vision Web サイト](https://customvision.ai)にログインするために使用するのと Microsoft アカウントまたは Azure Active Directory (AAD) アカウントに関連付けられた有効な Azure サブスクリプションが必要です。 
    - Azure アカウントを持っていない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/)します。
    - Azure のサブスクリプションやリソースの概念の概要については、[Azure 開発者向けガイド](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)を参照してください。
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure portal で Custom Vision リソースを作成する

Azure で Custom Vision Service を使用するには、[Azure Portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) で Custom Vision Training および Prediction リソースを作成する必要があります。 

1 つのリソースに複数のプロジェクトを関連付けることができます。 [料金と制限](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)に関する詳細情報を参照できます。 Custom Vision Service を引き続き無料で使用するには、Azure Portal で F0 レベルを選択できます。 

> [!NOTE]
> Custom Vision プロジェクトを Azure リソースに移行すると、その Azure リソースの基になる[アクセス許可]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)が継承されます。 組織内の他のユーザーが、プロジェクトが属している Azure リソースの所有者である場合、それらのユーザーは [Custom Vision Web サイト](https://customvision.ai)でそのプロジェクトにアクセスできます。 同様に、リソースを削除すると、そのプロジェクトも削除されます。  

## <a name="find-your-limited-trial-project-information"></a>期間限定試用版プロジェクト情報を検索する

プロジェクトを移行するには、移行対象のプロジェクトの "_プロジェクト ID_" と "_トレーニング キー_" が必要です。 この情報がわからない場合は、[https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) にアクセスして各プロジェクトの ID とキーを取得します。 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Python サンプル コードを使用してプロジェクトを Azure にコピーする

[サンプル コードの指示](https://github.com/Azure-Samples/custom-vision-move-project)に従い、期間限定試用版のキーとプロジェクト ID を "ソース" 素材として使用し、作成した新しい Azure リソースのキーを "宛先" として使用します。

既定では、すべての期間限定試用版プロジェクトは米国中南部の Azure リージョンでホストされています。

## <a name="next-steps"></a>次の手順

プロジェクトが Azure リソースに移行されました。 これまでに記述したすべてのアプリケーションで Training および Prediction キーを更新する必要があります。

プロジェクトを [Custom Vision Web サイト](https://customvision.ai)で表示するには、Azure portal へのサインインに使用したものと同じアカウントでサインインします。 プロジェクトが表示されない場合は、[Custom Vision Web サイト](https://customvision.ai)のディレクトリが、Azure portal でリソースが配置されているディレクトリと同じであることを確認してください。 Azure Portal と CustomVision.ai の両方で、画面の右上隅にあるドロップダウンのユーザー メニューからディレクトリを選択できます。