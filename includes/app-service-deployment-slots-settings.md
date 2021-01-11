---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131894"
---
別のデプロイ スロットから構成を複製する場合、複製された構成を編集することができます。 構成要素には、スワップを経ても内容が反映される (スロット固有でない) ものもあれば、スワップ後に同じスロットに残されている (スロット固有の) ものもあります。 次の一覧では、スロットのスワップ時に変更される設定を示します。

**スワップされる設定**:

* 一般設定 (フレームワーク バージョン、32/64 ビット、Web ソケットなど)
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* Virtual Network 統合 *
* サービス エンドポイント *
* Azure Content Delivery Network *

アスタリスク (*) 記号付きの機能は、スワップされない予定です。 

**スワップされない設定**:

* 発行エンドポイント
* カスタム ドメイン名
* パブリックでない証明書と TLS/SSL 設定
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* 診断設定
* クロスオリジン リソース共有 (CORS)

> [!NOTE]
> スワップされていない設定に適用されている特定のアプリ設定もスワップされません。 たとえば、診断の設定はスワップされないため、`WEBSITE_HTTPLOGGING_RETENTION_DAYS` や `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` などの関連するアプリ設定もスワップされません。これは、スロット設定として表示されない場合でも同様です。
>
