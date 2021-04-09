---
title: カスタム アクションとリソースを作成する
description: このチュートリアルでは、Azure Resource Manager でカスタム アクションとリソースを作成する方法について説明します。 また、Azure Resource Manager テンプレートや Azure CLI、Azure Policy、Azure アクティビティ ログとの間で、カスタム ワークフローがどのように作用するかについても取り上げます。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "75648549"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Azure でカスタム アクションとカスタム リソースを作成する

カスタム プロバイダーは、Azure とエンドポイントの間のコントラクトです。 カスタム プロバイダーを使うと、Azure Resource Manager に新しい API を追加することによって、Azure 内のワークフローに変更を加えることができます。 Resource Manager からそれらのカスタム API を通じて、新しいデプロイ機能と管理機能を使用することができます。

このチュートリアルでは、簡単な例を使って、Azure に新しいアクションとリソースを追加する方法、およびそれらを統合する方法を説明します。

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure カスタム プロバイダー用の Azure Functions を設定する

このチュートリアルのパート 1 では、カスタム プロバイダーと連携するように Azure 関数アプリを設定する方法について説明します。

- [Azure カスタム プロバイダー用の Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)

カスタム プロバイダーでは、任意のパブリック URL を使用できます。

## <a name="author-a-restful-endpoint-for-custom-providers"></a>カスタム プロバイダー用の RESTful エンドポイントを作成する

このチュートリアルのパート 2 では、カスタム プロバイダー用の RESTful エンドポイントを作成する方法について説明します。

- [カスタム プロバイダー用の RESTful エンドポイントを作成する](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>カスタム プロバイダーを作成して使用する

このチュートリアルのパート 3 では、カスタム プロバイダーを作成して、そのカスタム アクションとカスタム リソースを使用する方法について説明します。

- [カスタム プロバイダーを作成して使用する](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カスタム プロバイダーとその作成方法について学習しました。 次のチュートリアルに進む場合は、「[チュートリアル: Azure カスタム プロバイダー用の Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)」を参照してください。

リファレンスやクイックスタートを探している場合に役に立つリンクをいくつか次に示します。

- [クイック スタート: Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法: カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
