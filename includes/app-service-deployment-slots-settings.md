---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623708"
---
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 構成要素には、スワップを経ても内容が反映される (スロット固有でない) ものもあれば、スワップ後に同じスロットに残されている (スロット固有の) ものもあります。 次の一覧では、スロットのスワップ時に変更される設定を示します。

**スワップされる設定**:

* 一般設定 (フレームワーク バージョン、32/64 ビット、Web ソケットなど)
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* 監視と診断の設定
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* Virtual Network 統合 *
* サービス エンドポイント *
* Azure Content Delivery Network *

アスタリスク (*) 記号付きの機能は、スロット固有とされる予定です。 

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* プライベート証明書と SSL のバインド
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* プロトコル設定 (HTTPS、TLS バージョン、クライアント証明書)
* 診断ログの設定
* クロスオリジン リソース共有 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->