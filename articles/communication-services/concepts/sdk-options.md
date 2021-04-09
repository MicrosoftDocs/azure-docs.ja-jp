---
title: Azure Communication Services のクライアント ライブラリと REST API
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services のクライアント ライブラリと REST API について詳しく説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 01a5f4a947f0b89b5881eddb3c743b9a9b184b19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495642"
---
# <a name="client-libraries-and-rest-apis"></a>クライアント ライブラリと REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure Communication Services の機能は、概念的に 6 つの領域に分けられています。 一部の領域には、完全にオープンソースのクライアント ライブラリが含まれます。 Calling クライアント ライブラリでは独自のネットワーク インターフェイスが使用され、現在はクローズド ソースです。Chat ライブラリにはクローズド ソースの依存関係が含まれています。 クライアント ライブラリのサンプルとその他の技術的な詳細は、[Azure Communication Services の GitHub リポジトリ](https://github.com/Azure/communication)で公開されています。

## <a name="client-libraries"></a>クライアント ライブラリ

| アセンブリ               | プロトコル             |オープンまたはクローズド ソース| 名前空間                          | 機能                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | [ファイル]            | Azure.ResourceManager.Communication | Communication Services のリソースをプロビジョニングおよび管理します             |
| 共通                 | REST | [ファイル]               | Azure.Communication.Common          | 他のクライアント ライブラリの基本データ型を提供します |
| ID         | REST | [ファイル]               | Azure.Communication.Identity  | ユーザーを管理し、トークンにアクセスします |
| 電話番号         | REST | [ファイル]               | Azure.Communication.PhoneNumbers  | 電話番号の管理 |
| チャット                   | 独自の通知を含む REST | オープン (クローズド ソースの通知パッケージを使用)    | Azure.Communication.Chat            | アプリケーションにリアルタイムのテキスト ベースのチャットを追加します  |
| SMS                    | REST | [ファイル]              | Azure.Communication.SMS             | SMS メッセージを送信および受信します |
| 呼び出し                | 独自の転送 | クローズ |Azure.Communication.Calling         | 音声、ビデオ、画面共有、およびその他のリアルタイム データ通信機能を活用します          |

Azure Resource Manager、ID、および SMS クライアント ライブラリはサービス統合に重点を置いており、多くの場合、これらの機能をエンド ユーザー アプリケーションに統合すると、セキュリティ上の問題が発生します。 Common および Chat クライアント ライブラリは、サービスおよびクライアント アプリケーションに適しています。 Calling クライアント ライブラリは、クライアント アプリケーション向けに設計されています。 サービス シナリオに重点を置いたクライアン トライブラリは開発中です。

### <a name="languages-and-publishing-locations"></a>言語と公開場所

以下では、個々のクライアント ライブラリ パッケージの公開場所について説明します。

