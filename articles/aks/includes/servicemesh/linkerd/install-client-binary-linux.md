---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86277713"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd の linkerd クライアント バイナリをダウンロードしてインストールする

Linux または [Windows Subsystem for Linux][install-wsl] の bash ベースのシェルで `curl` を使用して、次のように Linkerd のリリースをダウンロードします。

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Linkerd サービス メッシュの対話型操作が可能になります。 Linux または [Windows Subsystem for Linux][install-wsl] で bash ベースのシェルで Linkerd `linkerd` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`linkerd` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Linkerd `linkerd` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
