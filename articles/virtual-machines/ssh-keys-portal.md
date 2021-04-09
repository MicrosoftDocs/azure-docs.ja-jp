---
title: Azure portal で SSH キーを作成する
description: Linux VM を接続するために、Azure portal で SSH キーを生成して格納する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88929442"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Azure portal で SSH キーを生成して格納する

ポータルを使用して Linux VM を頻繁にデプロイする場合は、SSH キーをポータルで直接作成するか、コンピューターからアップロードすることで、SSH キーの利用をより簡単にできます。

最初に VM を作成するときに SSH キーを作成し、他の VM に再利用することができます。 または、別個に SSH キーを作成することができます。そうすることで、Azure に格納されるキーのセットを用意して、組織のニーズに合わせます。 

既存のキーがあり、ポータルで簡単に使用できるようにする場合は、再利用のためにそれらを Azure にアップロードして格納することができます。

Linux VM で SSH キーを作成し、使用することの詳細については、[SSH キーを使用して Linux VM に接続する](./linux/ssh-from-windows.md)ことに関するページを参照してください。

## <a name="generate-new-keys"></a>新しいキーを生成する

1. [Azure portal](https://portal.azure.com) を開きます。

1. ページ上部で、「*SSH*」と入力して検索します。 **[Marketplace]** で、 **[SSH キー]** を選択します。

1. **[SSH キー]** ページで、 **[作成]** を選択します。

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="新しいリソース グループを作成し、SSH キー ペアを生成する":::

1. **[リソース グループ]** で、 **[新規作成]** を選択して、キーを格納するための新しいリソース グループを作成します。 リソース グループの名前を入力し、 **[OK]** を選択します。

1. **[リージョン]** で、キーを格納するリージョンを選択します。 キーはどのリージョンでも使用できます。これはキーが格納されるリージョンにすぎません。

1. **[キー ペアの名前]** にキーの名前を入力します。

1. **[SSH 公開キー ソース]** で、 **[公開キー ソースの生成]** を選択します。 

1. 完了したら、 **[確認および作成]** を選択します。

1. 検証に合格したら、 **[作成]** を選択します。

1. 次にポップアップ ウィンドウが表示されるので、 **[秘密キーをダウンロードしてリソースを作成する]** を選択します。 これで、SSH キーが .pem ファイルとしてダウンロードされます。

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="秘密キーを .pem ファイルとしてダウンロードする":::

1. .pem ファイルがダウンロードされたら、SSH クライアントから容易に参照できるコンピューター上の任意の場所に、このファイルを移動できます。


## <a name="connect-to-the-vm"></a>VM に接続します

ローカル コンピューターで PowerShell プロンプトを開き、次のように入力します。

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

たとえば、「`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`」と入力します。


## <a name="upload-an-ssh-key"></a>SSH キーをアップロードする

SSH 公開キーをアップロードして Azure に格納することもできます。 SSH キー ペアを作成する方法については、[SSH キーを使用して Linux VM に接続する](./linux/ssh-from-windows.md)ことに関するページを参照してください。

1. [Azure portal](https://portal.azure.com) を開きます。

1. ページ上部で、「*SSH*」と入力して検索します。 * *[Marketplace]* で、 **[SSH キー]** を選択します。

1. **[SSH キー]** ページで、 **[作成]** を選択します。

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Azure に格納される SSH 公開キーをアップロードする":::

1. **[リソース グループ]** で、 **[新規作成]** を選択して、キーを格納するための新しいリソース グループを作成します。 リソース グループの名前を入力し、 **[OK]** を選択します。

1. **[リージョン]** で、キーを格納するリージョンを選択します。 キーはどのリージョンでも使用できます。これはキーが格納されるリージョンにすぎません。

1. **[キー ペアの名前]** にキーの名前を入力します。

1. **[SSH 公開キー ソース]** で、 **[既存の公開キーをアップロードする]** を選択します。 

1. 公開キーの内容全体を **[キーのアップロード]** に貼り付けてから、 **[確認と作成]** を選択します。

1. 検証が完了した後、 **[作成]** を選択します。 

キーがアップロードされたら、VM を作成するときに、そのキーを使用することを選択できます。

## <a name="list-keys"></a>キーのリスト

ポータルで作成された SSH キーは、リソースとして保存されるので、リソース ビューをフィルター処理してそのすべての SSH キーを表示できます。

1. ポータルで **[すべてのリソース]** を選択します。
1. フィルターで **[タイプ]** を選択し、 **[すべて選択]** オプションを選択解除して一覧をクリアします。
1. フィルターに「**SSH**」と入力し、 **[SSH キー]** を選択します。

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="すべての SSH キーを表示するように一覧をフィルター処理する方法を示したスクリーンショット。":::

## <a name="get-the-public-key"></a>公開キーを取得する

公開キーが必要な場合は、キーのポータル ページから簡単にコピーできます。 (最後のセクションのプロセスを使用して) キーを一覧表示し、一覧からキーを選択するだけです。 キーのページが開き、キーの横にある **[クリップボードにコピー]** アイコンをクリックしてコピーできます。

## <a name="next-steps"></a>次の手順

Azure VM で SSH キーを使用することの詳細については、[SSH キーを使用して Linux VM に接続する](./linux/ssh-from-windows.md)ことに関するページを参照してください。
