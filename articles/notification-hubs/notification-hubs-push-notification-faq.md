---
title: Azure Notification Hubs:よく寄せられる質問 (FAQ) | Microsoft Docs
description: Notification Hubs におけるソリューションの設計と実装についての FAQ
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: プッシュ通知、プッシュ通知、iOS のプッシュ通知、android のプッシュ通知、ios のプッシュ、android のプッシュ
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: jowargo
ms.openlocfilehash: a434101a806a810218927cbf0bc24fa0d22c5628
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488644"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Azure Notification Hubs によるプッシュ通知:よく寄せられる質問

## <a name="general"></a>全般

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Notification Hubs のリソース構造について教えてください。

Azure Notification Hubs には、2 つのリソース レベルとして、ハブと名前空間があります。 ハブは、1 つのアプリのクロスプラットフォーム プッシュ情報を保持できる単一のプッシュ リソースです。 名前空間は、1 つのリージョンのハブのコレクションです。

推奨されるマッピングでは、1 つの名前空間を 1 つのアプリに対応付けます。 その名前空間内に、運用アプリで動作する運用環境ハブ、テスト アプリで動作するテスト ハブなどを配置できます。

### <a name="what-is-the-price-model-for-notification-hubs"></a>Notification Hubs の価格モデルを教えてください。

最新の価格情報については、「[Notification Hubs の価格]」ページを参照してください。 Notification Hubs は、名前空間レベルで課金されます (名前空間の定義については、「Notification Hubs のリソース構造について教えてください」を参照してください)。Notification Hubs には、次の 3 つのレベルがあります。

* **Free**:プッシュ機能を試してみるには、このレベルで始めるのが適しています。 運用環境のアプリにはお勧めしません。 名前空間ごとに 1 か月あたり 500 デバイスと 100 万プッシュが提供されますが、サービス レベル アグリーメント (SLA) の保証はありません。
* **Basic**:このレベル (または Standard レベル) は、小規模な運用アプリにお勧めです。 名前空間ごとに 1 か月あたり 200,000 デバイスと 1,000 万プッシュがベースラインとして提供されます。
* **Standard**:このレベルは、中規模から大規模な運用アプリにお勧めです。 名前空間ごとに 1 か月あたり 1,000 万デバイスと 1,000 万プッシュがベースラインとして提供されます。 豊富な利用統計情報 (提供されるプッシュの状態に関する追加データ) が含まれます。

Standard レベルの機能:

* **豊富なテレメトリ**:Notification Hubs のメッセージごとのテレメトリを使用して、デバッグのためにすべてのプッシュ要求とプラットフォーム通知システムのフィードバックを追跡できます。
* **マルチテナント**:プラットフォーム通知システムの資格情報を名前空間レベルで使用することができます。 このオプションにより、テナントを同じ名前空間内のハブに簡単に分割することができます。
* **プッシュのスケジュール**:通知をいつ送信するかをスケジュールすることができます。
* **バルク操作**: [登録のエクスポートとインポート]に関するドキュメントで説明しているように、登録のエクスポート/インポート機能を有効にします。

### <a name="what-is-the-notification-hubs-sla"></a>Notification Hubs の SLA について教えてください。

Basic と Standard の Notification Hubs レベルでは、適切に構成されたアプリケーションは、少なくとも 99.9% の時間、プッシュ通知を送信したり、登録管理操作を行ったりすることができます。 SLA の詳細については、「[Notification Hubs の SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/)」ページを参照してください。

> [!NOTE]
> プッシュ通知はサードパーティのプラットフォーム通知システム (Apple の APNS、Google の FCM など) に依存するため、これらのメッセージの配信に対する SLA 保証はありません。 Notification Hubs がプラットフォーム通知システムにバッチを送信 (SLA 保証あり) した後、プッシュの配信 (SLA 保証なし) はプラットフォーム通知システムの責任です。

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>ハブまたは名前空間を別のレベルにアップグレードまたはダウングレードする方法を教えてください。

**[Azure Portal]**  >  **[Notification Hubs の名前空間]** または **[Notification Hubs]** の順に移動します。 更新するリソースを選択して、 **[価格レベル]** に移動します。 以下の要件に注意してください。

* 更新された価格レベルは、使用している名前空間内の "*すべて*" のハブに適用されます。
* デバイス数がダウングレード後のレベルでの上限を超えている場合は、ダウングレードする前にデバイスを削除する必要があります。

## <a name="design-and-development"></a>設計と開発

### <a name="which-server-side-platforms-do-you-support"></a>サポートするサーバー側のプラットフォームはどれですか。

