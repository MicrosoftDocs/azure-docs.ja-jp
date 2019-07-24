---
title: クラスター構成へのアクセスの変更 - Azure HDInsight
description: 機密性の高いクラスター構成のフィールドへのアクセスに対する変更について説明します。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 85a6737ee111975d4c7ecf078673280127bfd0fd
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610172"
---
# <a name="changes-to-cluster-configuration-access"></a>クラスター構成へのアクセスの変更

Azure HDInsight SDK の最新リリースでは、機密情報を取得するためのいっそうきめ細かなロールベースのアクセスのサポートに対して、いくつかの重要な変更が導入されています。 これらの変化の一部として、影響を受ける方法のいずれかを使っている場合は、いくつかの**アクションが必要になる場合があります**。

## <a name="sdk-for-net-500"></a>SDK for .NET 5.0.0

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ConfigurationOperationsExtensions.List` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、`ClusterOperationsExtensions.GetGatewaySettings` を使います。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) は非推奨になり、`ClusterOperationsExtensions.UpdateGatewaySettings` に置き換えられています。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) と [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) は非推奨となりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。