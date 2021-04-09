---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666720"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl クライアント バイナリをダウンロードしてインストールする

Linux または [Windows Subsystem for Linux][install-wsl] 上の bash ベースのシェルで、次のように `curl` を使用して Istio のリリースをダウンロードし、`tar` で解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

`istioctl` クライアント バイナリはクライアント コンピューターで実行され、これを使用して Istio を AKS クラスターにインストールして管理できます。 Linux または [Windows Subsystem for Linux][install-wsl] で bash ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Istio `istioctl` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
