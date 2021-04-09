---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563243"
---
| 使用モデル | キャッシュ モード | バックエンド検証 | ライトバックの最大遅延 |
|--|--|--|--|
| 負荷が高く、頻度の低い書き込みを読み取ります | Read | なし | なし |
| 書き込みが 15% を超えています | 読み取り/書き込み | 8 時間 | 20 分 |
| クライアントはキャッシュをバイパス | Read | 30 秒 | なし |
| 書き込みが 15% を超え、頻繁なバックエンド チェックが発生しています (30 秒) | 読み取り/書き込み | 30 秒 | 20 分 |
| 書き込みが 15% を超え、頻繁なバックエンド チェックが発生しています (60 秒) | 読み取り/書き込み | 60 秒 | 20 分 |
| 書き込みが 15% を超え、頻繁なライトバックが発生しています | 読み取り/書き込み | 30 秒 | 30 秒 |
| 読み取りの負荷が高く、バッキング サーバーの確認が 3 時間ごとに行われます | Read | 3 時間 | なし |
