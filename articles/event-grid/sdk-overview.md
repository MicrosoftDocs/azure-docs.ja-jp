---
title: Azure Event Grid SDK
description: Azure Event Grid の SDK について説明します。 これらの SDK は、管理、発行、および使用の機能を提供します。
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: aa53aaa89a703bb88df6611cdb0dd38341248a55
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955926"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>管理と発行のための Event Grid SDK

Event Grid の SDK を使うと、プログラムでリソースの管理やイベントの投稿を行うことができます。

## <a name="management-sdks"></a>管理 SDK

管理 SDK を使うと、Event Grid のトピックとサブスクリプションを作成、更新、および削除できます。 現時点では、次の SDK を利用できます。

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [[Node]](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>データ プレーン SDK

データ プレーン SDK を使うと、トピックにイベントを投稿でき、認証、イベントの形成、指定されたエンドポイントへの非同期の投稿が行われます。 これを使ってファースト パーティのイベントを使用することもできます。 現時点では、次の SDK を利用できます。

| プログラミング言語 | SDK | 
| -------------------- | ---------- | 
| .NET | 安定版 SDK:[Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>プレビュー版 SDK:[Azure.Messaging.EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | 安定版 SDK: [azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>プレビュー版 SDK: [azure-messaging-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) ( **[Release history]\(リリース履歴\)** ページの最新の安定版とプレリリース版を参照してください) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) ( **[Versions]\(バージョン\)** タブに切り替えて、最新の安定版とベータ版のパッケージを確認してください)。 | 
| Go | [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>次の手順

* アプリケーションの例については、[Event Grid のコード サンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)に関するページを参照してください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure CLI での Event Grid コマンドについては、[Azure CLI](/cli/azure/eventgrid) のページをご覧ください。
* PowerShell での Event Grid コマンドについては、[PowerShell](/powershell/module/az.eventgrid) のページをご覧ください。
