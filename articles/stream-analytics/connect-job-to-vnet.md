---
title: Azure Virtual Network (VNET) のリソースへの Stream Analytics ジョブの接続
description: この記事では、Azure Stream Analytics ジョブを VNET 内のリソースに接続する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878241"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Azure Virtual Network (VNet) のリソースへの Stream Analytics ジョブの接続

Stream Analytics ジョブは、Azure の入出力リソースへの送信接続を確立して、リアルタイムでデータを処理し、結果を生成します。 これらの入力リソースと出力リソース (Azure Event Hubs や Azure SQL Database など) は、Azure ファイアウォールまたは Azure Virtual Network (VNet) の内側に配置できます。 Stream Analytics サービスは、ネットワーク ルールに直接含めることができないネットワークから動作します。

ただし、このようなシナリオで、Stream Analytics ジョブを入力リソースと出力リソースに安全に接続する方法が 2 つあります。
* Stream Analytics クラスターのプライベート エンドポイントを使用する。
* マネージド ID の認証モードを、"信頼されたサービスを許可する" ネットワーク設定とともに使用する。

Stream Analytics ジョブは受信接続を受け入れません。

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Stream Analytics クラスターのプライベート エンドポイント。
[Stream Analytics クラスター](./cluster-overview.md)は、Stream Analytics ジョブを実行できる単一テナント専用のコンピューティング クラスターです。 Stream Analytics クラスターにはマネージド プライベート エンドポイントを作成できます。これにより、クラスターで実行されているすべてのジョブで、入力リソースや出力リソースへの安全な送信接続を行うことができます。

Stream Analytics クラスターでのプライベート エンドポイントの作成は、[2 ステップの操作](./private-endpoints.md)です。 このオプションは、中規模から大規模のストリーミング ワークロードに適しています。Stream Analytics クラスターの最小サイズが 36 SU であるためです (ただし、36 SU は、さまざまなサブスクリプションや環境 (開発環境、テスト環境、運用環境など) の異なるジョブで共有できます)。

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>"信頼されたサービスを許可する" 構成でのマネージド ID 認証
一部の Azure サービスには、 **[信頼された Microsoft サービスを許可]** というネットワーク設定があります。この設定を有効にすると、Stream Analytics ジョブは、強力な認証を使用して安全にリソースに接続できます。 このオプションを使用すると、Stream Analytics クラスターやプライベート エンドポイントなしで、入力リソースや出力リソースにジョブを接続できます。 この手法を使用するためのジョブの構成は、次の 2 ステップの操作です。
* Stream Analytics ジョブで入力または出力を構成するときに、マネージド ID 認証モードを使用します。
* Azure ロールをジョブのシステム割り当て済みマネージド ID に割り当てることにより、特定の Stream Analytics ジョブにターゲット リソースへの明示的なアクセス権を付与します。 

**[信頼された Microsoft サービスを許可]** を有効にしても、ジョブへの包括的なアクセス権は付与されません。 これにより、どの Stream Analytics ジョブに、リソースへの安全なアクセスを許可するかを完全に制御できます。 

ジョブは、この手法を使用して、次の Azure サービスに接続できます。
1. [Blob Storage または Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) - ジョブのストレージ アカウント、ストリーミング入力または出力。
2. [Azure Event Hubs](./event-hubs-managed-identity.md) - ジョブのストリーミング入力または出力。

ジョブから他の入力または出力の種類に接続する必要がある場合は、まず Stream Analytics から Event Hubs 出力に書き込み、次に Azure Functions を使用して任意の出力先に書き込むことができます。 VNet またはファイアウォールでセキュリティで保護されている他の出力の種類に Stream Analytics から直接書き込む必要がある場合は、Stream Analytics クラスターでプライベート エンドポイントを使用するしかありません。

## <a name="next-steps"></a>次のステップ

* [Stream Analytics クラスターでプライベート エンドポイントを作成および削除する](./private-endpoints.md)
* [マネージド ID 認証を使用して VNet 内の Event Hubs に接続する](./event-hubs-managed-identity.md)
* [マネージド ID 認証を使用して VNet 内の Blob Storage と ADLS Gen2 に接続する](./blob-output-managed-identity.md)