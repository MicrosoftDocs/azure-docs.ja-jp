---
title: "Azure モバイル エンゲージメント関連のトラブルシューティング ガイド - サービス"
description: "Azure モバイル エンゲージメント関連のトラブルシューティング ガイド"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01862d158f4771989ae6eb3b20fe8e080123a231
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshooting-guide-for-service-issues"></a>サービスの問題に関するトラブルシューティング ガイド
次に説明するのは、Azure モバイル エンゲージメントの実行方法に関して発生する可能性のある問題です。

## <a name="service-outages"></a>サービスの停止
### <a name="issue"></a>問題
* Azure モバイル エンゲージメント サービスの停止に起因すると思われる問題です。

### <a name="causes"></a>原因
* Azure モバイル エンゲージメント サービスの停止に起因すると思われる問題は、いくつかの異なる問題が原因で発生します。
  * すべての Azure モバイル エンゲージメントに元からシステム的に表れる単独の問題
  * サーバーの停止に起因する既知の問題 (サーバー ステータスに表示されないことがあります)。
  * スケジュールの遅延、ターゲティングのエラー、バッジ更新に関する問題、統計情報収集の停止、プッシュ機能の停止、API 機能の停止、新しいアプリやユーザーを作成できない、DNS エラー、デバイス上の UI、API、アプリのタイムアウト エラー。
  * クラウドの依存関係の停止。[Azure サービスの状態](http://status.azure.com/)
  * プッシュ通知サービス (PNS) の依存関係の停止
  * アプリ ストアの停止

1) 問題がシステムの問題であるかどうかをテストするには、同じ機能を、以下を変えてテストします。

* Azure Mobile Engagement 統合アプリケーション
* テスト デバイス
* テスト デバイスの OS バージョン
* キャンペーン
* 管理者のユーザー アカウント
* ブラウザー (Firefox、Chrome など)
* コンピューター

2) 問題が UI または API にのみ影響するかどうかをテストします。

* 同じ機能を、Azure Mobile Engagement UI と Azure Mobile Engagement API の両方からテストします。

3) 携帯電話ネットワークに問題があるかどうかをテストします。

* WIFI を使用してインターネットに接続し、3 G 携帯電話ネットワーク経由で接続している間にテストを行います。
* ファイアウォールがいずれかの Azure Mobile Engagement IP アドレスまたはポートをブロックしていないことを確認します。

4) デバイスに問題があるかどうかをテストします。

* デバイスが、別の Azure Mobile Engagement 統合アプリを使用して Azure Mobile Engagement に接続できるかどうかをテストします。
* Azure Mobile Engagement UI で確認できるイベント、ジョブ、およびクラッシュを電話から生成できることをテストします。 
* Azure Mobile Engagement UI から、デバイス ID に基づいてデバイスにプッシュ通知を送信できるかどうかをテストします。 

5) アプリに問題があるかどうかをテストします。

* エミュレーターをインストールし、物理デバイスの代わりにエミュレーターからアプリケーションをテストします。

6) エンド ユーザー デバイスへの OS のアップグレードに問題があるかどうかをテストします。解決するには、SDK のアップグレードが必要です。

* アプリケーションを、OS のバージョンが異なるさまざまなデバイスでテストします。
* 最新バージョンの SDK を使用していることを確認します。

## <a name="connectivity-and-incorrect-information-issues"></a>接続と誤った情報に関する問題
### <a name="issue"></a>問題
* Azure モバイル エンゲージメント UI にログインする際の問題。
* Azure モバイル エンゲージメント API との接続エラー。
* デバイス API を使用してアプリ情報タグをアップロードする際の問題。
* Azure モバイル エンゲージメントからログをダウンロードまたはデータをエクスポートする際の問題。
* 正しくない情報が Azure モバイル エンゲージメント UI に表示される。
* 正しくない情報が Azure モバイル エンゲージメント ログに表示される。

### <a name="causes"></a>原因
* ユーザー アカウントがタスクを実行するのに十分な権限があることを確認します。
* 問題が 1 台のコンピューターまたはローカル ネットワークに分離されてないことを確認します。
* Azure モバイル エンゲージメント サービスで障害が報告されていないことを確認します。
* アプリ情報タグのファイルがすべてのルールに従っていることを確認します。
  * UTF8 文字セットのみを使用します (ANSI 文字セットはサポートされていません)。
  * 区切り文字としてコンマ「,」を使用します (サービス要求を開いて、.csv の区切り文字をコンマ「,」からセミコロン「;」などの別の文字に変更するように要求できます)。
  * ブール値の「TRUE」と「FALSE」には、すべて小文字を使用します。
  * 最大ファイル サイズの 35 MB よりも小さいファイルを使用します。


