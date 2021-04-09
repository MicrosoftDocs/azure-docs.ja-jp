---
title: メディア占有ユニット (MRU) の移行ガイダンス
description: この記事では、Azure Media Services V2 から V3 への移行に役立つ MRU シナリオ ベースのガイダンスを示します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 5ca01bcea348b866c0f40f82ebe90dc31d032739
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927069"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>メディア占有ユニット (MRU) シナリオベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、Azure Media Services V2 から V3 への移行に役立つ MRU シナリオ ベースのガイダンスを示します。

- Azure portal で作成された新しい V3 アカウントの場合、または V3 API の 2020-05-01 バージョンの場合は、メディア占有ユニット (MRU) を設定する必要がなくなりました。 これで、負荷に基づいて、システムが自動的にスケールアップおよびスケールダウンされます。
- API の 2020-05-01 バージョンより前に作成された V3 または V2 アカウントがある場合でも、メディア占有ユニットを使用してジョブの同時実行性とパフォーマンスを制御できます。 詳細については、メディア処理のスケール設定に関するページを参照してください。 MRU の管理には、CLI 2.0 for Media Services V3、または Azure portal を使用できます。  
- Azure portal で MRU を管理するオプションが表示されない場合は、2020-05-01 API 以降で作成されたアカウントを実行しています。
- MRU の種類を S3 に設定することに慣れている場合は、パフォーマンスが向上するか、変わりません。
- 既存の V2 のお客様は、移行が完了する前に、既存のアプリケーションをサポートするために新しい V2 アカウントを作成する必要があります。 
- 完全に非推奨とされているインデクサー V1 またはその他のメディア プロセッサは、再度有効にする必要がある場合があります。 

MRU の詳細については、「[メディア占有ユニット](concept-media-reserved-units.md)」と「[メディア占有ユニットをスケーリングする方法](media-reserved-units-cli-how-to.md)」を参照してください。

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU の概念、チュートリアル、および方法に関するガイド

### <a name="concepts"></a>概念

[メディア占有ユニット](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>ハウツー ガイド

[メディア占有ユニットをスケーリングする方法](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
