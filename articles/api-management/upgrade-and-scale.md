---
title: Azure API Management インスタンスのアップグレードとスケーリングを行う | Microsoft Docs
description: このトピックでは、Azure API Management インスタンスのアップグレードとスケーリングを行う方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018235"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management インスタンスのアップグレードとスケーリングを行う  

Azure API Management (APIM) インスタンスは、ユニットを追加するか削除することでスケーリングできます。 **ユニット**は専用の Azure リソースで構成され、1 か月あたりの API 呼び出しの数として表される特定の耐荷容量があります。 この数値は呼び出しの制限を表しているのではなく、大まかな容量計画を行うための最大スループット値です。 実際のスループットと待ち時間は、コンカレント接続の数とレート、構成されたポリシーの種類と数、要求のサイズと応答のサイズ、バックエンドの待ち時間などの多くの要因によって、大幅に異なります。

各ユニットの容量と価格は、ユニットが存在する**レベル**によって決まります。 4 つのレベル、すなわち、**Developer**、**Basic**、**Standard**、**Premium** から選択できます。 レベル内でサービスの容量を増やす必要がある場合は、ユニットを追加する必要があります。 現在選択されている APIM インスタンスのレベルではそれ以上ユニットを追加できない場合は、上位のレベルにアップグレードする必要があります。

各ユニットの価格と利用可能な機能 (複数リージョンへのデプロイなど) は、APIM インスタンス用に選択したレベルによって異なります。 [価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事で、ユニットあたりの価格と各レベルで利用できる機能が説明されています。 

>[!NOTE]
>[価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事では、各レベルにおけるユニットの容量の概算の数値が示されています。 正確な数値を取得するには、API の現実的なシナリオを検討する必要があります。 [「Capacity of an Azure API Management instance」](api-management-capacity.md)(Azure API Management インスタンスの容量) の記事を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプションを持っている。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンスを持っている。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

+ [Azure API Management インスタンスの容量](api-management-capacity.md)の概念を理解している。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>アップグレードとスケーリング  

4 つのレベル、すなわち、**Developer**、**Basic**、**Standard**、**Premium** から選択できます。 **Developer** レベルは、サービスを評価するために使用する必要があります。運用環境では使用しないでください。 **Developer** レベルには SLA がなく、このレベルをスケーリング (ユニットの追加/削除) することはできません。 

**Basic**、**Standard**、**Premium** は運用レベルであり、SLA があり、スケーリングできます。 **Basic**  レベルは SLA がある最も安価なレベルで、最大 2 ユニットにスケールアップできます。**Standard** レベルは最大 4 ユニットにスケールアップできます。 **Premium** レベルでは、任意の数のユニットを追加できます。

**Premium** レベルでは、1 つの Azure API Management インスタンスを任意の数の Azure リージョンに配布できます。 Azure API Management サービスを初めて作成すると、インスタンスにはユニットが 1 つだけ含まれ、そのインスタンスは 1 つの Azure リージョンに存在します。 最初のリージョンは、**プライマリ** リージョンとして指定されます。 別のリージョンを簡単に追加できます。 リージョンを追加するときに、割り当てるユニットの数を指定します。 たとえば、**プライマリ** リージョンに １ つのユニットを、別のリージョンに 5 つのユニットを割り当てることができます。 ユニット数は、各リージョンのトラフィックに合わせて調整できます。 詳細については、「[複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)」を参照してください。

レベル間でアップグレードまたはダウングレードを実行できます。 アップグレードまたはダウングレードすると、一部の機能が削除される可能性があることにご注意ください。たとえば、Premium レベルから Standard または Basic レベルにダウングレードすると、VNET や複数リージョンへのデプロイは利用できなくなります。

> [!NOTE]
> アップグレードまたはスケーリング プロセスは、適用されるまでに 15 ～ 45 分かかる場合があります。 終了すると通知されます。

> [!NOTE]
> **従量課金**レベルの API Management サービスは、トラフィックに基づいて自動的にスケールされます。

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Azure ポータルを使用してアップグレードとスケーリングを行う

![Azure portal で APIM をスケーリングする](./media/upgrade-and-scale/portal-scale.png)

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[スケールと料金]** を選択します。
3. 目的のレベルを選択します。
4. 追加する**ユニット**の数を指定します。 スライダーを使用するか、ユニットの数を入力できます。  
    **Premium** レベルを選択した場合は、最初にリージョンを選択する必要があります。
5. **[保存]** をクリックします。

## <a name="downtime-during-scaling-up-and-down"></a>スケールアップおよびスケールダウンの際のダウンタイム
Developer レベルへのスケールダウンまたは Developer レベルからのスケールアップを行うとき、ダウンタイムが発生します。 それ以外の場合、ダウンタイムはありません。 


## <a name="next-steps"></a>次の手順

- [複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)
- [Azure API Management サービス インスタンスを自動的にスケーリングする方法](api-management-howto-autoscale.md)
