---
title: Azure Communication Services の Chat クライアント ライブラリの概要
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services の Chat クライアント ライブラリについて説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656109"
---
# <a name="chat-client-library-overview"></a>Chat クライアント ライブラリの概要  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Azure Communication Services の Chat クライアント ライブラリを使用して、アプリケーションにリアルタイムのリッチ チャットを追加できます。
    
## <a name="chat-client-library-capabilities"></a>Chat クライアント ライブラリの機能 

次の一覧は、Communication Services の Chat クライアント ライブラリで現在使用できる機能のセットを示しています。  

| 機能のグループ | 機能 | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| コア機能 | 2 人以上のユーザー (最大 250 人のユーザー) 間のチャット スレッドを作成する                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | チャット スレッドのトピックを更新する                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | チャット スレッドの参加者を追加または削除する                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 追加する参加者とチャット メッセージの履歴を共有するかどうかを選択する                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | チャット スレッドの参加者の一覧を取得する                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | チャット スレッドを削除する                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Communication ユーザーを指定して、そのユーザーが属するチャット スレッドの一覧を取得する                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 特定のチャット スレッドに関する情報を取得する                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | チャット スレッドでメッセージを送受信する                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 送信されたメッセージの内容を編集する                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | メッセージを削除する                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | チャットの他の参加者が確認済みメッセージの開封確認 <br/> *チャット スレッドの参加者数が 20 を超える場合は使用できません*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 参加者がチャット スレッドにメッセージを入力中であることを示す通知を受け取る <br/> *チャット スレッドのメンバー数が 20 を超える場合は使用できません*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | チャット スレッド内のすべてのメッセージを取得する <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | メッセージ コンテンツの一部として Unicode の絵文字を送信する                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|リアルタイムのシグナリング (独自のシグナリング パッケージで対応**)|  チャット アプの受信メッセージおよびその他の操作の更新をリアルタイムで取得するように登録する。 リアルタイムのシグナリングでサポートされている更新の一覧を確認するには、「[チャットに関する概念](concepts.md#real-time-signaling)」をご覧ください                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Event Grid のサポート             | Azure Event Grid との統合を使用して通信サービスを構成し、チャット アクティビティに基づいてビジネス ロジックを実行したり、カスタム プッシュ通知サービスをプラグインしたりする   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| 監視        | Azure portal で出力された API 要求メトリックを使用してダッシュボードを構築し、チャット アプリの正常性を監視し、異常を検出するアラートを設定する      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | 監視と診断のためにチャット操作ログを受信するするように Communication Services のリソースを構成する          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


**独自のシグナリング パッケージは、Web ソケットを使用して実装します。 Web ソケットがサポートされていない場合は、ロング ポーリングにフォールバックされます。  

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>OS とブラウザーによる JavaScript チャット クライアント ライブラリのサポート 

次の表は、現在使用可能な、サポートされているブラウザーとバージョンのセットを示しています。
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **チャット クライアント ライブラリ** | Firefox *、Chrome*、新しい Microsoft Edge | Firefox *、Chrome*、Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

\* 前の 2 つのリリースに加えて最新バージョンがサポートされていることに注意してください。<br/>   

## <a name="next-steps"></a>次の手順   

> [!div class="nextstepaction"] 
> [チャットを開始する](../../quickstarts/chat/get-started.md)    

次のドキュメントもご覧ください。  
- [チャットの概念](../chat/concepts.md)について理解する