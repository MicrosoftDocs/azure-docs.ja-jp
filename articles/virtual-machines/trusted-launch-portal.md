---
title: 'プレビュー: トラステッド起動の VM をデプロイする'
description: トラステッド起動を使用する VM をデプロイします。
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: dec9c7581bbcf55196b04e0a76e9e61f81a27244
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582070"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>トラステッド起動を有効にして VM をデプロイする (プレビュー)

[トラステッド起動](trusted-launch.md)は、[第 2 世代](generation-2.md)の VM のセキュリティを向上させる手段です。 トラステッド起動を使用すると、vTPM やセキュア ブートのようなインフラストラクチャ テクノロジを組み合わせることによって、高度で永続的な攻撃手法から保護されます。

> [!IMPORTANT]
> トラステッド起動は、現在パブリック プレビュー段階にあります。
> 
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="deploy-using-the-portal"></a>ポータルを使用したデプロイ

トラステッド起動を有効にして仮想マシンを作成します。

1. Azure [Portal](https://aka.ms/TL_preview) にサインインします。
1. 「**仮想マシン**」を検索します。
1. **[サービス]** で、 **[仮想マシン]** を選択します。
1. **[仮想マシン]** ページで、 **[追加]** を選択してから、 **[仮想マシン]** を選択します。
1. **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認します。
1. **[リソース グループ]** で **[新規作成]** を選択し、リソース グループの名前を入力するか、ドロップダウンから既存のリソース グループを選択します。
1. **[インスタンスの詳細]** で、仮想マシンの名前を入力し、[トラステッド起動](trusted-launch.md#public-preview-limitations)がサポートされているリージョンを選択します。
1. **[イメージ]** で、[トラステッド起動がサポートされているイメージ](trusted-launch.md#public-preview-limitations)を選択します。 イメージの第 1 世代のバージョンしか表示されない場合がありますが、それでもかまわないので、次のステップに進みます。
1. ページの上部にある **[詳細設定]** タブを選択してそれに切り替えます。
1. **[VM の生成]** セクションまで下にスクロールし、 **[Gen 2]** を選択します。
1. 引き続き **[詳細設定]** タブで、 **[Trusted launch]\(トラステッド起動\)** まで下にスクロールして、 **[Trusted launch]\(トラステッド起動\)** チェック ボックスをオンにします。 これにより、さらに [Secure boot]\(セキュア ブート\) と [vTPM] の 2 つのオプションが表示されるようになります。 自分のデプロイに合った適切なオプションを選択します。

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="トラステッド起動のオプションを示すスクリーンショット。":::

1. **[基本]** タブに戻り、 **[イメージ]** の下に次のメッセージが表示されていることを確認します: "**This image supports trusted launch preview. (このイメージは、トラステッド起動のプレビューをサポートしています。) Configure in the Advanced tab ([詳細設定] タブで構成します)** "。これで、第 2 世代のイメージが選択されるはずです。

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="これがトラステッド起動をサポートする第 2 世代イメージであることを確認するメッセージを示すスクリーンショット。":::

1.  トラステッド起動がサポートされている VM サイズを選択します。 [サポートされているサイズ](trusted-launch.md#public-preview-limitations)の一覧を参照してください。
1.  **[管理者アカウント]** の情報を入力した後、 **[受信ポートの規則]** を指定します。
1.  ページの下部にある **[確認および作成]** を選択します
1.  **[仮想マシンの作成]** ページで、デプロイしようとしている VM の詳細を確認できます。 準備ができたら **[作成]** を選択します。

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="トラステッド起動のオプションが含まれる検証ページのスクリーンショット。":::


VM がデプロイされるまでに数分かかります。 

## <a name="deploy-using-a-template"></a>テンプレートを使用したデプロイ

クイックスタート テンプレートを使用して、トラステッド起動 VM をデプロイできます。

**Linux**:    
[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>表示および更新する

ポータルで VM の **[概要]** ページに移動することにより、既存の VM に対するトラステッド起動の構成を表示できます。

トラステッド起動の構成を変更するには、左側のメニューで **[設定]** セクションの **[構成]** を選択します。 **[Trusted Launch]\(トラステッド起動\)** セクションでは、セキュア ブートと vTPM を有効または無効にすることができます。 終わったら、ページの上部にある **[保存]** を選択します。 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="トラステッド起動の構成を変更する方法のスクリーンショット。":::

VM が実行中の場合、トラステッド起動の変更された構成を適用するために VM が再起動されることを示すメッセージが表示されます。 **[はい]** を選択した後、VM が再起動されて変更が有効になるまで待ちます。


## <a name="verify-secure-boot-and-vtpm"></a>セキュア ブートと vTPM を確認する

仮想マシンでセキュア ブートと vTPM が有効になっていることを検証できます。
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: セキュア ブートが実行されているかどうかを検証する

VM に SSH で接続して、次のコマンドを実行します。 

```bash
mokutil --sb-state
```

セキュア ブートが有効になっている場合、コマンドからは次が返されます。
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: vTPM が有効になっているかどうかを検証する

VM に SSH で接続します。 tpm0 デバイスが存在するかどうかを調べます。 

```bash
ls /dev/tpm0
```

vTPM が有効になっている場合、コマンドからは次が返されます。

```output
/dev/tpm0
```

vTPM が無効になっている場合、コマンドからは次が返されます。

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: セキュア ブートが実行されていることを検証する

リモート デスクトップを使用して VM に接続し、`msinfo32.exe` を実行します。

右側のペインで、セキュア ブートの状態が **[オン]** になっていることを確認します。

## <a name="enable-the-azure-security-center-experience"></a>Azure Security Center のエクスペリエンスを有効にする

トラステッド起動の VM に関する情報が Azure Security Center に表示されるようにするには、いくつかのポリシーを有効にする必要があります。 ポリシーを有効にする最も簡単な方法は、この [Resource Manager テンプレート](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies)をサブスクリプションにデプロイすることです。 

サブスクリプションにポリシーをデプロイするには、下のボタンを選択してください。

[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

テンプレートは、サブスクリプションごとに 1 回だけデプロイする必要があります。 サポートされているすべての VM に、`GuestAttestation` および `AzureSecurity` 拡張機能が自動的にインストールされます。 エラーが発生する場合は、もう一度テンプレートを再デプロイしてみてください。

トラステッド起動の VM で vTPM とセキュア ブートに関する推奨事項を入手するには、[サブスクリプションへのイニシアティブの追加](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)に関するページを参照してください。
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Linux でセキュア ブートに関するものに署名する

場合によっては、UEFI のセキュア ブートに関するものに署名することが必要になります。  たとえば、Ubuntu では「[How to sign things for Secure Boot](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot)」(セキュア ブートに関するものに署名する方法) の説明に従うことが必要になる場合があります。 このような場合、お使いの VM の MOK ユーティリティ登録キーを入力する必要があります。 これを行うには、Azure シリアル コンソールを使用して MOK ユーティリティにアクセスする必要があります。

1. Linux 用 Azure シリアル コンソールを有効にします。 詳細については、「[Linux 用シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-linux)」を参照してください。
1. [Azure Portal](https://portal.azure.com) にログインします。
1. **[仮想マシン]** を見つけ、一覧から自分の VM を選択します。
1. 左側のメニューで、 **[サポート + トラブルシューティング]** の **[シリアル コンソール]** を選択します。 右側にページが開いてシリアル コンソールが表示されます。
1. Azure シリアル コンソールを使用して VM にログオンします。 **[ログイン]** には、VM の作成時に使用したユーザー名を入力します。 たとえば、*azureuser* などです。 プロンプトが表示されたら、ユーザー名に関連付けられているパスワードを入力します。
1. ログインしたら、`mokutil` を使用して公開キーの `.der` ファイルをインポートします。

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. 「`sudo reboot`」と入力して、Azure シリアル コンソールからマシンを再起動します。 10 秒のカウントダウンが開始されます。
1. 上方向キーまたは下方向キーを押してカウントダウンを中断し、UEFI コンソール モードで待ちます。 タイマーを中断しないと、ブート プロセスが続行され、MOK のすべての変更が失われます。
1. MOK ユーティリティ メニューから適切なアクションを選択します。

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="シリアル コンソールの MOK 管理メニューで使用可能なオプションを示すスクリーンショット。":::


## <a name="next-steps"></a>次のステップ

[トラステッド起動](trusted-launch.md)と[第 2 世代](generation-2.md)の VM の詳細を確認します。