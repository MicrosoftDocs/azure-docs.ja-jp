---
title: "Azure Notification Hubs: よく寄せられる質問 (FAQ) | Microsoft Docs"
description: "Notification Hubs におけるソリューションの設計と実装についての FAQ"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "プッシュ通知、プッシュ通知、iOS のプッシュ通知、android のプッシュ通知、ios のプッシュ、android のプッシュ"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f3bfda250b7f8ed679d05057682b95683d402535
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Azure Notification Hubs のプッシュ通知: よく寄せられる質問
## <a name="general"></a>全般
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Notification Hubs のリソース構造について教えてください。

Azure Notification Hubs には、2 つのリソース レベルとして、ハブと名前空間があります。 ハブは、1 つのアプリのクロスプラットフォーム プッシュ情報を保持できる単一のプッシュ リソースです。 名前空間は、1 つのリージョンのハブのコレクションです。

推奨されるマッピングでは、1 つの名前空間を 1 つのアプリに対応付けます。 その名前空間内に、運用アプリで動作する運用環境ハブ、テスト アプリで動作するテスト ハブなどを配置できます。

### <a name="what-is-the-price-model-for-notification-hubs"></a>Notification Hubs の価格モデルを教えてください。
最新の価格情報については、「[Notification Hubs の価格]」ページを参照してください。 Notification Hubs は、名前空間レベルで課金されます  (名前空間の定義については、「Notification Hubs のリソース構造について教えてください」を参照してください)。Notification Hubs には、次の 3 つのレベルがあります。

* **Free**: プッシュ機能を試してみるには、このレベルで始めるのが適しています。 運用環境のアプリにはお勧めしません。 名前空間ごとに 1 か月あたり 500 デバイスと 100 万プッシュが提供されますが、サービス レベル アグリーメント (SLA) の保証はありません。
* **Basic**: このレベル (または Standard レベル) は、小規模な運用アプリにお勧めです。 名前空間ごとに 1 か月あたり 200,000 デバイスと 1,000 万プッシュがベースラインとして提供されます。 クォータを増やすオプションが含まれています。
* **Standard**: このレベルは、中規模から大規模な運用アプリにお勧めです。 名前空間ごとに 1 か月あたり 1,000 万デバイスと 1,000 万プッシュがベースラインとして提供されます。 クォータを増やすオプションと豊富なテレメトリ機能が含まれています。

Standard レベルの機能:
* **豊富なテレメトリ**: Notification Hubs のメッセージごとのテレメトリを使用して、デバッグのためにすべてのプッシュ要求とプラットフォーム通知システムのフィードバックを追跡できます。
* **マルチテナント**: プラットフォーム通知システムの資格情報を名前空間レベルで使用することができます。 このオプションにより、テナントを同じ名前空間内のハブに簡単に分割することができます。
* **プッシュのスケジュール**: 通知をいつ送信するかをスケジュールすることができます。

### <a name="what-is-the-notification-hubs-sla"></a>Notification Hubs の SLA について教えてください。
Basic と Standard の Notification Hubs レベルでは、適切に構成されたアプリケーションは、少なくとも 99.9% の時間、プッシュ通知を送信したり、登録管理操作を行ったりすることができます。 SLA の詳細については、「[Notification Hubs の SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/)」ページを参照してください。

> [!NOTE]
> プッシュ通知はサードパーティのプラットフォーム通知システム (Apple の APNS、Google の FCM など) に依存するため、これらのメッセージの配信に対する SLA 保証はありません。 Notification Hubs がプラットフォーム通知システムにバッチを送信 (SLA 保証あり) した後、プッシュの配信 (SLA 保証なし) はプラットフォーム通知システムの責任です。

### <a name="which-customers-are-using-notification-hubs"></a>Notification Hubs を使用している顧客を教えてください。
多くのお客様が Notification Hubs を使用しています。 たとえば、次のようなお客様です。

* ソチ 2014 冬季オリンピック: 数百におよぶ利益団体、300 万台以上のデバイス、2 週間でディスパッチした通知 1 億 5,000 万件以上。 [ケース スタディ: ソチ]
* Skanska: [ケース スタディ: Skanska]
* シアトル タイムズ: [ケース スタディ: シアトル タイムズ]
* Mural.ly: [ケース スタディ: Mural.ly]
* 7Digital: [ケース スタディ: 7Digital]
* Bing アプリ: 数千万台のデバイスが、1 日あたり 300 万件の通知を送信。

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>ハブまたは名前空間を別のレベルにアップグレードまたはダウングレードする方法を教えてください。
**[Azure Portal]** > **[Notification Hubs の名前空間]** または **[Notification Hubs]** の順に移動します。 更新するリソースを選択して、**[価格レベル]** に移動します。 以下の要件に注意してください。