.NET、Java、Node.js、PHP、Python のサーバー SDK を利用できます。 Notification Hubs API は REST インターフェイスに基づくため、さまざまなプラットフォームを使用している場合や、追加の依存関係を避ける場合は、REST API を直接使用できます。 詳細については、[Notification Hubs REST API] に関するページを参照してください。

### <a name="which-client-platforms-do-you-support"></a>どのクライアント プラットフォームをサポートしていますか。

プッシュ通知がサポートされているのは、[iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-fcm-get-started.md)、[Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Android China (Baidu 経由)](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) と Android)、および [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) です。 詳細については、[Notification Hubs の使用チュートリアル]に関するページを参照してください。

### <a name="do-you-support-text-message-email-or-web-notifications"></a>テキスト メッセージ、電子メール、または Web 通知をサポートしていますか。

Notification Hubs は、主にモバイル アプリに通知を送信することを目的としています。 電子メールまたはテキスト メッセージの機能は用意されていません。 ただし、こうした機能を提供するサードパーティ製のプラットフォームを Notification Hubs に統合し、[Mobile Apps] を使ってネイティブのプッシュ通知を送信することができます。

また、Notification Hubs には、すぐに使えるブラウザー内プッシュ通知の送信サービスもありません。 この機能は、サポートされているサーバー側プラットフォームで SignalR を使用すれば実装できます。 

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Mobile Apps と Azure Notification Hubs はどのように関係していて、どのような場合に使用すればよいですか。

モバイル アプリのバックエンドが既にあり、プッシュ通知の送信機能だけを追加する場合は、Azure Notification Hubs を使用することができます。 モバイル アプリのバックエンドを最初からセットアップする場合は、Azure App Service Mobile Apps 機能を使用することを検討してください。 モバイル アプリのバックエンドからプッシュ通知を簡単に送信できるように、モバイル アプリが通知ハブを自動的にプロビジョニングします。 Mobile Apps の料金には、通知ハブの基本料金が含まれています。 それに含まれているプッシュを超過した分だけを支払えば済みます。 コストの詳細については、「[App Service の価格]」ページを参照してください。

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Notification Hubs を使用してプッシュ通知を送信する場合、サポートされるデバイス数は何台ですか。

サポートされているデバイス数の詳細については、「[Notification Hubs の価格]」ページを参照してください。

1000 万を超える登録デバイスのサポートが必要な場合、複数のハブでデバイスをパーティション分割する必要があります。

### <a name="how-many-push-notifications-can-i-send-out"></a>送信できるプッシュ通知の数を教えてください。

選択されているレベルに応じて、Azure Notification Hubs は、システムで通信されている通知の数に基づいて自動的にスケールアップします。

> [!NOTE]
> 使用の全体コストは、送信するプッシュ通知数に基づいて増加する場合があります。 レベルごとに上限があることに注意してください。概要については、「[Notification Hubs の価格]」ページを参照してください。

Notification Hubs を使用しているお客様は、毎日数百万のプッシュ通知を送信しています。 Azure Notification Hubs を使用している限り、プッシュ通知のリーチを拡張するために特別な処理は必要ありません。

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>プッシュ通知が自分のデバイスに到達するまでどのくらいの時間がかかりますか。

Azure Notification Hubs では、着信負荷が一定で均一な通常の使用シナリオにおいては、"*1 分間に少なくとも 100 万件のプッシュ通知*" を処理できます。 この数は、タグの数、着信する通知の性質、その他の外部要因によって異なります。

サービスは、この推定配信時間中に、登録したタグやタグ式を基にしてプラットフォームあたりのターゲットを計算し、プッシュ通知サービス (PNS) にメッセージをルーティングします。 デバイスに通知を送信するのは、PNS の役目です。

PNS は、通知の送信に関するいかなる SLA も保証しません。 ただし、ほとんどのプッシュ通知は、Notification Hubs に送信されてから数分以内に (通常は 10 分以内に) ターゲット デバイスに配信されます。 一部の通知は、もう少し時間がかかります。

> [!NOTE]
> また、Azure Notification Hubs には、30 分以内に PNS に配信できないプッシュ通知を停止するポリシーがあります。 こうした遅延にはさまざまな理由が考えられますが、最も一般的な理由は PNS がアプリケーションをスロットルしているためです。

### <a name="is-there-any-latency-guarantee"></a>遅延に対する保証はありますか。

プッシュ通知の性質 (外部のプラットフォーム固有の PNS によって配信されるしくみ) のため、遅延に対する保証はありません。 通常、ほとんどのプッシュ通知は数分以内に配信されます。

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>名前空間と通知ハブを使用するソリューションを設計する際には、何を考慮する必要がありますか。

