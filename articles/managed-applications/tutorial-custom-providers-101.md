---
title: Azure でカスタム アクションとカスタム リソースを作成する
description: このチュートリアルでは、Azure Resource Manager でカスタム アクションとカスタム リソースを作成する方法、およびそれらを Azure Resource Manager テンプレート、Azure CLI、Azure Policy、アクティビティ ログのカスタム ワークフローに統合する方法について説明します。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799191"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Azure でカスタム アクションとカスタム リソースを作成する

カスタム プロバイダーを使うと、Azure 上でワークフローをカスタマイズできます。 カスタム プロバイダーは、Azure と`endpoint`の間のコントラクトです。 Azure Resource Manager に新しいカスタム API を追加して、デプロイと管理の新しい機能を有効にできます。 このチュートリアルでは、簡単な例を使って、Azure に新しいアクションとリソースを追加する方法、およびそれらを統合する方法を説明します。

このチュートリアルは、次のステップに分かれています。

- Azure カスタム プロバイダー用に Azure Functions を設定する
- カスタム プロバイダー用の RESTful エンドポイントを作成する
- カスタム プロバイダーを作成して利用する

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Azure カスタム プロバイダー用に Azure Functions を設定する

チュートリアルのこの部分では、カスタム プロバイダーを使用するために Azure 関数を設定する方法の詳細を説明します。 カスタム プロバイダーでは、任意のパブリック URL を使用できます。

- [Azure カスタム プロバイダー用に Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>カスタム プロバイダー用の RESTful エンドポイントを作成する

チュートリアルのこの部分では、カスタム プロバイダー用の RESTful エンドポイントを作成する方法の詳細を説明します。

- [カスタム プロバイダー用の RESTful エンドポイントを作成する](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>カスタム プロバイダーを作成して利用する

チュートリアルのこの部分では、カスタム プロバイダーを作成し、そのカスタム アクションとカスタム リソースを使用する方法の詳細を説明します。

- [Azure カスタム プロバイダーを作成して利用する](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>次の手順

この記事では、カスタム プロバイダーとその作成方法について学習しました。 チュートリアルの次のステップに進むには:

- [チュートリアル:Azure カスタム プロバイダー用に Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)

リファレンスやクイックスタートを探している場合に役に立つリンクをいくつか次に示します。

- [クイック スタート:Azure カスタム リソース プロバイダーを作成してカスタム リソースをデプロイする](./create-custom-provider.md)
- [方法:カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法:カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
