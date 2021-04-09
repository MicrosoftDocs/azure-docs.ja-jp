---
title: Azure Event Grid のイベント ドメインのユース ケース
description: この記事では、Azure Event Grid でイベント ドメインを使用するいくつかのユースケースについて説明します。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204240"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Azure Event Grid のイベント ドメインのユース ケース
この記事では、Azure Event Grid でイベント ドメインを使用するいくつかのユースケースについて説明します。 

## <a name="use-case-1"></a>ユース ケース 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>ユース ケース 2
システム トピックを使用する場合、イベント サブスクリプションの制限値は 500 です。 システム トピックに 500 を超えるイベント サブスクリプションが必要な場合は、ドメインを使用します。 

たとえば、Azure Blob Storage のシステム トピックを作成し、そのトピックに対して 500 を超えるサブスクリプションを作成する必要があっても、制限値 (トピックあたりのサブスクリプションは 500) が原因で実現できません。 この場合は、イベント ドメインを使用する次のソリューションを検討してください。 

1. 最大 100,000 のトピックをサポートし、各ドメインのトピックに 500 のイベント サブスクリプションを含めることができるドメインを作成します。 このモデルでは、500 * 100,000 のイベント サブスクリプションが可能です。 
1. Azure Storage システム トピックに対して Azure 関数サブスクリプションを作成します。 関数は、Azure Storage からイベントを受け取ると、それを強化して適切なドメイン トピックに発行できます。 たとえば、関数は BLOB ファイル名を解析してターゲット ドメインのトピックを確認し、イベントをドメイン トピックに発行できます。 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid ドメイン - ユース ケース 2":::


## <a name="next-steps"></a>次のステップ
イベント ドメインの設定、トピックの作成、イベント サブスクリプションの作成、イベントの発行の詳細については、[イベント ドメインの管理](./how-to-event-domains.md)に関するページを参照してください。
