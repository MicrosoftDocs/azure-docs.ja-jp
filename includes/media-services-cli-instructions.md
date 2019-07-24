---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 8439da94c770bee313a1ae1d1da5df30683cd2ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57964207"
---
## <a name="cli-shell"></a>CLI シェル

CLI コマンドを実行するには、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を使用することをお勧めします。 **Cloud Shell** は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 Cloud Shell には一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Azure Cloud Shell には、業務に最適のシェル操作を選択できる柔軟性があります。 Linux ユーザーは Bash を、Windows ユーザーは PowerShell を選ぶことができます。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 

### <a name="login"></a>ログイン

(クラウドまたはローカルで) CLI シェルを使い始めるには、`az login` を実行して Azure との接続を作成する必要があります。

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザー ページを開いて、お使いのブラウザーで https://aka.ms/devicelogin に移動した後、コマンド ラインの指示に従って承認コードを入力します。

### <a name="specify-location-of-files"></a>ファイルの場所を指定する

多くの Media Services CLI コマンドでは、ファイル名と共にパラメーターを渡すことができます。 **Cloud Shell** を使用している場合は、(Bash または PowerShell を使用して) ファイルを clouddrive にアップロードできます。 

![ファイルをアップロードする]

ローカル CLI を使用しているか **Cloud Shell** を使用しているかにかかわらず、使用している OS または Cloud Shell (Bash または PowerShell) に従ってファイルのパスを指定する必要があります。 以下に例をいくつか示します。

ファイルの相対パス (すべての OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac および Windows の OS の絶対ファイル パス

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

コマンドでファイルへのパスが求められた場合は、`{file}` を使用します。 たとえば、「 `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json` 」のように入力します。 <br/> 指定したファイルをコマンドで読み込む場合は、`@{file}` を使用します。 たとえば、「 `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json` 」のように入力します。

[ファイルをアップロードする]: ./media/media-services-cli/upload-download-files.png