* 更新された価格レベルは、使用している名前空間内の "*すべて*" のハブに適用されます。
* デバイス数がダウングレード後のレベルでの上限を超えている場合は、ダウングレードする前にデバイスを削除する必要があります。


## <a name="design-and-development"></a>設計と開発
### <a name="which-server-side-platforms-do-you-support"></a>サポートするサーバー側のプラットフォームはどれですか。
.NET、Java、Node.js、PHP、Python のサーバー SDK を利用できます。 Notification Hubs API は REST インターフェイスに基づくため、さまざまなプラットフォームを使用している場合や、追加の依存関係を避ける場合は、REST API を直接使用できます。 詳細については、[Notification Hubs REST API] に関するページを参照してください。

### <a name="which-client-platforms-do-you-support"></a>どのクライアント プラットフォームをサポートしていますか。
プッシュ通知がサポートされているのは、[iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、[Android China (Baidu 経由)](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) と [Android](xamarin-notification-hubs-push-notifications-android-gcm.md))、[Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md)、[Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) です。 詳細については、[Notification Hubs の使用チュートリアル]に関するページを参照してください。

### <a name="do-you-support-text-message-email-or-web-notifications"></a>テキスト メッセージ、電子メール、または Web 通知をサポートしていますか。
Notification Hubs は、主にモバイル アプリに通知を送信することを目的としています。 電子メールまたはテキスト メッセージの機能は用意されていません。 ただし、こうした機能を提供するサードパーティ製のプラットフォームを Notification Hubs に統合し、[Mobile Apps] を使ってネイティブのプッシュ通知を送信することができます。

また、Notification Hubs には、すぐに使えるブラウザー内プッシュ通知の送信サービスもありません。 この機能は、サポートされているサーバー側プラットフォームで SignalR を使用すれば実装できます。 Chrome サンドボックスのブラウザー アプリに通知を送信する方法については、[Chrome アプリのチュートリアル]を参照してください。

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Mobile Apps と Azure Notification Hubs はどのように関係していて、どのような場合に使用すればよいですか。
モバイル アプリのバックエンドが既にあり、プッシュ通知の送信機能だけを追加する場合は、Azure Notification Hubs を使用することができます。 モバイル アプリのバックエンドを最初からセットアップする場合は、Azure App Service の Mobile Apps 機能を使用することを検討してください。 モバイル アプリが通知ハブを自動的にプロビジョニングするため、モバイル アプリのバックエンドからプッシュ通知を簡単に送信することができます。 Mobile Apps の料金には、通知ハブの基本料金が含まれています。 それに含まれているプッシュを超過した分だけを支払えば済みます。 コストの詳細については、「[App Service の価格]」ページを参照してください。

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Notification Hubs を使用してプッシュ通知を送信する場合、サポートされるデバイス数は何台ですか。
サポートされているデバイス数の詳細については、「[Notification Hubs の価格]」ページを参照してください。

