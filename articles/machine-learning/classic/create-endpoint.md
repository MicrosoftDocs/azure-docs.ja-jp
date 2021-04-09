---
title: 'ML Studio (classic): Web サービス エンドポイントを作成する - Azure'
description: Azure Machine Learning Studio (クラシック) で Web サービスのエンドポイントを作成します。 Web サービスの各エンドポイントは、個別にアドレス指定、スロットル、および管理されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 36eb6a67aa2a8da80894c27937994afd4f263ecd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517112"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>デプロイされた Azure Machine Learning Studio (クラシック) Web サービス用のエンドポイントを作成する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> このトピックでは、**従来** の Machine Learning Web サービスに適用できる手法について説明します。

Web サービスがデプロイされた後に、そのサービスに対して既定のエンドポイントが作成されます。 既定のエンドポイントは、その API キーを使用して呼び出すことができます。 Web サービス ポータルから、独自のキーでさらにエンドポイントを追加できます。
Web サービスの各エンドポイントは、個別にアドレス指定、スロットル、および管理されます。 各エンドポイントは一意の URL と承認キーであり、お客様に配布することができます。

## <a name="add-endpoints-to-a-web-service"></a>Web サービスにエンドポイントを追加する

Azure Machine Learning Web サービス ポータルを使用して、Web サービスにエンドポイントを追加できます。 エンドポイントが作成されたら、同期 API、バッチ API、および Excel のワークシートによってそれを使用できます。

> [!NOTE]
> Web サービスにエンドポイントを追加した場合、既定のエンドポイントを削除することはできません。

1. Machine Learning Studio (クラシック) の左側のナビゲーション列で [Web サービス] をクリックします。
2. Web サービス ダッシュボードの下部で、 **[Manage endpoints]\(エンドポイントの管理\)** をクリックします。 Azure Machine Learning Web サービス ポータルが Web サービスのエンドポイント ページに表示されます。
3. **[新規作成]** をクリックします。
4. 新しいエンドポイントの名前と説明を入力します。 エンドポイントの名前は、長さが 24 文字以内で、小文字のアルファベットまたは数字で構成されている必要があります。 ログ レベルとサンプル データが有効になっているかどうかを選択します。 詳細については、「 [Machine Learning Web サービスのログ記録の有効化](web-services-logging.md)」を参照してください。

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> 新しいエンドポイントを追加して Web サービスをスケーリングする

既定では、発行される Web サービスはそれぞれ、20 件の同時実行要求をサポートするように構成されますが、200 件までの同時実行要求をサポートするように構成できます。 Azure Machine Learning Studio (クラシック) では、Web サービスで最大限のパフォーマンスを実現するために、設定が自動的に最適化され、ポータルの値は無視されます。

同時呼び出しの最大数 200 件でサポートされる内容を上回る負荷の API を呼び出す場合は、同じ Web サービスに複数のエンドポイントを作成する必要があります。 その後、それら全体に負荷を無作為に分配できます。

Web サービスのスケーリングは一般的なタスクです。 スケーリングの理由としてあげられるのは、200 件を越える同時要求のサポート、複数のエンドポイントによる可用性の向上、または Web サービス用の個別エンドポイントの提供です。 スケールを大きくするには、次の図に示すように [Azure Machine Learning Web サービス](https://services.azureml.net/) ポータルから同じ Web サービスに追加のエンドポイントを追加します。

コンカレンシーの数を増やす場合、これに相当する高いレートで API を呼び出さないと悪影響が出ることがありますのでご注意ください。 高い負荷で構成された API に比較的低い負荷を割り当てた場合、不規則なタイムアウトや待機時間の急増が発生することがあります。

同期 API は通常、短い待機時間が望まれる状況で使用されます。 ここでの待機時間とは、API が 1 つの要求を完了するのにかかる時間を指し、ネットワークの遅延に影響しないものです。 たとえば待機時間が 50 ms の API があるとします。 スロットル レベル "高" で同時呼び出しの最大数が 20 件のときに使用可能な容量をすべて使用するには、この API を 1 秒あたり 20 × 1000 / 50 = 400 回呼び出す必要があります。 さらに増やして、同時呼び出しの最大数 200 件にすると、API を 1 秒あたり 4,000 回呼び出すことができ、この場合の待機時間は 50 ms と想定されます。

## <a name="next-steps"></a>次のステップ

[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)