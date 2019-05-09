---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54841433"
---
|パラメーター名| type | 説明| 指定できる値|
|-|-|-|-|
| /ServerMode|必須|構成サーバーとプロセス サーバーの両方をインストールするか、プロセス サーバーだけをインストールするかを指定します。|CS<br>PS|
|/InstallLocation|必須|コンポーネントがインストールされているフォルダー。| コンピューター上の任意のフォルダー|
|/MySQLCredsFilePath|必須|MySQL サーバーの資格情報が保存されているファイルのパス。|ファイルは下記の形式である必要があります。|
|/VaultCredsFilePath|必須|コンテナーの資格情報ファイルのパス。|有効なファイル パス|
|/EnvType|必須|保護する環境の種類 |VMware<br>NonVMware|
|/PSIP|必須|レプリケーション データの転送に使用する NIC の IP アドレス。| 任意の有効な IP アドレス|
|/CSIP|必須|構成サーバーがリッスンする NIC の IP アドレス。| 任意の有効な IP アドレス|
|/PassphraseFilePath|必須|パスフレーズ ファイルの場所の完全パス。|有効なファイル パス|
|/BypassProxy|省略可能|構成サーバーがプロキシを介さずに Azure に接続することを指定します。|この値は Venu から取得します。|
|/ProxySettingsFilePath|省略可能|プロキシ設定 (認証を必要とする既定のプロキシ、またはカスタム プロキシ)。|ファイルは下記の形式である必要があります。|
|DataTransferSecurePort|省略可能|レプリケーション データに使用する PSIP のポート番号。| 有効なポート番号 (既定値は 9433)|
|/SkipSpaceCheck|省略可能|キャッシュ ディスクの領域チェックをスキップします。| |
|/AcceptThirdpartyEULA|必須|サード パーティのライセンス条項への同意を意味するフラグ。| |
|/ShowThirdpartyEULA|省略可能|サード パーティのライセンス条項を表示します。 入力として提供された場合、他のすべてのパラメーターが無視されます。| |
