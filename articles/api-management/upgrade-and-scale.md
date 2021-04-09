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
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127789"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management インスタンスのアップグレードとスケーリングを行う  

ユーザーは、ユニットを追加したり削除したりすることにより、Azure API Management インスタンスをスケーリングできます。 **ユニット** は専用の Azure リソースで構成され、1 か月あたりの API 呼び出しの数として表される特定の耐荷容量があります。 この数値は呼び出しの制限を表しているのではなく、大まかな容量計画を行うための最大スループット値です。 実際のスループットと待ち時間は、コンカレント接続の数とレート、構成されたポリシーの種類と数、要求のサイズと応答のサイズ、バックエンドの待ち時間などの多くの要因によって、大幅に異なります。

各ユニットの容量と価格は、ユニットが存在する **レベル** によって決まります。 4 つのレベル、すなわち、**Developer**、**Basic**、**Standard**、**Premium** から選択できます。 レベル内でサービスの容量を増やす必要がある場合は、ユニットを追加する必要があります。 API Management インスタンスで現在選択されているサービス レベルでユニットのさらなる追加が許可されていない場合は、上位のサービス レベルにアップグレードする必要があります。

各ユニットの価格と使用可能な機能 (複数リージョンへのデプロイなど) は、API Management インスタンス用に選択したサービス レベルによって異なります。 [価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事で、ユニットあたりの価格と各レベルで利用できる機能が説明されています。 

>[!NOTE]
>[価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事では、各レベルにおけるユニットの容量の概算の数値が示されています。 正確な数値を取得するには、API の現実的なシナリオを検討する必要があります。 [「Capacity of an Azure API Management instance」](api-management-capacity.md)(Azure API Management インスタンスの容量) の記事を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプションを持っている。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ API Management インスタンスを持っている。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

+ [Azure API Management インスタンスの容量](api-management-capacity.md)の概念を理解している。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>アップグレードとスケーリング  

4 つのレベル、すなわち、**Developer**、**Basic**、**Standard**、**Premium** から選択できます。 **Developer** レベルは、サービスを評価するために使用する必要があります。運用環境では使用しないでください。 **Developer** レベルには SLA がなく、このレベルをスケーリング (ユニットの追加/削除) することはできません。 

**Basic**、**Standard**、**Premium** は、SLA が含まれており、スケーリングが可能な運用レベルです。 **Basic**  レベルは SLA を含む最も安価なレベルで、最大 2 ユニットにスケールアップできます。**Standard** レベルは最大 4 ユニットにスケールアップできます。 **Premium** レベルでは、任意の数のユニットを追加できます。

**Premium** レベルでは、1 つの Azure API Management インスタンスを任意の数の Azure リージョンに配布できます。 Azure API Management サービスを初めて作成すると、インスタンスにはユニットが 1 つだけ含まれ、そのインスタンスは 1 つの Azure リージョンに存在します。 最初のリージョンは、**プライマリ** リージョンとして指定されます。 別のリージョンを簡単に追加できます。 リージョンを追加するときに、割り当てるユニットの数を指定します。 たとえば、**プライマリ** リージョンに １ つのユニットを、別のリージョンに 5 つのユニットを割り当てることができます。 ユニット数は、各リージョンのトラフィックに合わせて調整できます。 詳細については、「[複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)」を参照してください。

レベル間でアップグレードまたはダウングレードを実行できます。 アップグレードまたはダウングレードすると、一部の機能が削除される可能性があります。たとえば、Premium レベルから Standard または Basic にダウングレードすると、VNET や複数リージョンへのデプロイは利用できなくなります。

> [!NOTE]
> アップグレードまたはスケーリング プロセスは、適用されるまでに 15 ～ 45 分かかる場合があります。 終了すると通知されます。

> [!NOTE]
> **従量課金** レベルの API Management サービスは、トラフィックに基づいて自動的にスケールされます。

## <a name="scale-your-api-management-service"></a>API Management サービスをスケーリングする

![Azure portal で API Management サービスをスケーリングする](./media/upgrade-and-scale/portal-scale.png)

1. [Azure portal](https://portal.azure.com/) で API Management サービスに移動します。
2. メニューから **[場所]** を選択します。
3. スケーリングする場所が含まれている行をクリックします。
4. スライダーを使用するか数値を入力して、**ユニット** の新しい数を指定します。
5. **[Apply]** をクリックします。

## <a name="change-your-api-management-service-tier"></a>API Management サービス レベルを変更する

1. [Azure portal](https://portal.azure.com/) で API Management サービスに移動します。
2. メニューで **[価格レベル]** をクリックします。
3. ドロップダウンから、目的のサービス レベルを選択します。 スライダーを使用して、変更後の API Management サービスのスケールを指定します。
4. **[保存]** をクリックします。

## <a name="downtime-during-scaling-up-and-down"></a>スケールアップおよびスケールダウンの際のダウンタイム
Developer レベルへのスケールダウンまたは Developer レベルからのスケールアップを行うとき、ダウンタイムが発生します。 それ以外の場合、ダウンタイムはありません。 

## <a name="compute-isolation"></a>コンピューティングの分離
セキュリティ要件に [コンピューティングの分離](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)が含まれている場合は、**Isolated** 価格レベルを使用できます。 このレベルによって、API Management サービス インスタンスのコンピューティング リソースが物理ホスト全体を消費し、サポートする必要のある分離レベル (たとえば、米国国防総省影響レベル 5 (IL5) のワークロード) が提供されます。 Isolated レベルにアクセスできるようにするには、[サポート チケットを作成](../azure-portal/supportability/how-to-create-azure-support-request.md)してください。 



## <a name="next-steps"></a>次のステップ

- [複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)
- [Azure API Management サービス インスタンスを自動的にスケーリングする方法](api-management-howto-autoscale.md)
- [API Management のコストを計画および管理する](plan-manage-costs.md)
- [API Management の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)