---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274621"
---
## <a name="configure-an-environment-variable-for-authentication"></a>認証用の環境変数を構成する

アプリケーションは、使用する Cognitive Services へのアクセスを認証する必要があります。 認証するために、Azure リソースのキーを格納する環境変数を作成することをお勧めします。 

キーを用意したら、アプリケーションを実行しているローカル マシンの新しい環境変数に書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `your-key` をお使いのリソースのいずれかのキーで置き換えます。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動します。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

次のように .bash_profile を編集し、環境変数を追加します。

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

***