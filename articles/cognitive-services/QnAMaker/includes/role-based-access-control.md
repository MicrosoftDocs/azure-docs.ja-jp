---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665922"
---
コラボレーションのために次のロールが用意されています。

|Role|機能|API アクセス|API のアクセス許可|
|--|--|--|--|
|所有者|All|認証キー|All|
|Contributor|ロールに新しいメンバーを追加する機能を除く "すべて"|認証キー|ロールに新しいメンバーを追加する機能を除く "すべて"|
|QnA Maker Read (QnA Maker の読み取り)<br>(読み取り)|エクスポート、ダウンロード<br>テスト|ベアラー トークン|1.KB のダウンロード API<br>2.ユーザーの KB の一覧表示 API<br>3.ナレッジ ベースの詳細を取得する<br>4.変更をダウンロードする<br>回答を生成する |
|QnA Maker Editor (QnA Maker エディター)<br>(読み取り、書き込み)|エクスポート、ダウンロード<br>テスト<br>KB の更新<br>KB のエクスポート<br>KB のインポート<br>KB の置換<br>KB の作成|ベアラー トークン|1.KB の作成 API<br>2.KB の更新 API<br>3.KB の置換 API<br>4.変更の置換<br>5."トレーニング API" [新しいサービス モデル v5 内]|
|Cognitive Service User (コグニティブ サービス ユーザー)<br>(読み取り、書き込み、公開)|All|ベアラー トークン|All|