1,000 万台を超える登録済みのデバイスをサポートしたい場合は、直接[お問い合わせ](https://azure.microsoft.com/overview/contact-us/)ください。ソリューションの拡張をお手伝いいたします。

### <a name="how-many-push-notifications-can-i-send-out"></a>送信できるプッシュ通知の数を教えてください。
選択されているレベルに応じて、Azure Notification Hubs は、システムで通信されている通知の数に基づいて自動的にスケールアップします。

> [!NOTE]
> 使用の全体コストは、送信するプッシュ通知数に基づいて増加する場合があります。 レベルごとに上限があることに注意してください。概要については、「[Notification Hubs の価格]」ページを参照してください。
> 
> 

Notification Hubs を使用しているお客様は、毎日数百万のプッシュ通知を送信しています。 Azure Notification Hubs を使用している限り、プッシュ通知のリーチを拡張するために特別な処理は必要ありません。

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>プッシュ通知が自分のデバイスに到達するまでどのくらいの時間がかかりますか。
Azure Notification Hubs では、着信負荷が一定で均一な通常の使用シナリオにおいては、"*1 分間に少なくとも 100 万件のプッシュ通知*" を処理できます。 この数は、タグの数、着信する通知の性質、その他の外部要因によって異なります。

サービスは、この推定配信時間中に、登録したタグやタグ式を基にしてプラットフォームあたりのターゲットを計算し、プッシュ通知サービス (PNS) にメッセージをルーティングします。 デバイスに通知を送信するのは、PNS の役目です。

PNS は、通知の送信に関するいかなる SLA も保証しません。 ただし、ほとんどのプッシュ通知は、Notification Hubs に送信されてから数分以内に (通常は 10 分以内に) ターゲット デバイスに配信されます。 一部の通知は、もう少し時間がかかります。

> [!NOTE]
> また、Azure Notification Hubs には、30 分以内に PNS に配信できないプッシュ通知を停止するポリシーがあります。 こうした遅延にはさまざまな理由が考えられますが、最も一般的な理由は PNS がアプリケーションをスロットルしているためです。
> 
> 

### <a name="is-there-any-latency-guarantee"></a>遅延に対する保証はありますか。
プッシュ通知の性質 (外部のプラットフォーム固有の PNS によって配信されるしくみ) のため、遅延に対する保証はありません。 通常、ほとんどのプッシュ通知は数分以内に配信されます。

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>名前空間と通知ハブを使用するソリューションを設計する際には、何を考慮する必要がありますか。
#### <a name="mobile-appenvironment"></a>モバイル アプリ/環境
* 1 つの環境、1 つのモバイル アプリに対して、1 つの通知ハブを使用してください。
* マルチテナントのシナリオでは、各テナントに個別のハブが必要になります。
* 運用環境とテスト環境で、同じ通知ハブを共有しないでください。 そのような設定では、通知を送信するときに問題が生じる場合があります  (Apple では、Sandbox と Production Push エンドポイントをそれぞれ個別の資格情報で提供しています)。
* 既定では、Azure Portal または Visual Studio の Azure が統合されているコンポーネントから、登録済みのデバイスにテスト通知を送信できます。 しきい値は、登録プールからランダムに選択される 10 個のデバイスに設定されています。

> [!NOTE]
> ハブが最初は Apple サンドボックス証明書で構成され、その後 Apple 運用証明書を使用するように再構成された場合、元のデバイス トークンは無効になります。 無効なトークンは、プッシュの失敗の原因になります。 運用環境とテスト環境は別にして、それぞれの環境で別個のハブを使用することをお勧めします。
> 
> 

#### <a name="pns-credentials"></a>PNS の資格情報
モバイル アプリがプラットフォームの開発者ポータル (Apple、Google など) に登録されている場合は、アプリの識別子とセキュリティ トークンが送信されます。 アプリのバックエンドは、プッシュ通知をデバイスに送信できるように、プラットフォームの PNS にこれらのトークンを提供します。 セキュリティ トークンは、証明書の形式 (たとえば Apple iOS や Windows Phone) またはセキュリティ キーの形式 (たとえば Google Android や Windows) にすることができます。 これらは、通知ハブで構成する必要があります。 構成は通常、通知ハブ レベルで行われますが、マルチテナントのシナリオでは名前空間レベルで行うこともできます。

#### <a name="namespaces"></a>名前空間
名前空間はデプロイのグループ化に使用できます。 また、マルチテナントのシナリオで、同じアプリの全テナントの全通知ハブを表すために使用することもできます。

#### <a name="geo-distribution"></a>地理的分散
プッシュ通知のシナリオでは、地理的分散は常に重要というわけではありません。 デバイスにプッシュ通知を配信するさまざまな PNS (たとえば、APNS や GCM) は、均等に分散されていません。

世界中で使用されるアプリケーションの場合は、Notification Hubs サービスを使用して、世界のさまざまな Azure リージョンでさまざまな名前空間にハブを作成することができます。

> [!NOTE]
> この配置は、管理コスト (特に登録のコスト) が高くなるため、お勧めしません。 どうしても必要な場合にのみ行ってください。
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>登録はアプリのバックエンドから行うべきですか。クライアント デバイスから直接行うべきですか。
アプリのバックエンドからの登録は、登録を作成する前にクライアントを認証する必要がある場合に便利です。 アプリのロジックに基づいてアプリのバックエンドによって作成または変更される必要があるタグがある場合にも便利です。 詳細については、「[バックエンド登録のガイダンス]」ページと「[バックエンド登録のガイダンス 2]」ページを参照してください。

### <a name="what-is-the-push-notification-delivery-security-model"></a>プッシュ通知の配信のセキュリティ モデルは何ですか。
Azure Notification Hubs では、[Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) ベースのセキュリティ モデルを使用しています。 Shared Access Signature トークンはルートの名前空間レベルや詳細な通知ハブ レベルで使用できます。 メッセージ アクセス許可の送信や、通知アクセス許可のリッスンなど、さまざまな承認規則に従うように、Shared Access Signature トークンを設定できます。 詳細については、[Notification Hubs のセキュリティ モデル]に関するドキュメントを参照してください。

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>プッシュ通知内の機密情報を含むペイロードはどのように扱えばよいですか。
すべての通知は、プラットフォームの PNS によってターゲット デバイスに送信されます。 通知は、Azure Notification Hubs に送信されると、処理されて該当する PNS に渡されます。

Azure Notification Hubs を経由して送信者から PNS に至る間のすべての接続で、HTTPS が使用されます。

> [!NOTE]
> Azure Notification Hubs では、メッセージのペイロードはいかなる形でも記録されません。
> 
> 

機密情報が含まれているペイロードを送信するには、安全なプッシュのパターンを使用することをお勧めします。 送信者は、機密情報のペイロードが含まれていない、メッセージ識別子の付いた ping 通知をデバイスに配信します。 デバイス上のアプリがペイロードを受信すると、アプリはセキュリティで保護された API を直接呼び出して、メッセージの詳細をフェッチします。 このパターンの実装方法のガイドについては、[Notification Hubs の安全なプッシュのチュートリアル]に関するページを参照してください。

## <a name="operations"></a>操作
### <a name="what-support-is-provided-for-disaster-recovery"></a>ディザスター リカバリーのためにはどのようなサポートが提供されていますか。
Microsoft 側でのメタデータ (Notification Hubs の名前、接続文字列、その他の重要情報など) のディザスター リカバリー対応を提供しています。 ディザスター リカバリー シナリオが開始された場合、Notification Hubs インフラストラクチャで失われる "*唯一のセグメント*" は登録データです。 次のようにして、復旧後の新しいハブにこのデータを再設定するソリューションを実装する必要があります。

1. 別のデータセンターにセカンダリ通知ハブを作成します。 ディザスター リカバリー発生時の管理機能への影響を避けるために、最初からセカンダリ通知ハブを作成しておくことをお勧めします。 ディザスター リカバリーの発生時にセカンダリ通知ハブを作成することもできます。

2. セカンダリ通知ハブにプライマリ通知ハブの登録を設定します。 両方のハブで登録を保持し、登録が行われたときに両方の同期を維持しようとすることはお勧めしません。 この方法は、登録が PNS 側で期限切れになるという本質的な傾向があるため、うまく機能しません。 Notification Hubs は、期限切れの登録や無効な登録に関する PNS フィードバックを受け取ったときに、登録をクリーンアップします。  

アプリのバックエンドに関して、次の 2 つの推奨事項があります。

* 登録の特定のセットを保持する、アプリのバックエンドを使用します。 そうすれば、セカンダリ通知ハブに一括挿入を実行することができます。

* プライマリ通知ハブからバックアップとして登録の定期的なダンプを取得する、アプリのバックエンドを使用します。 そうすれば、セカンダリ通知ハブに一括挿入を実行することができます。

> [!NOTE]
> Standard レベルで利用できる登録のエクスポート/インポート機能については、[登録の一括エクスポートと変更]に関するドキュメントを参照してください。
> 
> 

バックエンドがない場合は、ターゲット デバイスでアプリが起動するときに、セカンダリ通知ハブへの新しい登録が行われます。 最終的に、すべてのアクティブなデバイスがセカンダリ通知ハブに登録されます。

開かれていないアプリがあるデバイスが、通知を受信しない期間があります。

### <a name="is-there-audit-log-capability"></a>監査ログ機能はありますか。
すべての Notification Hubs 管理操作は、操作ログに記録されます。このログは、[Azure クラシック ポータル]で公開されます。

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング
### <a name="what-troubleshooting-capabilities-are-available"></a>どのようなトラブルシューティングの機能がありますか。
Azure Notification Hubs には、トラブルシューティングを行うための機能がいくつか用意されています。これらは特に、通知が停止される最も一般的なシナリオで使用します。 詳細については、[Notification Hubs のトラブルシューティング]に関するホワイト ペーパーを参照してください。

### <a name="what-telemetry-features-are-available"></a>どのようなテレメトリ機能がありますか。
Azure Notification Hubs では、[Azure クラシック ポータル]でテレメトリ データを表示できます。 使用できるメトリックの詳細については、[Notification Hubs のメトリック]に関するページを参照してください。

> [!NOTE]
> 通知の成功は、単にプッシュ通知が外部の PNS (たとえば Apple の APNS や Google の GCM) に配信されたことを意味します。 ターゲット デバイスに通知を配信するのは、PNS の役目です。 通常、PNS は、配信メトリックを第三者に公開しません。  
> 
> 

Microsoft では、プログラムによってテレメトリ データをエクスポートする機能も提供しています (Standard レベル)。 詳細については、[Notification Hubs のメトリックのサンプル]を参照してください。

[Azure クラシック ポータル]: https://manage.windowsazure.com
[Notification Hubs の価格]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[ケース スタディ: ソチ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[ケース スタディ: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[ケース スタディ: シアトル タイムズ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[ケース スタディ: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[ケース スタディ: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Notification Hubs REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs の使用チュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome アプリのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[バックエンド登録のガイダンス]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[バックエンド登録のガイダンス 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs のセキュリティ モデル]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs の安全なプッシュのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Notification Hubs のトラブルシューティング]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs のメトリック]: https://msdn.microsoft.com/library/dn458822.aspx
[Notification Hubs のメトリックのサンプル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[登録の一括エクスポートと変更]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service の価格]: https://azure.microsoft.com/pricing/details/app-service/

