---
title: ネットワーク ルーティング設定
titleSuffix: Azure Storage
description: ネットワークのルーティング優先設定を使用すると、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定できます。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: bf2270fe6f71dfe5be31db8e82f6c44696f28074
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601493"
---
# <a name="network-routing-preference-for-azure-storage"></a>Azure Storage のネットワーク ルーティング優先設定

Azure ストレージ アカウントに対してネットワークの[ルーティング優先設定](../../virtual-network/routing-preference-overview.md)を構成し、インターネット経由でクライアントからアカウントにネットワーク トラフィックをルーティングする方法を指定することができます。 既定では、インターネットからのトラフィックは、[Microsoft グローバル ネットワーク](../../networking/microsoft-global-network.md)経由で、ストレージ アカウントのパブリック エンドポイントにルーティングされます。 Azure Storage には、ストレージ アカウントへのトラフィックのルーティング方法を構成するための追加オプションが用意されています。

ルーティング優先設定の構成を使用すると、Premium ネットワークのパフォーマンスやコストに応じて、トラフィックを柔軟に最適化できます。 ルーティング優先設定を構成するときは、ストレージ アカウントのパブリック エンドポイントにトラフィックが送信される既定の方法を指定します。 また、ストレージ アカウント用にルート固有のエンドポイントを公開することもできます。

> [!NOTE]
> この機能は、premium パフォーマンス レベルまたはゾーン冗長ストレージ (ZRS) を使用するように構成されているストレージ アカウントではサポートされていません。

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft グローバル ネットワークとインターネット ルーティングの比較

既定では、Azure 環境の外部のクライアントは、Microsoft グローバル ネットワーク経由でストレージ アカウントにアクセスします。 Microsoft グローバル ネットワークは、高い信頼性で Premium ネットワークのパフォーマンスを実現するため、低待機時間のパス選択用に最適化されています。 受信トラフィックと送信トラフィックのどちらも、クライアントに最も近いポイント オブ プレゼンス (POP) を通してルーティングされます。 この既定のルーティング構成により、ストレージ アカウントとの間でやり取りされるトラフィックは、そのパスの大部分を Microsoft グローバル ネットワーク経由で送信されるため、ネットワークのパフォーマンスが最大になります。

ストレージ アカウントのルーティング構成を変更し、Azure 環境の外部にあるクライアントとの間でやり取りされる受信と送信両方のトラフィックが、ストレージ アカウントに最も近い POP を通してルーティングされるようにすることができます。 このルートにより、Microsoft グローバル ネットワーク上のトラフィックの移動距離が最小限になり、最も早い機会に通過 ISP に渡されます。 このルーティング構成を利用することで、ネットワークのコストを削減できます。

次の図では、各ルーティング優先設定についてクライアントとストレージ アカウントの間のトラフィック フローを示します。

![Azure Storage のルーティング オプションの概要](media/network-routing-preference/routing-options-diagram.png)

Azure でのルーティング優先設定の詳細については、「[ルーティング優先設定とは](../../virtual-network/routing-preference-overview.md)」を参照してください。

## <a name="routing-configuration"></a>ルーティング構成

ルーティング優先設定とルート固有のエンドポイントを構成する手順の詳細については、「[Azure Storage に対してネットワークのルーティング優先設定を構成する](configure-network-routing-preference.md)」を参照してください。

ストレージ アカウントのパブリック エンドポイントに対する既定のルーティング優先設定として、Microsoft グローバル ネットワーク ルーティングとインターネット ルーティングのどちらかを選択できます。 既定のルーティング優先設定は、Azure の外部にあるクライアントからのすべてのトラフィックに適用され、Azure Data Lake Storage Gen2、Blob Storage、Azure Files、および静的 Web サイトのエンドポイントに影響します。 ルーティング優先設定の構成は、Azure キューまたは Azure テーブルではサポートされていません。

また、ストレージ アカウント用にルート固有のエンドポイントを公開することもできます。 ルート固有のエンドポイントを公開すると、Azure Storage により、目的のパス経由でトラフィックをルーティングするストレージ アカウントに対して、新しいパブリック エンドポイントが作成されます。 この柔軟性により、既定のルーティング優先設定を変更することなく、特定のルートを通してストレージ アカウントにトラフィックを転送できます。

たとえば、"StorageAccountA" に対してインターネット ルート固有のエンドポイントを公開すると、ストレージ アカウントに対して次のエンドポイントが公開されます。

| ストレージ サービス        | ルート固有のエンドポイント                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| File service           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| 静的な Web サイト        | `StorageAccountA-internetrouting.web.core.windows.net`   |

読み取りアクセス geo 冗長ストレージ (RA-GRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) のストレージ アカウントがある場合は、ルート固有のエンドポイントを公開すると、読み取りアクセス用に、セカンダリ リージョンに対応するエンドポイントが自動的に作成されます。

| ストレージ サービス        | ルート固有の読み取り専用セカンダリ エンドポイント                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| File service           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| 静的な Web サイト        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

公開されたルート固有のエンドポイントの接続文字列は、[Azure portal](https://portal.azure.com) でコピーできます。 これらの接続文字列は、既存のすべての Azure Storage SDK と API で共有キー承認用に使用できます。

## <a name="regional-availability"></a>リージョン別の提供状況

Azure Storage のルーティング優先設定は、次のリージョンで利用できます。

- 米国中部 
- 米国中部 EUAP
- 米国東部 
- 米国東部 2
- 米国東部 2 
- 米国東部 2 EUAP
- 米国中南部
- 米国中西部
- 米国西部 
- 米国西部 2 
- フランス中部 
- フランス南部 
- ドイツ北部 
- ドイツ中西部 
- 米国中北部
- 北ヨーロッパ 
- ノルウェー東部 
- スイス北部
- スイス西部
- 英国南部 
- 英国西部 
- 西ヨーロッパ 
- アラブ首長国連邦中部
- 東アジア 
- 東南アジア 
- 東日本 
- 西日本 
- インド西部
- オーストラリア東部 
- オーストラリア南東部 

Azure Storage のルーティング優先設定には、次の既知の問題が影響します。

- Microsoft グローバル ネットワーク用のルート固有エンドポイントに対するアクセス要求は、HTTP エラー 404 またはそれと同等のエラーで失敗します。 Microsoft グローバル ネットワーク経由のルーティングは、パブリック エンドポイントに対する既定のルーティング優先設定として設定されている場合、想定どおりに動作します。

## <a name="pricing-and-billing"></a>価格と課金

価格と課金の詳細については、「[ルーティング優先設定とは](../../virtual-network/routing-preference-overview.md#pricing)」の「**価格**」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [ルーティング設定とは](../../virtual-network/routing-preference-overview.md)
- [ネットワーク ルーティング設定の構成](configure-network-routing-preference.md)
- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)
