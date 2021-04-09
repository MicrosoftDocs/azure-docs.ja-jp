---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する - パイプライン エンドポイントの使用'
description: Azure Machine Learning でクライアント アプリケーションとパイプライン エンドポイントを統合します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565654"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>クライアント アプリケーションからパイプライン エンドポイントを使用する

この記事では、クライアント アプリケーションと Azure Machine Learning エンドポイントを統合する方法について説明します。 アプリケーション コードの記述の詳細については、[Azure Machine Learning エンドポイントの使用](../how-to-consume-web-service.md)方法に関するページを参照してください。

この記事は、スタジオ (クラシック) から Azure Machine Learning への移行シリーズの一部です。 Azure Machine Learning への移行の詳細については、[移行の概要に関する記事](migrate-overview.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成](../how-to-manage-workspace.md#create-a-workspace)します。
- [Azure Machine Learning のリアルタイム エンドポイントまたはパイプライン エンドポイント](migrate-rebuild-web-service.md)。


## <a name="consume-a-real-time-endpoint"></a>リアルタイム エンドポイントを使用する 

モデルを **リアルタイム エンドポイント** としてデプロイした場合、その REST エンドポイントと、C#、Python、R で事前生成された使用コードがあります。

1. Azure Machine Learning スタジオ ([ml.azure.com](https://ml.azure.com)) に移動します。
1. **[エンドポイント]** タブに移動します。
1. リアルタイム エンドポイントを選択します。
1. **[使用]** を選択します。

> [!NOTE]
> エンドポイントの Swagger 仕様は、 **[詳細]** タブにもあります。Swagger の定義を使用し、エンドポイント スキーマを理解します。 Swagger 定義の詳細については、[Swagger 公式ドキュメント](https://swagger.io/docs/specification/2-0/what-is-swagger/)を参照してください。


## <a name="consume-a-pipeline-endpoint"></a>パイプライン エンドポイントを使用する

パイプライン エンドポイントは、2 つの方法で使用できます。

- REST API 呼び出し
- Azure Data Factory との統合

### <a name="use-rest-api-calls"></a>REST API 呼び出しの使用

クライアント アプリケーションから REST エンドポイントを呼び出します。 エンドポイントの Swagger 仕様を使用し、そのスキーマを理解できます。

1. Azure Machine Learning スタジオ ([ml.azure.com](https://ml.azure.com)) に移動します。
1. **[エンドポイント]** タブに移動します。
1. **[Pipeline endpoints]\(パイプライン エンドポイント\)** を選択します。
1. パイプライン エンドポイントを選択します。
1. **[Pipeline endpoint overview]\(パイプライン エンドポイントの概要\)** ウィンドウで、 **[REST エンドポイント ドキュメント]** の下にあるリンクを選択します。

### <a name="use-azure-data-factory"></a>Azure Data Factory を使用する

Azure Machine Learning パイプラインを、Azure Data Factory パイプラインのステップとして呼び出すことができます。 詳細については、「[Azure Machine Learning パイプラインを Azure Data Factory で実行する](../../data-factory/transform-data-machine-learning-service.md)」を参照してください。


## <a name="next-steps"></a>次の手順

この記事では、パイプライン エンドポイントのスキーマとサンプル コードを見つける方法について説明しました。 クライアント アプリケーションからエンドポイントを使用する方法の詳細については、[Azure Machine Learning エンドポイントの使用](../how-to-consume-web-service.md)方法に関するページを参照してください。

Azure Machine Learning の移行に関するシリーズの残りの記事を参照してください。 
1. [移行の概要](migrate-overview.md)に関するドキュメントを参照してください。
1. [データセットを移行します](migrate-register-dataset.md)。
1. [スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。
1. [スタジオ (クラシック) の Web サービスを再構築します](migrate-rebuild-web-service.md)。
1. **Azure Machine Learning の Web サービスをクライアント アプリと統合します**。
1. [R スクリプトの実行を移行します](migrate-execute-r-script.md)。