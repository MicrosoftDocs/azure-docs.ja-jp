---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81735284"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd の linkerd クライアント バイナリをダウンロードしてインストールする

Windows の PowerShell ベースのシェルで `Invoke-WebRequest` を使用して、次のように Linkerd のリリースをダウンロードします。

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Linkerd サービス メッシュの対話型操作が可能になります。 Windows で PowerShell ベースのシェルで Linkerd `linkerd` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、Linkerd フォルダーに `linkerd` クライアント バイナリがコピーされ、即座 (現在のシェル内で) かつ永続的 (シェルの再起動により) に `PATH` 経由で利用できるようになります。 これらのコマンドの実行には昇格された (管理者) 特権は必要ありません。また、シェルを再起動する必要はありません。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```