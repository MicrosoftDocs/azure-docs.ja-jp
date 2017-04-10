---
title: "Azure Notification Hubs - よく寄せられる質問 (FAQ)"
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
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Azure Notification Hubs のプッシュ通知 - よく寄せられる質問 (FAQ)
## <a name="general"></a>全般
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.Notification Hubs のリソース構造について教えてください。

Notification Hubs には、2 つのリソース レベル、ハブおよび名前空間があります。 ハブは、1 つのアプリのクロスプラットフォーム プッシュ情報を保持できる単一のプッシュ リソースです。 名前空間は、1 つのリージョンのハブのコレクションです。

一般的に推奨されるのは、1 つのアプリに対応するように 1 つの名前空間を配置するマッピングです。 その名前空間内に、運用アプリで動作する運用環境ハブ、テスト アプリで動作するテスト ハブなどを配置できます。

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.Notification Hubs の価格モデルを教えてください。
最新の情報については、「 [Notification Hubs の価格] 」ページを参照してください。 Notification Hubs は、名前空間レベルで課金され (名前空間については、上記のリソース構造を参照してください)、3 つのレベルが提供されています。

* **Free** - このレベルは、運用環境アプリには適していませんが、プッシュ機能について調べるための出発点としてお勧めです。 名前空間ごとに 1 か月あたり 500 個のデバイスと 100 万回のプッシュ数が含まれます。SLA の保証はありません。
* **Basic** - このレベルと Standard レベルは、小規模な運用アプリにお勧めです。 基準値として、名前空間ごとに 1 か月あたり 20 万個のデバイスと 1,000 万回のプッシュ数が含まれ、オプションでクォータを増やすことができます。
* **Standard** - 中規模から大規模な運用アプリにお勧めです。 基準値として、名前空間ごとに 1 か月あたり 1,000 万個のデバイスと 1,000 万回のプッシュ数が含まれ、オプションでクォータを増やすことができ、充実したテレメトリも利用できます。

Standard レベルのすばらしい機能の一部をここで紹介します。
* "*豊富なテレメトリ*" - Notification Hubs は、メッセージごとのテレメトリを提供して、すべてのプッシュ要求と、デバッグが必要なプラットフォーム通知システムのフィードバックを追跡します。
* "*マルチテナント*" - プラットフォーム通知システム (PNS) の資格情報を名前空間レベルで使用することができます。 これにより、テナントを同じ名前空間内のハブに簡単に分割することができます。
* "*プッシュのスケジュール*" - 通知がいつでも送信されるようにスケジュールすることができます。

### <a name="2--what-is-the-notification-hubs-sla"></a>2.Notification Hubs の SLA について教えてください。
**Basic** と **Standard** レベルの Notification Hubs では、アプリケーションが正しく構成されている場合、サポートされているレベルにデプロイされている通知ハブから、少なくとも 99.9% はプッシュ通知を送信したり、登録管理操作が実行できることが保証されています。 SLA の詳細については、「 [Notification Hubs の SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) 」ページを参照してください。

> [!NOTE]
> プッシュ通知はサード パーティのプラットフォーム通知システム (Apple の APNS、Google の FCM など) に依存するため、これらのメッセージの配信に対する SLA 保証はありません。 Notification Hubs が PNS への送信をバッチで処理 (SLA 保証あり) した後は、プッシュが配信されるかどうかは PNS 任せとなります (SLA 保証なし)。

### <a name="3--which-customers-are-using-notification-hubs"></a>3.Notification Hubs を使用している顧客を教えてください。
大勢のお客様が Notification Hubs をご使用になっています。次にいくつかご紹介します。

* ソチ 2014 年冬季オリンピック - 数百におよぶ利益団体、300 万台以上のデバイス、2 週間でディスパッチした通知 150 万件以上 [CaseStudy - ソチ]
* Skanska - [CaseStudy - Skanska]
* シアトル タイムズ - [CaseStudy - シアトル タイムズ]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing アプリ - 数千万台のデバイス、1 日あたり 300 万件の通知を送信

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4.ハブまたは名前空間を別のレベルにアップグレードまたはダウングレードする方法を教えてください。
[Azure Portal] の [Notification Hubs Namespaces (Notification Hubs の名前空間)] または [Notification Hubs] に移動して、更新するリソースをクリックし、ナビゲーションで [価格レベル] に移動します。 希望するどのレベルにも更新できますが、いくつか注意する点があります。
* 更新された価格レベルは、使用している名前空間内の "*すべて*" のハブに適用されます。
* ダウングレードする場合、デバイスの数がダウングレード先のレベルの上限を超えているときは、ダウングレードする前に、上限内に収めるためにデバイスを削除する必要があります。


