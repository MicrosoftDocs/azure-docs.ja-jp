---
title: "BizTalk Services の管理および開発タスク一覧 | Microsoft Docs"
description: "Azure BizTalk Services のデプロイ向けの計画およびジョブ支援"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1013419f9283c3e81a6a6e8ed56a34d6ee5937ea


---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>BizTalk Services の管理および開発タスク一覧
## <a name="getting-started"></a>Getting Started (概要)
Microsoft Azure BizTalk Services を使用する場合、考慮する必要があるオンプレミスおよびクラウドベースのコンポーネントがいくつかあります。 まずはじめに、次のプロセス フローを考慮します。  

| 手順 | 責任者 | タスク | 関連リンク |
| --- | --- | --- | --- |
| 1. |管理者 |Microsoft アカウントまたは組織アカウントを使用して、Microsoft Azure のサブスクリプションを作成します |[Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885) |
| 2. |管理者 |BizTalk サービスの作成またはプロビジョニング |[Azure クラシック ポータルを使用して BizTalk サービスを作成する](http://go.microsoft.com/fwlink/p/?LinkID=302280) |
| 3. |管理者 |個人または会社の BizTalk Services のデプロイを登録します。 |[BizTalk Services ポータルでの BizTalk サービス デプロイの登録と更新](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |管理者 |アプリケーションが BizTalk Adapter サービスを使用してオンプレミス基幹業務 (LOB) システムに接続しているか、キューまたはトピック ターゲットを使用している場合に適用されます。  Azure Service Bus 名前空間を作成します。 この名前空間、Service Bus の発行者名、および Service Bus の発行者キー値を開発者に伝えます。 |「[方法: Service Bus サービス名前空間を作成または変更する](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)」および「[発行者名および発行者キー値の取得](biztalk-issuer-name-issuer-key.md)」 |
| 5. |Developer |SDK をインストールし、Visual Studio で BizTalk サービス プロジェクト を作成します。 |「[Azure BizTalk Services SDK のインストール](https://msdn.microsoft.com/library/azure/hh689760.aspx)」および「[Azure の多機能メッセージング エンドポイントの作成](https://msdn.microsoft.com/library/azure/hh689766.aspx)」 |
| 6. |Developer |Azure でホストされている BizTalk サービスに BizTalk サービス プロジェクトをデプロイします。 |[BizTalk Services プロジェクトのデプロイおよび更新](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |管理者 |EDI を使用している場合に適用されます。  パートナーを追加し、Microsoft Azure BizTalk Services ポータルでアグリーメントを作成できます。 アグリーメントを作成するときに、アグリーメントの設定に開発者が作成したブリッジまたは変換を追加することができます。 |[BizTalk Services ポータルでの EDI、AS2、および EDIFACT の構成](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |管理者 |Azure クラシック ポータルを使用して、パフォーマンス メトリックなど、BizTalk サービスの状態を監視します。 |[BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |管理者 |Microsoft Azure BizTalk Services ポータルを使用して、BizTalk Services で使用されているアーティファクトを管理し、ブリッジ ファイルで処理されるメッセージを追跡します。 |[BizTalk Services ポータルを使用する](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |管理者 |バックアップ計画を作成して BizTalk サービスをバックアップします。 |[BizTalk Services のビジネス継続性と障害復旧](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>次のステップ
[チュートリアルとサンプル](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Visual Studio でのプロジェクトの作成](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Azure BizTalk Services SDK のインストール](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>概念
[Visual Studio でのプロジェクトの作成](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
「[EDI、AS2、および EDIFACT メッセージング (企業間)](https://msdn.microsoft.com/library/azure/hh689898.aspx)」  

## <a name="other-resources"></a>その他のリソース
[ソース、宛先、およびブリッジ メッセージング エンドポイントの追加](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[メッセージ マップおよび変換の学習および作成](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[BizTalk アダプター サービス (BAS) の使用](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)




<!--HONumber=Nov16_HO3-->