| 領域           | JavaScript | .NET | Python | Java SE | iOS | Android | その他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [GitHub 経由の Go](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 共通         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | 該当なし      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| ID | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| 電話番号 | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| チャット           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 呼び出し        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| リファレンス ドキュメント     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>REST API

Communication Services API は、他の Azure REST API と共に [docs.microsoft.com](/rest/api/azure/) に記載されています。 このドキュメントでは、HTTP メッセージを構成する方法と、Postman を使用するためのガイダンスが提供されます。 このドキュメントは [GitHub](https://github.com/Azure/azure-rest-api-specs) の Swagger 形式でも提供されています。

## <a name="additional-support-details"></a>その他のサポートの詳細

### <a name="ios-and-android-support-details"></a>iOS と Android のサポートの詳細

- Communication Services iOS クライアント ライブラリは、iOS バージョン 13 以降と Xcode 11 以降を対象としています。
- Android Java クライアント ライブラリは、Android API レベル 21 以上と Android Studio 4.0 以降を対象としています

### <a name="net-support-details"></a>.NET サポートの詳細

Calling を除き、Communication Services パッケージは、以下に示すプラットフォームがサポートされる .NET Standard 2.0 をターゲットとしています。

.NET Framework 4.6.1 を介したサポート
- Windows 10、8.1、8、7
- Windows Server 2012 R2、2012、2008 R2 SP1

.NET Core 2.0 を介したサポート:
- Windows 10 (1607 以降)、7 SP1+、8.1
- Windows Server 2008 R2 SP1+
- Max OS X 10.12 以降
- Linux の複数のバージョン/ディストリビューション
- UWP 10.0.16299 (RS3) 2017 年 9 月
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

## <a name="calling-client-library-timeouts"></a>通話クライアント ライブラリに関わるタイムアウト

次のタイムアウトが Communication Services の通話クライアント ライブラリに適用されます。

| アクション           | タイムアウトまでの秒数 |
| -------------- | ---------- |
| 参加者の再接続/削除 | 120 |
| 通話に対する新しいモダリティの追加または削除 (ビデオまたはスクリーン共有の開始/停止) | 40 |
| 通話転送操作に関わるタイムアウト | 60 |
| 1:1 通話確立に関わるタイムアウト | 85 |
| グループ通話確立に関わるタイムアウト | 85 |
| PSTN 通話確立に関わるタイムアウト | 115 |
| 1:1 通話のグループ通話への昇格に関わるタイムアウト | 115 |


## <a name="api-stability-expectations"></a>API の安定性に関する想定

> [!IMPORTANT]
> このセクションでは、**安定** とマークされている REST API およびクライアント ライブラリに関するガイダンスを提供します。 プレリリース、プレビュー、またはベータとマークされている API は、**予告なしに** 変更されたり非推奨になったりする場合があります。

将来的に、Communication Services クライアント ライブラリのバージョンが廃止される可能性があります。また、REST API とリリースされたクライアント ライブラリに対して破壊的変更が加えられる可能性があります。 Azure Communication Services は、サービス バージョンの廃止に関して、"*通常は*" 次の 2 つのサポート ポリシーに従います。

- Communication Services インターフェイスの変更に起因するコードの変更を求められる場合、お客様は少なくとも 3 年前に通知されます。 通常、ドキュメントに記載されているすべての REST API とクライアント ライブラリ API では、インターフェイスが使用停止になる前に少なくとも 3 年間警告が発生します。
- お客様は、クライアント ライブラリ アセンブリを最新のマイナー バージョンに更新する必要が生じる少なくとも 1 年前に通知されます。 これらの必須の更新は、同じメジャー バージョンに含まれているため、コードを変更する必要はありません。 これは、セキュリティとパフォーマンスに関する更新を頻繁に必要とするリアルタイム コンポーネントを持つ Calling および Chat ライブラリに対しては特に当てはまります。 Communication Services クライアント ライブラリを最新の状態に保つことを強くお勧めします。

### <a name="api-and-client-library-decommissioning-examples"></a>API とクライアント ライブラリの使用停止の例

**v24 バージョンの SMS REST API をアプリケーションに統合しました。Azure Communication では v25 がリリースされています。**

これらの API の機能が停止し、v25 に強制的に更新される前に、3 年間警告が表示されます。 この更新ではコードの変更が必要になる場合があります。

**v2.02 バージョンの Calling クライアント ライブラリをアプリケーションに統合しました。Azure Communication では v2.05 がリリースされています。**

v2.05 バージョンの Calling クライアント ライブラリへの更新を、v2.05 のリリースから 12 か月以内に求められる場合があります。 これはコードを変更せず、成果物を単に置き換えるだけで済みます。v2.05 は v2 メジャー バージョンに含まれ、破壊的変更はないためです。

## <a name="next-steps"></a>次の手順

詳細については、次のクライアント ライブラリの概要を参照してください。

- [Calling クライアント ライブラリの概要](../concepts/voice-video-calling/calling-sdk-features.md)
- [Chat クライアント ライブラリの概要](../concepts/chat/sdk-features.md)
- [SMS クライアント ライブラリの概要](../concepts/telephony-sms/sdk-features.md)

Azure Communication Services の使用を開始するには:

- [Azure Communication のリソースを作成する](../quickstarts/create-communication-resource.md)
- [ユーザー アクセス トークン](../quickstarts/access-tokens.md)を生成する
