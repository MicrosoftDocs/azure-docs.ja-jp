---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c60d2a9b13cce9251ff0f730081a9d677206770d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630128"
---
## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="for-windows"></a>Windows の場合

Windows OS ディスクは、[sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) ツールを使用して一般化されます。 後で OS を更新または再構成する場合は、sysprep を再び実行する必要があります。

> [!WARNING]
> 更新プログラムが自動的に実行される可能性があるため、sysprep を実行した後、デプロイされるまでは VM をオフにしてください。 このシャットダウンで、以降の更新プログラムによるオペレーティング システムまたはインストール済みサービスへのインスタンス固有の変更が行われなくなります。 sysprep の実行に関する詳細については、[VHD を一般化する手順](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)に関する記事を参照してください。

### <a name="for-linux"></a>Linux の場合

次のプロセスでは、Linux VM を一般化して別の VM として再デプロイします。 詳細については、「[仮想マシンまたは VHD のイメージを作成する方法](../../virtual-machines/linux/capture-image.md)」を参照してください。 「キャプチャしたイメージから VM を作成する」というセクションに達したら止めることができます。

1. Azure Linux エージェントを削除します。
    1. SSH クライアントを使って Linux VM に接続します。
    2. SSH ウィンドウで、コマンド `sudo waagent –deprovision+user` を入力します。
    3. 「Y」と入力して続行します (前のコマンドに -force パラメーターを追加すると、この確認手順を省略できます)。
    4. コマンドが完了したら、「**Exit**」と入力して SSH クライアントを閉じます。
2. 仮想マシンを停止します。
    1. Azure portal でリソース グループ (RG) を選択し、VM の割り当てを解除します。
    2. これで VM が一般化されたため、この VM ディスクを使用して新しい VM を作成できます。

### <a name="capture-image"></a>イメージをキャプチャする

VM の準備ができたら、Azure Shared Image Gallery でキャプチャできます。 キャプチャするには、次の手順に従います。

1. [Azure portal](https://ms.portal.azure.com/) で、使用している仮想マシンのページにアクセスします。
2. **[キャプチャ]** を選択します。
3. **[Shared Image Gallery にイメージを共有する]** で、 **[Yes, share it to a gallery as an image version]\(はい、ギャラリーにイメージ バージョンとして共有します\)** を選択します。
4. **[Operating system state]\(オペレーティング システムの状態\)** で、[一般化] を選択します。
5. ターゲット イメージ ギャラリーまたは **[新規作成]** を選択します。
6. ターゲット イメージ定義または **[新規作成]** を選択します。
7. イメージの **[バージョン番号]** を指定します。
8. **[確認と作成]** を選択して、選択内容を確認します。
9. 検証に合格したら、 **[作成]** を選択します。

公開するには、公開元アカウントに SIG イメージへの Owner アクセス権が必要です。 アクセス権を付与するには、次のようにします。

1. Shared Image Gallery にアクセスします。
2. 左側のパネルで **[アクセス制御 (IAM)]** を選択します。
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します。
4. **[ロール]** または **[所有者]** を選択します。
5. **[アクセス権の割り当て先]** で、 **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。
6. イメージを公開するユーザーの Azure メールを選択します。
7. **[保存]** を選択します。

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="ロール割り当ての追加ウィンドウを表示します。":::

> [!NOTE]
> パートナー センターで SIG イメージを公開できるようになったため、SAS URI を生成する必要はありません。 ただし、それでも SAS URI の生成手順を参照する必要がある場合は、「[VM イメージの SAS URI を生成する方法](../azure-vm-get-sas-uri.md)」を参照してください。
