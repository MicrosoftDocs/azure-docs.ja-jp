---
title: Service Health の概要 | Microsoft Docs
description: お使いの Azure アプリが現在および将来の Azure サービスの問題やメンテナンスの影響をどのように受けるかに関するカスタマイズした情報。
services: service-health
author: stephbaron
ms.author: stbaron
documentationcenter: service-health
ms.service: service-health
ms.topic: article
ms.workload: Supportability
ms.date: 03/27/2018
ms.openlocfilehash: 465e8751d02692648234a7a90b84b68f41522cb2
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328267"
---
# <a name="service-health"></a>サービス正常性
Service Health は、ユーザーが使用しているリージョン内の Azure サービスの正常性を追跡するカスタマイズ可能なダッシュボードを提供します。 このダッシュボードでは、進行中のサービスの問題、次に予定されている定期的なメンテナンス、関連する正常性の勧告などのアクティブなイベントを追跡できます。 イベントが非アクティブになると、それらは正常性の履歴に最大 90 日間保存されます。 最後に、Service Health のダッシュボードを使用して、サービスの問題が影響を与えているときにユーザーに事前に通知するサービス正常性アラートを作成および管理できます。

## <a name="service-health-events"></a>Service Health のイベント
Service Health はお使いのリソースに及ぼす可能性のある次の 3 つの種類の正常性に関するイベントを追跡します。
1. **サービスの問題** - ユーザーに今すぐ影響を及ぼす Azure サービスの問題。 
2. **定期的なメンテナンス** - お使いのサービスの可用性に将来影響を及ぼす可能性のある今後のメンテナンス。  
3. **正常性に関する勧告** - ユーザーが注目する必要のある Azure サービスの変化。 Azure の機能が非推奨となるタイミングや、使用量のクォータを超えた場合に関する例も含まれます。

> [!NOTE]
> Service Health イベントを表示するには、ユーザーがサブスクリプションに閲覧者ロールを持っている必要があります。

## <a name="get-started-with-service-health"></a>Service Health の使用
Service Health ダッシュボードを起動するには、ポータルのダッシュボードにある [Service Health] タイルを選択します。 過去にタイルを削除した、またはダッシュボードをカスタマイズしている場合は、[その他のサービス] \(ダッシュボードの右下) で Service Health サービスを検索してください。

![Service Health の使用](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>お使いのサービスに現在影響を及ぼしている問題を確認する
**[サービスに関する問題]** には、お使いのリソースに影響を及ぼしている継続中の Azure サービスの問題が表示されます。 問題がいつから発生しているか、およびどのサービスやリージョンが影響を受けているかを確認できます。 また、問題を解決するための Azure の対策に関する最新の更新情報を確認できます。 

![サービスの問題を管理する](./media/service-health-overview/azure-service-health-overview-2.png)

問題の影響を受けている可能性のある具体的なリソースの一覧を確認するには、**[可能性のある影響]** タブを選択します。 これらのリソースの CSV 一覧をダウンロードしてチームと共有できます。

![サービスの問題を管理する - 影響](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>リンクやダウンロード可能な説明を取得する 
問題のリンクを取得して、問題管理システムで使用できます。 PDF や CSV ファイル (ある場合) をダウンロードして、Azure Portal にアクセスできないユーザーと共有できます。   

![サービスの問題を管理する - 問題管理](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>マイクロソフトのサポートを受ける
問題が解決した後もリソースが不適切な状態のままである場合は、サポートにお問い合わせください。  ページ右側のサポートへのリンクを使用します。  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>カスタマイズした正常性マップをダッシュボードにピン留めする
Service Health をフィルター処理して、ビジネス クリティカルなサブスクリプション、リージョン、およびリソースの種類を表示します。 フィルターを保存して、カスタマイズした正常性世界地図をポータルのダッシュボードにピン留めします。 

![個人用に設定された正常性マップをフィルター処理する](./media/service-health-overview/azure-service-health-overview-6a.png)

![個人用に設定された正常性マップをピン留めする](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>サービス正常性アラートを構成する
Service Health は Azure Monitor と統合されており、ビジネスに不可欠なリソースが影響を受けていると電子メール、テキスト メッセージ、および webhook 通知を使用してユーザーに警告します。 適切なサービス正常性イベントのアクティビティ ログ アラートを設定します。 組織内の適切なユーザーにそのアラートをルーティングするには、アクション グループを使用します。 詳しくは、[Service Health のアラートの構成](../azure-monitor/platform/alerts-activity-log-service-notifications.md)に関する記事をご覧ください。

# <a name="next-steps"></a>次の手順
アラートをセットアップして、正常性に関する問題について通知を受け取ります。 詳細については、[サービス正常性のアラートの構成](../azure-monitor/platform/alerts-activity-log-service-notifications.md)に関する記事を参照してください。 
