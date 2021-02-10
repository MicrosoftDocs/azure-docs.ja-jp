---
title: Azure Communication Services 通話クライアント ライブラリの概要
titleSuffix: An Azure Communication Services concept document
description: 通話クライアント ライブラリの概要について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f949747649e351947ba65666fdb57c0beac3aa23
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492112"
---
# <a name="calling-client-library-overview"></a>通話クライアント ライブラリの概要

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

通話クライアント ライブラリには、"*クライアント*" と "*サービス*" に対する 2 つの異なるファミリがあります。 現在使用可能なクライアント ライブラリは、Web サイトとネイティブ アプリのエンドユーザー エクスペリエンスを対象としています。

サービス クライアント ライブラリはまだ使用可能ではなく、ボットやその他のサービスとの統合に適した生の音声およびビデオのデータ プレーンへのアクセスを提供します。

## <a name="calling-client-library-capabilities"></a>通話クライアント ライブラリの機能

次の一覧は、 Azure Communication Services の通話クライアント ライブラリで現在使用できる機能のセットを示しています。

| 機能のグループ | 機能                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| コア機能 | 2 人のユーザー間で一対一の通話を行う                                                                           | ✔️   | ✔️            | ✔️  
|                   | 3 人以上のユーザーでグループ通話を行う (最大 350 ユーザー)                                                       | ✔️   | ✔️            | ✔️ 
|                   | 2 人のユーザーによる一対一の通話を 3 人以上のユーザーによるグループ通話に昇格させる                                 | ✔️   | ✔️            | ✔️ 
|                   | 開始後にグループ通話に参加する                                                                              | ✔️   | ✔️            | ✔️ 
|                   | 別の VoIP 参加者を招待して、進行中のグループ通話に参加させる                                                       | ✔️   | ✔️            | ✔️
|                   | ビデオをオンまたはオフにする                                                         | ✔️   | ✔️            | ✔️ 
|                   | マイクをミュート/ミュート解除する                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | カメラを切り替える                                                                                              | ✔️   | ✔️            | ✔️           
|                   | ローカルの保留/保留解除                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | アクティブなスピーカー                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 通話用のスピーカーを選択する                                                                                            | ✔️   | ✔️            | ✔️           
|                   | 通話用のマイクを選択する                                                                                         | ✔️   | ✔️            | ✔️           
|                   | 参加者の状態を表示する<br/>*アイドル状態、初期メディア、接続中、接続、保留中、ロビー、切断*         | ✔️   | ✔️            | ✔️           
|                   | 通話の状態を表示する<br/>*初期メディア、着信、接続中、呼び出し中、接続、保留、切断中、切断* | ✔️   | ✔️            | ✔️           
|                   | 参加者がミュートされているかどうかを表示する                                                                                      | ✔️   | ✔️            | ✔️           
|                   | 参加者が通話を終了した理由を表示する                                                                       | ✔️   | ✔️            | ✔️     
| 画面共有    | アプリケーション内から画面全体を共有する                                                                 | ✔️   | ❌            | ❌           
|                   | (実行中のアプリケーションの一覧から) 特定のアプリケーションを共有する                                                | ✔️   | ❌            | ❌           
|                   | 開いているタブの一覧から Web ブラウザー タブを共有する                                                                  | ✔️   | ❌            | ❌           
|                   | 参加者はリモート スクリーン共有を表示可能                                                                            | ✔️   | ✔️            | ✔️         
| 名簿            | 参加者の一覧表示                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | 参加者の削除                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | PSTN 参加者と一対一の通話を行う                                                                     | ✔️   | ✔️            | ✔️   
|                   | PSTN 参加者とグループ通話を行う                                                                           | ✔️   | ✔️            | ✔️
|                   | PSTN 参加者との一対一の通話をグループ通話に昇格させる                                                 | ✔️   | ✔️            | ✔️
|                   | グループ通話から PSTN 参加者としてダイヤルアウトする                                                                    | ✔️   | ✔️            | ✔️   
| 全般           | オーディオ テスト サービスを使用して、マイク、スピーカー、およびカメラをテストする (8:echo123 に通話することで利用可能)                   |  ✔️  | ✔️            | ✔️   

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>OS とブラウザーによる JavaScript 通話クライアント ライブラリのサポート

次の表は、現在使用可能な、サポートされているブラウザーとバージョンのセットを示しています。

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **通話クライアント ライブラリ** | Chrome*、新しい Edge | Chrome *、Safari** | Chrome*  | Chrome* | Chrome* | Safari** | Safari** |


\* 前の 2 つのリリースに加えて Chrome の最新バージョンがサポートされていることに注意してください。<br/>

** Safari バージョン 13.1 以降がサポートされていることに注意してください。 Safari macOS の発信ビデオはまだサポートされていませんが、iOS ではサポートされています。 発信画面の共有は、デスクトップ iOS でのみサポートされています。 現在、Safari では 1 対 1 およびグループの通話は利用できません。

## <a name="calling-client---browser-security-model"></a>通話クライアント - ブラウザーのセキュリティ モデル

### <a name="user-webrtc-over-https"></a>HTTPS 経由のユーザー WebRTC

`getUserMedia` などの WebRTC API では、これらの API を呼び出すアプリが HTTPS 経由で提供される必要があります。

ローカル開発の場合は、`http://localhost` を使用できます。

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Communication Services 通話 SDK を iframe に埋め込む

新しい[アクセス許可ポリシー (機能ポリシーとも呼ばれます)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) は、さまざまなブラウザーで採用されつつあります。 このポリシーは、アプリケーションがクロスオリジン iframe 要素を介してデバイスのカメラとマイクにアクセスする方法を制御することで通話シナリオに影響を与えます。

iframe を使用して別のドメインからアプリの一部をホストする場合は、適切な値を指定した `allow` 属性を iframe に追加する必要があります。

たとえば、次の iframe では、カメラとマイクの両方へのアクセスを許可します。

```html
<iframe allow="camera *; microphone *">
```

## <a name="calling-client-library-streaming-support"></a>通話クライアント ライブラリでのストリーミングのサポート
Communication Services の通話クライアント ライブラリでは、次のストリーミング構成をサポートしています。

|           |Web | Android、iOS|
|-----------|----|------------|
|**同時に送信できる発信ストリームの数** |1 つのビデオと 1 つの画面の共有 | 1 つのビデオ |
|**同時に表示できる着信ストリームの数** |1 つのビデオと 1 つの画面の共有| 6 つのビデオと 1 つの画面の共有 |


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話を開始する](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

詳細については、次の記事を参照してください。
- 一般的な[通話のフロー](../call-flows.md)を理解する
- [通話の種類](../voice-video-calling/about-call-types.md)について学習する
- [PSTN ソリューションを計画する](../telephony-sms/plan-solution.md)
