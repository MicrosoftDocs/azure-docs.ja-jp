---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c92d6569e3c92d3bad3575599283c7796bd78225
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068624"
---
## <a name="prerequisites"></a>前提条件

このチュートリアルの唯一の前提条件は、Azure Speech サブスクリプションです。 まだお持ちでない場合は、新しいサブスクリプションの作成に関する[ガイド](../get-started.md#new-resource)を参照してください。

## <a name="download-and-install"></a>ダウンロードしてインストールする

#### <a name="windows-install"></a>[Windows のインストール](#tab/windowsinstall)

Windows に Speech CLI をインストールするには、次の手順に従います。

1. [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) または [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) のいずれかをインストールします。
2. Speech CLI [ZIP アーカイブ](https://aka.ms/speech/spx-zips.zip)をダウンロードして、抽出します。
3. ダウンロードから抽出したルート ディレクトリ `spx-zips` にアクセスし、必要なサブディレクトリを抽出します (.NET Framework 4.7 の場合は `spx-net471`、x64 CPU での .NET Core 3.0 の場合は `spx-netcore-win-x64`)。

コマンド プロンプトで、ディレクトリをこの場所に変更し、「`spx`」と入力して、Speech CLI のヘルプを表示します。

> [!NOTE]
> Windows の Speech CLI では、ローカル コンピューター上のコマンド プロンプトで使用できるフォントのみを表示できます。
> [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701)では、Speech CLI によって対話的に生成されるすべてのフォントがサポートされます。
> ファイルに出力すると、メモ帳などのテキスト エディターや、Microsoft Edge などの Web ブラウザーでも、すべてのフォントを表示できます。

> [!NOTE]
> Powershell では、コマンドの検索時にローカル ディレクトリがチェックされません。 Powershell で、ディレクトリを `spx` の場所に変更し、`.\spx` と入力してツールを呼び出します。
> このディレクトリをパスに追加すると、Powershell と Windows コマンド プロンプトが `.\` プレフィックスを含めずにディレクトリから `spx` を検索します。

#### <a name="linux-install"></a>[Linux のインストール](#tab/linuxinstall)

Linux の x64 CPU で Speech CLI をインストールするには、次の手順に従います。

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) をインストールします。
2. Speech CLI [ZIP アーカイブ](https://aka.ms/speech/spx-zips.zip)をダウンロードして、抽出します。
3. ダウンロードから抽出したルートディレクトリ `spx-zips` にアクセスし、`spx-netcore-30-linux-x64` を新しい `~/spx` ディレクトリに抽出します。
4. ターミナルで、次のコマンドを入力します。
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

「`spx`」と入力して、Speech CLI のヘルプを表示します。

#### <a name="docker-install"></a>[Docker のインストール](#tab/dockerinstall)

Docker コンテナー内に Speech CLI をインストールするには、次の手順に従います。

1. [プラットフォーム用の Docker Desktop](https://www.docker.com/get-started) をインストールして実行します。
1. 新しいコマンド プロンプトまたはターミナルで、次のコマンドを入力します: `docker pull msftspeech/spx`
1. 次のコマンドを入力します。 Speech CLI のヘルプ情報が表示されます: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>コンテナーにディレクトリをマウントする

Speech CLI ツールは、構成設定をファイルとして保存し、ヘルプ コマンドを除く任意のコマンドを実行するときに、これらのファイルを読み込みます。
Docker コンテナー内で Speech CLI を使用する際は、コンテナーからローカル ディレクトリをマウントする必要があります。これにより、ツールで構成設定を格納したり検索したりできるようになります。また、音声のオーディオ ファイルなど、コマンドに必要なファイルの読み取りまたは書き込みを行うことができるようになります。

Windows の場合は、次のコマンドを入力して、Speech CLI がコンテナー内から使用できるローカル ディレクトリを作成します。

`mkdir c:\spx-data`

Linux または Mac の場合は、ターミナルで次のコマンドを入力してディレクトリを作成し、その絶対パスを確認します。

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Speech CLI を呼び出すときは、この絶対パスを使用します。

### <a name="run-speech-cli-in-the-container"></a>コンテナーで Speech CLI を実行する

このドキュメントでは、Docker 以外のインストールで使用される Speech CLI `spx` コマンドについて説明します。
Docker コンテナーで `spx` コマンドを呼び出す場合は、Speech CLI が構成値を格納および検索してファイルを読み書きできるように、コンテナー内のディレクトリをファイル システムにマウントする必要があります。
Windows の場合、コマンドは次のように始まります。

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

Linux または Mac の場合、コマンドは次のように始まります。

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> `/ABSOLUTE_PATH` を、前のセクションの `pwd` コマンドによって表示された絶対パスで置き換えます。

コンテナーにインストールされている `spx` コマンドを使用するには、必ず上記の完全なコマンドを入力し、その後に要求のパラメーターを入力します。
たとえば、Windows では、次のコマンドによってキーが設定されます。

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Docker コンテナー内で Speech CLI を実行するときは、コンピューターのマイクまたはスピーカーを使用することはできません。
> これらのデバイスを使用するには、Docker コンテナーの外部で記録または再生できるように、Speech CLI との間でオーディオ ファイルを受け渡します。
> Speech CLI ツールは、前述の手順で設定したローカル ディレクトリにアクセスできます。

***

## <a name="create-subscription-config"></a>サブスクリプション構成の作成

Speech CLI の使用を開始するには、最初に、Speech サブスクリプション キーとリージョン情報を入力する必要があります。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。 サブスクリプション キーとリージョン識別子 (たとえば、 `eastus`、`westus`) を入手したら、次のコマンドを実行します。

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

これで、今後の SPX 要求のためのサブスクリプション認証が格納されるようになりました。 これらの格納されている値のいずれかを削除する必要がある場合は、`spx config @region --clear` または `spx config @key --clear` を実行します。
