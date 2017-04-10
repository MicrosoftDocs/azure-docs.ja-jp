---
title: "Azure Scheduler とは | Microsoft Docs"
description: "Azure Scheduler では、クラウドで実行するアクションを宣言的に記述することができます。 その後スケジュールを設定し、アクションを自動的に実行します。"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38


---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler とは
Azure Scheduler では、クラウドで実行するアクションを宣言的に記述することができます。 その後スケジュールを設定し、アクションを自動的に実行します。  Scheduler では、[Azure Portal](scheduler-get-started-portal.md)、コード、[REST API](https://msdn.microsoft.com/library/mt629143.aspx)、または Azure PowerShell を使用してこれを実行します。

Scheduler には、スケジュールされた作業の作成、保守、呼び出しの機能があります。  Scheduler が、ワークロードをホストしたり、コードを実行したりすることはありません。 Scheduler は他の場所 (Azure、オンプレミス、または別のプロバイダー) でホストされるコードを *呼び出す* だけです。 呼び出しは、HTTP、HTTPS、ストレージ キュー、Service Bus キュー、Service Bus トピックを介して行われます。

Scheduler では [ジョブ](scheduler-concepts-terms.md)をスケジュールし、レビュー可能なジョブ実行結果の履歴を保持し、ワークロードを確実かつ高い信頼度で実行するようにスケジュールします。 Azure WebJobs (Azure App Service の Web Apps 機能の一部) およびその他の Azure スケジュール機能では、Scheduler をバック グラウンドで使用します。 [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) では、こうしたアクションにおける通信を管理します。 このため、Scheduler は [複雑なスケジュールおよび高度な定期実行](scheduler-advanced-complexity.md) に容易に対応できます。

Scheduler の使用に適しているシナリオがいくつかあります。 次に例を示します。

* *定期的なアプリケーション アクション:* Twitter からのデータを定期的に収集し、フィードに集めます。
* *日々のメンテナンス:* 日々のログの排除、バックアップの実行、その他のメンテナンス タスク。 たとえば、管理者は、午前 1 時にデータベースをバックアップすることができます  (今後 9 か月間、毎日)。

Scheduler では、ジョブおよび [ジョブ コレクション](scheduler-concepts-terms.md) の作成、更新、削除、表示、および管理を、プログラム、スクリプト、およびポータルを使用して実行することができます。

## <a name="see-also"></a>関連項目
 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)




<!--HONumber=Dec16_HO2-->


