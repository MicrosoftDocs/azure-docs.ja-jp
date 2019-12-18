---
title: ポリシーを使用して、 Azure マネージアプリケーションのアソシエーションをデプロイする
description: Azure Policy サービスを使用して、マネージアプリケーションのアソシエーションをデプロイする方法について学びます。
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: e3dda343c1d5664e188b434c54e4c288b515d420
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932200"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Azure Policy を使用して、 Azure マネージアプリケーションのアソシエーションをデプロイする

Azure ポリシーは、リソースをマネージアプリケーションに関連付けするためのアソシエーションをデプロイするために使用できます。 この記事では、関係をデプロイする組み込みのポリシーと、そのポリシーを使用する方法について説明します。

## <a name="built-in-policy-to-deploy-associations"></a>関係を展開する組み込みポリシー

マネージアプリケーションの関係のデプロイは、リソースをマネージアプリケーションに関連付けるためにアソシエーションをデプロイするために使用できる組み込みのポリシーです。 このポリシーでは、次の3つのパラメーターを受け入れます：

- マネージド アプリケーション ID - この ID は、リソースの関連付けが必要なマネージド アプリケーションのリソース ID です。
- 関連付けるリソースの種類 - これらのリソースの種類は、マネージド アプリケーションに関連付けられるリソースの種類の一覧です。 同じポリシーを使用して、複数のリソースの種類を、マネージアプリケーションに関連付けることができます。
- アソシエーション名のプレフィックス - この文字列は、作成されるアソシエーションリソースの名前に追加されるプレフィックスです。 既定値は「DeployedByPolicy」です。

このポリシーでは、DeployIfNotExists を使用して評価をします。 リソースプロバイダーが、選択したリソースの種類のリソースの作成または更新要求を処理し、評価が正常に状態コードを返した後に実行されます。 その後、アソシエーションリソースが、テンプレートデプロイを使用してデプロイされます。
アソシエーションの詳細については、「[Azure カスタムプロバイダーのリソースオンボード](./concepts-custom-providers-resourceonboarding.md) 」を参照

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>デプロイ アソシエーションの組み込みポリシーを使用する方法 

### <a name="prerequisites"></a>前提条件
マネージアプリケーションが、操作を実行するために、サブスクリプションへのアクセス許可が必要な場合、アソシエーションリソースのポリシーのデプロイは、アクセス許可を付与しなければ機能しません。

### <a name="policy-assignment"></a>ポリシー割り当て
組み込みポリシーを使用するには、ポリシー割り当てを作成し、マネージド アプリケーション ポリシーのディプロイ アソシエーションを割り当てます。 ポリシーが正常に割り当てられると、ポリシーは準拠していないリソースを特定し、それらのリソースのアソシエーションをデプロイします。

![組み込みポリシー](media/builtin-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>ヘルプの表示

Azure カスタム リソース プロバイダーの開発に関する質問がある場合、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) で確認してみてください。 同様の質問が既に回答されていることもあるため、投稿する前にまず確認してください。 ```azure-custom-providers``` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次の手順

この記事では、組み込みのポリシーを使用してアソシエーションをディプロイする方法について学びます。 詳細については、次の記事を参照してください。

- [概念: Azure カスタム プロバイダー リソースのオンボード](./concepts-custom-providers-resourceonboarding.md)
- [チュートリアル:カスタム プロバイダーを使用したリソースのオンボード](./tutorial-custom-providers-resource-onboarding.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [クイック スタート:カスタム リソース プロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法: カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