## <a name="design--development"></a>設計と開発
### <a name="1--which-server-side-platforms-do-you-support"></a>1.サポートするサーバー側のプラットフォームはどれですか。
.NET、Java、Node.js、PHP、および Python のサーバー SDK が用意されています。 さらに、Notification Hubs API は REST インターフェイスに基づくため、さまざまなプラットフォームを使用している場合や、追加の依存関係を避ける場合は、REST API を直接使用する方法を選択できます。 詳細については、[NH REST API] に関するページを参照してください。

### <a name="2--which-client-platforms-do-you-support"></a>2.どのクライアント プラットフォームをサポートしていますか。
[iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、[Android China (Baidu 経由)](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) および  & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md))、[Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md)、および [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) への通知の送信をサポートしています。 詳細については、「Notification Hubs の使用」ページを参照してください。

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.SMS/メール/Web 通知はサポートしていますか。
Notification Hubs は、主にモバイル アプリに通知を送信することを目的とするため、メールや SMS の機能は提供していません。 ただし、こうした機能を提供するサード パーティ製のプラットフォームを Notification Hubs に統合させて、[Azure Mobile Apps] を使いネイティブのプッシュ通知を送信できます。

また、Notification Hubs には、すぐに使えるブラウザー内プッシュ通知の送信サービスもありません。 これは、サポートされているサーバー側プラットフォームに SignalR を使用して実装すれば使用できます。 Chrome サンドボックスのブラウザー アプリに通知を送信する方法については、「 [Chrome アプリのチュートリアル]」を参照してください。

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.  Azure Mobile Apps と Azure Notification Hubs との関係について教えてください。いつ、どれを使えばいいのですか。
モバイル アプリのバックエンドが既にあり、プッシュ通知の送信機能だけを追加する場合は、Azure Notification Hubs を使う必要があります。 モバイル アプリのバックエンドを最初から設定する場合は、Azure Mobile Apps の使用を検討する必要があります。 Azure Mobile Apps では通知ハブを自動的にプロビジョニングするため、モバイル アプリのバックエンドから簡単にプッシュ通知を送信できます。 Azure Mobile Apps の料金には、通知ハブの基本料金が含まれており、それに含まれるプッシュ数を超過する場合のみ料金を支払います。 価格の詳細については、「 [App Service の価格] 」ページを参照してください。

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.  Notification Hubs を使用してプッシュ通知を送信する場合、サポートされるデバイス数は何台ですか。
サポートされているデバイス数の詳細については、「 [Notification Hubs の価格] 」を参照してください。

