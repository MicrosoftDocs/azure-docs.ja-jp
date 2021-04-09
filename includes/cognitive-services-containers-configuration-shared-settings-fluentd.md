---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001184"
---
Fluentd は、ログの一元管理を実現するオープンソースのデータ コレクターです。 [Fluentd](https://www.fluentd.org) サーバーに対するコンテナーの接続は、`Fluentd` の設定によって管理されます。 コンテナーには、お客様のコンテナーでログ、および必要に応じてメトリック データを Fluentd サーバーに書き込むことができる Fluentd ログ プロバイダーが含まれます。

次の表に、`Fluentd` セクションでサポートされている構成設定について説明します。

| 名前 | データ型 | 説明 |
|------|-----------|-------------|
| `Host` | String | Fluentd サーバーの IP アドレスまたは DNS ホスト名。 |
| `Port` | Integer | Fluentd サーバーのポート。<br/> 既定値は 24224 です。 |
| `HeartbeatMs` | Integer | ハートビート間隔 (ミリ秒)。 この間隔が期限切れになるまでにイベント トラフィックが送信されなかった場合、ハートビートが Fluentd サーバーに送信されます。 既定値は、60000 ミリ秒 (1 分) です。 |
| `SendBufferSize` | Integer | 送信操作用に割り当てられたネットワーク バッファー領域 (バイト数)。 既定値は、32,768 バイト (32 キロバイト) です。 |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Fluentd サーバーとの SSL または TLS 接続を確立するためのタイムアウト (ミリ秒)。 既定値は、10000 ミリ秒 (10 秒) です。<br/> `UseTLS` が false に設定されている場合、この値は無視されます。 |
| `UseTLS` | Boolean | コンテナーで、Fluentd サーバーとの通信に SSL または TLS を使用する必要があるかどうかを示します。 既定値は false です。 |