#### <a name="mobile-appenvironment"></a>モバイル アプリ/環境

* 1 つの環境、1 つのモバイル アプリに対して、1 つの通知ハブを使用してください。
* 複数テナントのシナリオでは、各テナントで個別のハブが必要になります。
* 運用環境とテスト環境で、同じ通知ハブを共有しないでください。 そのような設定では、通知を送信するときに問題が生じる場合があります (Apple では、Sandbox と Production Push エンドポイントをそれぞれ個別の資格情報で提供しています)。
* 既定では、Azure Portal または Visual Studio の Azure が統合されているコンポーネントから、登録済みのデバイスにテスト通知を送信できます。 しきい値は、登録プールからランダムに選択される 10 個のデバイスに設定されています。

> [!NOTE]
> ハブが最初は Apple サンドボックス証明書で構成され、その後 Apple 運用証明書を使用するように再構成された場合、元のデバイス トークンは無効になります。 無効なトークンは、プッシュの失敗の原因になります。 運用環境とテスト環境は別にして、それぞれの環境で別個のハブを使用することをお勧めします。

#### <a name="pns-credentials"></a>PNS の資格情報

モバイル アプリがプラットフォームの開発者ポータル (Apple、Google など) に登録されている場合は、アプリの識別子とセキュリティ トークンが送信されます。 アプリのバックエンドは、プッシュ通知をデバイスに送信できるように、プラットフォームの PNS にこれらのトークンを提供します。 セキュリティ トークンは、証明書の形式 (たとえば Apple iOS や Windows Phone) またはセキュリティ キーの形式 (たとえば Google Android や Windows) にすることができます。 これらは、通知ハブで構成する必要があります。 構成は通常、通知ハブ レベルで行われますが、マルチテナントのシナリオでは名前空間レベルで行うこともできます。

#### <a name="namespaces"></a>名前空間

名前空間はデプロイのグループ化に使用できます。 また、マルチテナントのシナリオで、同じアプリの全テナントの全通知ハブを表すために使用することもできます。

#### <a name="geo-distribution"></a>地理的分散

プッシュ通知のシナリオでは、地理的分散は常に重要というわけではありません。 デバイスにプッシュ通知を配信するさまざまな PNS (たとえば、APNS や FCM) は、均等に分散されていません。

世界中で使用されるアプリケーションの場合は、Notification Hubs サービスを使用して、世界のさまざまな Azure リージョンでさまざまな名前空間にハブを作成することができます。

> [!NOTE]
> この配置は、管理コスト (特に登録のコスト) が高くなるため、お勧めしません。 どうしても必要な場合にのみ行ってください。

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>登録はアプリのバックエンドから行うべきですか。クライアント デバイスから直接行うべきですか。

アプリのバックエンドからの登録は、登録を作成する前にクライアントを認証する必要がある場合に便利です。 アプリのロジックに基づいてアプリのバックエンドによって作成または変更される必要があるタグがある場合にも便利です。 詳細については、「[バックエンド登録のガイダンス]」ページと「[バックエンド登録のガイダンス 2]」ページを参照してください。

### <a name="what-is-the-push-notification-delivery-security-model"></a>プッシュ通知の配信のセキュリティ モデルは何ですか。

Azure Notification Hubs では、[Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) ベースのセキュリティ モデルを使用しています。 Shared Access Signature トークンはルートの名前空間レベルや詳細な通知ハブ レベルで使用できます。 メッセージ アクセス許可の送信や、通知アクセス許可のリッスンなど、さまざまな承認規則に従うように、Shared Access Signature トークンを設定できます。 詳細については、[Notification Hubs のセキュリティ モデル]に関するドキュメントを参照してください。

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>プッシュ通知内の機密情報を含むペイロードはどのように扱えばよいですか。

すべての通知は、プラットフォームの PNS によってターゲット デバイスに送信されます。 通知は、Azure Notification Hubs に送信されると、処理されて該当する PNS に渡されます。

Azure Notification Hubs を経由して送信者から PNS に至る間のすべての接続で、HTTPS が使用されます。

> [!NOTE]
> Azure Notification Hubs では、メッセージのペイロードはいかなる形でも記録されません。

機密情報が含まれているペイロードを送信するには、安全なプッシュのパターンを使用することをお勧めします。 送信者は、機密情報のペイロードが含まれていない、メッセージ識別子の付いた ping 通知をデバイスに配信します。 デバイス上のアプリがペイロードを受信すると、アプリはセキュリティで保護された API を直接呼び出して、メッセージの詳細をフェッチします。 このパターンの実装方法のガイドについては、[Notification Hubs の安全なプッシュのチュートリアル]に関するページを参照してください。

