---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 95bd83575809f6ecda716ff751b47b7bb499cae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85073357"
---
## <a name="configure-an-environment-variable-for-authentication"></a>認証用の環境変数を構成する

アプリケーションは、使用する Cognitive Services へのアクセスを認証する必要があります。 認証するために、Azure リソースのキーを格納する環境変数を作成することをお勧めします。 

キーを用意したら、アプリケーションを実行しているローカル マシンの新しい環境変数に書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `your-key` をお使いのリソースのいずれかのキーで置き換えます。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動します。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macos"></a>[macOS](#tab/unix)

次のように .bash_profile を編集し、環境変数を追加します。

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bash_profile` を実行します。

***