特定のシナリオで、1,000 万を超える登録済みのデバイスをサポートするようにソリューションを拡張したい場合は、直接 [お問い合わせ](https://azure.microsoft.com/overview/contact-us/) ください。

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.  送信できるプッシュ通知の数を教えてください。
選択されているレベルに応じ、Azure は、システムで流動する通知の数を基に自動的に増やします。

> [!NOTE]
> 使用の全体コストは、送信するプッシュ通知数によって上がる場合があります。 レベルによって制限があることを理解しておいてください。これは、「[Notification Hubs の価格]」ページで概説されています。
> 
> 

既存のお客様は Notification Hubs を使って毎日数百万のプッシュ通知を送信しています。 Azure Notification Hubs を使用している限り、プッシュ通知のリーチを拡張するために、特別な処理は必要ありません。

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.  プッシュ通知が自分のデバイスに到達するまでどのくらいの時間がかかりますか。
Azure Notification Hubs では、着信負荷が一定で急増することのない通常の使用シナリオにおいては、 **1 分間に少なくとも 100 万件のプッシュ通知** を処理できます。 この数はタグの数や着信するメッセージおよびその他の要因によって異なります。

サービスは、この推定配信時間中に、登録したタグやタグ式を基に、プラットフォームあたりのターゲットを計測し、各プッシュ通知配信サービスへメッセージを送信します。 ここからデバイスに通知を送信するのはプッシュ通知サービス (PNS) の責任になります。

PNS では、通知の送信に対する SLA を保証しませんが、通常ほとんどのプッシュ通知はこちらのプラットフォームに送信された時から数分以内 (通常は 10 分の制限時間内) にデバイスに送信されます。 中にはもう少し時間がかかるものもあります。

> [!NOTE]
> また、Azure Notification Hubs には 30 分以内に PNS に送信できないプッシュ通知を停止するポリシーがあります。 こうした遅延にはさまざまな理由が考えられますが、最も一般的な理由は PNS がアプリケーションをスロットルするためです。
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.  遅延に対する保証はありますか。
プッシュ通知は外部プラットフォーム専用のプッシュ通知サービスによって送信されるという性質であるため、遅延に対する保証はありません。 通常、ほとんどのプッシュ通知は数分以内に送信されます。

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.  名前空間と Notification Hubs を使用するソリューションを設計する際に考慮する必要がある点を教えてください。
#### <a name="mobile-appenvironment"></a>モバイル アプリ/環境
* 1 つの環境、1 つのモバイル アプリに対して通知ハブは 1 つにする必要があります。
* 複数テナントのシナリオでは、各テナントで個別のハブが必要になります。
* テスト環境と本番環境で同じ通知ハブを共有しないでください。通知の送信中に問題が発生する原因になる場合があります。 たとえば、Apple では Sandbox と Production Push エンドポイントをそれぞれ個別の資格情報で提供しています。
* 既定では、Azure ポータルまたは Visual Studio の Azure が統合されているコンポーネントから、登録済みのデバイスにテスト通知を送信できます。 しきい値は、登録プールからランダムに選択される 10 個のデバイスに設定されています。

> [!NOTE]
> ハブがもともと Apple Sandbox の証明書で構成されており、後から Apple の本番用の証明書を使用するように再構成される場合、古いデバイスのトークンは新しい証明書で無効になり、プッシュが失敗する原因になります。 本番環境とテスト環境は別にし、個別の環境にはそれぞれのハブを使用することをお勧めします。
> 
> 

#### <a name="pns-credentials"></a>PNS の資格情報
モバイル アプリがプラットフォームの開発者ポータル (Apple や Google など) で登録される場合、アプリの識別子とセキュリティ トークンを取得します。これはデバイスにプッシュ通知を送信できるようにするために、アプリのバックエンドがプラットフォームのプッシュ通知サービスに提供するものです。 セキュリティ トークンは、Notification Hubs で構成する必要がある証明書 (Apple iOS、Windows Phone など) やセキュリティ キー (Google Android、Windows など) の形式にできます。 これは通常、通知ハブ レベルで行われますが、マルチ テナントのシナリオでは名前空間レベルで行うこともできます。

#### <a name="namespaces"></a>名前空間
名前空間はデプロイのグループ化に使用できます。  また、マルチ テナントのシナリオで同じアプリのすべてのテナントにすべての通知ハブを表すことにも使用できます。

#### <a name="geo-distribution"></a>地理的分散
プッシュ通知のシナリオでは、地理的分散は常に重要というわけではありません。 最終的にプッシュ通知をデバイスに送信するさまざまなプッシュ通知サービス (APNS、GCM など) が均等に分散されていないことに注意してください。

アプリケーションが世界中で使われている場合は、別の名前空間で複数のハブを作成し、全世界のさまざまな Azure リージョンで Notification Hubs サービスの可用性を活用できます。

> [!NOTE]
> これは、特に登録に関する管理コストが増加するため、お勧めできません。本当に必要な場合のみ行ってください。
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.  登録はアプリのバックエンドから行うべきですか。クライアント デバイスから直接行うべきですか。
アプリのバックエンドからの登録は、登録を作成する前にクライアント認証を実行する必要がある場合、アプリのロジックに基づいてアプリのバックエンドで作成または変更する必要のあるタグがある場合に役立ちます。 詳細については、「[バックエンド登録のガイダンス]」ページと「[バックエンド登録のガイダンス - 2]」ページを参照してください。

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.  プッシュ通知の配信のセキュリティ モデルは何ですか。
Azure Notification Hubs では [Shared Access Signature (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) ベースのセキュリティ モデルを使用しています。 SAS トークンはルートの名前空間レベルや詳細な通知ハブ レベルで使用できます。 こうした SAS トークンは、送信メッセージの許可やリッスン通知の許可などさまざまな承認規則で設定できます。詳細については、「[NH セキュリティ モデル]」のドキュメントを参照してください。

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.  プッシュ通知内の機密情報を含むペイロードはどのように扱われますか。
すべての通知は、プラットフォームのプッシュ通知サービス (PNS) によってターゲット デバイスに送信されます。 送信者が Azure Notification Hubs に通知を送信すると、通知は処理されてそれぞれの PNS に渡されます。

送信者から Azure Notification Hubs に接続され、さらに HTTPS を使用する PNS へと繋がります。

> [!NOTE]
> Azure Notification Hubs でメッセージのペイロードが記録されることは決してありません。
> 
> 

ただし、機密情報を含むペイロードの送信には、セキュリティで保護されたプッシュのパターンをお勧めします。送信者は機密情報を含むペイロードなしで、メッセージの識別子が付いた ping 通知をデバイスに送信します。デバイスのアプリでこのペイロードを受信すると、セキュリティ保護された API が直接呼び出され、メッセージの詳細がフェッチされます。 前述のパターンを実装する方法の説明については、「[NH - 安全なプッシュのチュートリアル]」ページを参照してください。

## <a name="operations"></a>操作
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.  災害復旧 (DR) ストーリーとは何ですか。
Microsoft ではメタデータの災害復旧 (通知ハブの名前、接続文字列やその他の重要情報など) を提供しています。 災害復旧シナリオが開始された場合、Notification Hubs インフラストラクチャで失われるのは、登録データ **セグメントのみ** です。 復旧後、新しいハブにこのデータを再入力するソリューションを実装する必要があります。

* *ステップ 1* - 別のデーター センターにセカンダリ通知ハブを作成します。 これは DR 発生時にすぐに作成できます。または最初から作成しておくこともできます。 通知ハブは、数秒間で比較的高速にプロビジョニングできるため、どちらのオプションを選択しても大きな違いはありません。 最初から作成しておくことによって、DR 発生時の管理機能への影響からも保護できるので、こちらのオプションを強くお勧めします。
* *ステップ 2* - プライマリの通知ハブからの登録をセカンダリの通知ハブに利用します。 登録は入ってくるものであり、一般的に PNS 側により期限切れになる傾向により正しく作動しないので、両方のハブで登録を管理し、最新に保つことはお勧めしません。 Notification Hubs では、期限が切れたり、無効な登録についての PNS のフィードバックを受信しながら、そうした登録をクリーンアップします。  

次のいずれかのアプリのバックエンドを使用することをお勧めします。

* 一連の登録を管理し、DR 発生時にセカンダリの通知ハブに一括挿入できる

**OR**

* バックアップとしてプライマリのハブから登録のダンプを定期的に取得し、セカンダリ NH に一括挿入する

> [!NOTE]
> Standard レベルで利用できる登録のエクスポート/インポート機能については「 [登録の一括エクスポートと変更] 」を参照してください。
> 
> 

バックエンドがない場合、ターゲット デバイスのいずれかでアプリを起動すると、セカンダリ通知ハブで新しい登録が実行され、最終的に、セカンダリ通知ハブにすべてのアクティブなデバイスが登録されます。

短所は、アプリが起動していないとき通知を受信しない期間がデバイスにあるという点です。

### <a name="2----is-there-any-audit-log-capability"></a>2.  監査ログ機能はありますか。
すべての Notification Hubs 管理操作は、 [Azure クラシック ポータル]で公開されている操作ログに移動します。

## <a name="monitoring--troubleshooting"></a>監視とトラブルシューティング
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.  どのようなトラブルシューティングの機能がありますか。
Azure Notification Hubs には、特に通知がドロップされた場合の最も一般的なシナリオをトラブルシューティングする機能がいくつか用意されています。 詳細については、ホワイト ペーパー「 [NH - トラブルシューティング] 」を参照してください。

### <a name="2----what-telemetry-features-are-available"></a>2.  どのようなテレメトリ機能がありますか。
Azure Notification Hubs では [Azure クラシック ポータル]でテレメトリ データの表示を有効にします。 使用可能なメトリックの詳細については、「 [NH - メトリック] 」を参照してください。

> [!NOTE]
> 通知の成功は、プッシュ通知が外部のプッシュ通知サービス (例: Apple の APNS、Google の GCM など) に、配信されたことのみを意味します。 ターゲット デバイスに通知が配信されるかどうかは PNS の責任です。 通常、PNS は、サード パーティに配信メトリックを公開しません。  
> 
> 

また、プログラムによってテレメトリ データをエクスポートする機能も提供しています ( **Standard** レベル)。 詳細については、「 [NH - メトリックのサンプル] 」を参照してください。

[Azure クラシック ポータル]: https://manage.windowsazure.com
[Notification Hubs の価格]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - ソチ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - シアトル タイムズ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome アプリのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[バックエンド登録のガイダンス]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[バックエンド登録のガイダンス - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH セキュリティ モデル]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - 安全なプッシュのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - トラブルシューティング]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - メトリック]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - メトリックのサンプル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[登録の一括エクスポートと変更]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service の価格]: https://azure.microsoft.com/pricing/details/app-service/