## <a name="operations"></a>Operations

### <a name="what-support-is-provided-for-disaster-recovery"></a>ディザスター リカバリーのためにはどのようなサポートが提供されていますか。

Microsoft 側でのメタデータ (Notification Hubs の名前、接続文字列、その他の重要情報など) のディザスター リカバリー対応を提供しています。 ディザスター リカバリー シナリオが開始された場合、Notification Hubs インフラストラクチャで失われる "*唯一のセグメント*" は登録データです。 次のようにして、復旧後の新しいハブにこのデータを再設定するソリューションを実装する必要があります。

1. 別のデータセンターにセカンダリ通知ハブを作成します。 ディザスター リカバリー発生時の管理機能への影響を避けるために、最初からセカンダリ通知ハブを作成しておくことをお勧めします。 ディザスター リカバリーの発生時にセカンダリ通知ハブを作成することもできます。

2. セカンダリ通知ハブにプライマリ通知ハブの登録を設定します。 両方のハブで登録を保持し、登録が行われたときに両方の同期を維持しようとすることはお勧めしません。 この方法は、登録が PNS 側で期限切れになるという本質的な傾向があるため、うまく機能しません。 Notification Hubs は、期限切れの登録や無効な登録に関する PNS フィードバックを受け取ったときに、登録をクリーンアップします。  

アプリのバックエンドについては、以下の推奨事項があります。

* 登録の特定のセットを保持する、アプリのバックエンドを使用します。 そうすれば、セカンダリ通知ハブに一括挿入を実行することができます。
* プライマリ通知ハブからバックアップとして登録の定期的なダンプを取得する、アプリのバックエンドを使用します。 そうすれば、セカンダリ通知ハブに一括挿入を実行することができます。

> [!NOTE]
> Standard レベルで利用できる登録のエクスポート/インポート機能については、[登録のエクスポートとインポート]に関するドキュメントを参照してください。

バックエンドがない場合は、ターゲット デバイスでアプリが起動するときに、セカンダリ通知ハブへの新しい登録が行われます。 最終的に、すべてのアクティブなデバイスがセカンダリ通知ハブに登録されます。

開かれていないアプリがあるデバイスが、通知を受信しない期間があります。

### <a name="is-there-audit-log-capability"></a>監査ログ機能はありますか。

はい。 すべての Notification Hubs 管理操作は、Azure アクティビティ ログに記録されます。このログは、[Azure portal] 上で公開されます。 Azure アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作に関する分析情報が提供されます。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して行われた書き込み操作 (PUT、POST、DELETE) すべてについて、いつ、誰が、何を行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 ただし、 アクティビティ ログには、読み取り (GET) 操作は含まれません。

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング

### <a name="what-troubleshooting-capabilities-are-available"></a>どのようなトラブルシューティングの機能がありますか。

Azure Notification Hubs には、トラブルシューティングを行うための機能がいくつか用意されています。これらは特に、通知が停止される最も一般的なシナリオで使用します。 詳細については、[Notification Hubs のトラブルシューティング]に関するホワイト ペーパーを参照してください。

### <a name="what-telemetry-features-are-available"></a>どのようなテレメトリ機能がありますか。

Azure Notification Hubs では、[Azure Portal] で利用統計情報を表示できます。 使用できるメトリックの詳細については、[Notification Hubs のメトリック]に関するページを参照してください。

プログラムでメトリックにアクセスすることもできます。 詳細については、次の記事を参照してください。

- [Retrieve Azure Monitor metrics with .NET (.NET を使用した Azure Monitor メトリックの取得)](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) このサンプルでは、ユーザー名とパスワードを使用します。 証明書を使用するために、[この例](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)に示すように、FromServicePrincipal メソッドをオーバーロードして、証明書を提供します。 
- [Getting metrics and activity logs for a resource (リソースのメトリックとアクティビティ ログの取得)](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure 監視 REST API のチュートリアル](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> 通知の成功は、単にプッシュ通知が外部の PNS (たとえば Apple の APNS や Google の FCM) に配信されたことを意味します。 ターゲット デバイスに通知を配信するのは、PNS の役目です。 通常、PNS は、配信メトリックを第三者に公開しません。  

[Azure Portal]: https://portal.azure.com
[Notification Hubs の価格]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Notification Hubs REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs の使用チュートリアル]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[バックエンド登録のガイダンス]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[バックエンド登録のガイダンス 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs のセキュリティ モデル]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs の安全なプッシュのチュートリアル]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Notification Hubs のトラブルシューティング]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs のメトリック]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[登録のエクスポートとインポート]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service の価格]: https://azure.microsoft.com/pricing/details/app-service/
