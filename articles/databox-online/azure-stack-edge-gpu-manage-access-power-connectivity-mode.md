---
title: Azure Stack Edge Pro GPU デバイスのアクセス、電源、接続モード | Microsoft Docs
description: Azure へのデータ転送に役立つ Azure Stack Edge Pro GPU デバイスのアクセス、電源、接続モードを管理する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443116"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU のアクセス、電源、接続モードを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、GPU 搭載 Azure Stack Edge Pro デバイスのアクセス、電源、接続モードを管理する方法について説明します。 これらの操作は、ローカル Web UI または Azure portal を使用して実行されます。

この記事は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。


この記事では、次のことについて説明します。

> [!div class="checklist"]
> * デバイスのアクセスを管理する
> * リソース アクセスの管理
> * 接続モードを管理する
> * 電源を管理する


## <a name="manage-device-access"></a>デバイスのアクセスを管理する

Azure Stack Edge Pro デバイスへのアクセスは、デバイスのパスワードを使用して制御されます。 パスワードは、ローカル Web UI を使用して変更できます。 また、Azure portal ではデバイスのパスワードをリセットすることもできます。

デバイス ディスク上のデータへのアクセスは、保存時の暗号化キーによっても制御されます。

### <a name="change-device-password"></a>デバイスのパスワードを変更する

デバイスのパスワードを変更するには、ローカル UI で次の手順のようにします。

1. ローカル Web UI で、 **[メンテナンス] > [パスワード]** に移動します。
2. 現在のパスワードを入力し、新しいパスワードを入力ます。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 新しいパスワードを確認入力します。

    ![パスワードの変更](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. **[パスワードの変更]** を選択します。
 
### <a name="reset-device-password"></a>デバイスのパスワードをリセットする

リセット ワークフローでは、ユーザーは古いパスワードを思い出す必要がなく、パスワードを忘れた場合に便利です。 このワークフローは、Azure portal で実行します。

1. Azure portal で、 **[概要] > [管理パスワードのリセット]** に移動します。

    ![スクリーンショットに、[デバイスのパスワードをリセットします] が選択されているデバイスが表示されています。](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. 新しいパスワードを入力し、それを確認します。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 **[リセット]** を選択します。

    ![パスワードのリセット 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>デバイス データへのアクセスを管理する

Azure Stack Edge Pro R と Azure Stack Edge Mini R デバイスでは、デバイス データへのアクセスは、デバイス　ドライブの保存時の暗号化キーを使用して制御されます。 保存時の暗号化用にデバイスを正常に構成すると、デバイスのローカル UI で、保存時の暗号化キーのローテーション オプションが使用できるようになります。 

この操作により、BitLocker ボリュームの `HcsData` と `HcsInternal`、およびデバイス上のすべての自己暗号化ドライブのキーを変更できます。

保存時の暗号化キーのローテーションを行うには、これらの手順に従います。

1. デバイスのローカル UI で、 **[開始]** ページに移動します。 **[セキュリティ]** タイルで、 **[保存時の暗号化:][Rotate keys]\(キーのローテーション\)** オプションを選択します。 このオプションは、保存時の暗号化キーを正常に構成した後でのみ使用できます。

    ![[開始] ページの [保存時の暗号化] で [Rotate keys]\(キーのローテーション\) を選択する](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. 独自の BitLocker キーを使用することも、システム生成のキーを使用することもできます。  

    独自のキーを指定する場合は、32 文字の Base-64 でエンコードされた文字列を入力します。 この入力は、保存時の暗号化を初めて構成するときに指定する内容に似ています。

    ![独自の保存時の暗号化キーを使用する](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    システム生成のキーの使用を選択することもできます。

    ![システム生成の保存時の暗号化キーを使用する](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. **[適用]** を選択します。 キー プロテクターがローテーションされます。

    ![新しい保存時の暗号化キーを適用する](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. キー ファイルをダウンロードして保存するように求めるメッセージが表示されたら、 **[Download and continue]\(ダウンロードして続行\)** を選択します。 

    ![キー ファイルをダウンロードして続行する](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    `.json` キー ファイルを安全な場所に保存します。 このファイルは、将来発生する可能性があるデバイスの復旧作業を容易にするために使用されます。

    ![スクリーンショットに、[デバイスのパスワードをリセットします] ダイアログ ボックスが表示されてます。](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>リソース アクセスの管理

Azure Stack Edge または Data Box Gateway、IoT Hub、Azure Storage リソースを作成するには、リソース グループ レベルで共同作成者以上のアクセス許可が必要です。 対応するリソース プロバイダーも登録する必要があります。 アクティブ化キーと資格情報が関係する操作には、Microsoft Graph API へのアクセス許可も必要です。 これらについては以降のセクションで説明します。 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API のアクセス許可の管理

Azure Stack Edge Pro デバイスのアクティブ化キーを生成するとき、または資格情報が必要な何らかの操作を実行するときは、Azure Active Directory Graph API へのアクセス許可が必要です。 資格情報が必要な操作には、次のものがあります。

-  ストレージ アカウントが関連付けられた共有の作成。
-  デバイス上の共有にアクセスできるユーザーの作成。

`Read all directory objects` を実行できる必要があるため、Active Directory テナントに対する `User` アクセス権が必要です。 Guest ユーザーは `Read all directory objects` を実行するアクセス許可がないため、使用できません。 ゲストである場合は、アクティブ化キーの生成、Azure Stack Edge Pro デバイス上の共有の作成、ユーザーの作成、Edge コンピューティング ロールの構成、デバイス パスワードのリセットなどの操作はすべて失敗します。

Microsoft Graph API へのアクセスをユーザーに提供する方法の詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」を参照してください。

### <a name="register-resource-providers"></a>リソース プロバイダーを登録する

(Azure Resource Manager モデルで) Azure でリソースをプロビジョニングするには、そのリソースの作成をサポートするリソース プロバイダーが必要です。 たとえば、仮想マシンをプロビジョニングするには、サブスクリプションで利用可能な 'Microsoft.Compute' リソース プロバイダーが必要です。
 
リソース プロバイダーはサブスクリプションのレベルで登録されます。 既定では、新しい Azure サブスクリプションには、一般的に使用されるリソース プロバイダーの一覧が事前登録されています。 'Microsoft.DataBoxEdge' のリソース プロバイダーは、この一覧に含まれていません。

これらのリソースのリソース プロバイダーが既に登録されている限り、ユーザーが所有者権限を持っているユーザーのリソース グループ内で 'Microsoft.DataBoxEdge' などのリソースをユーザーが作成できるようにするために、サブスクリプション レベルへのアクセス許可を付与する必要はありません。

何らかのリソースを作成しようとする前に、サブスクリプションにリソース プロバイダーが登録されていることを確認してください。 リソース プロバイダーが登録されていない場合、新しいリソースを作成しようとしているユーザーが、必要なリソース プロバイダーをサブスクリプション レベルで登録するための十分な権限を持っていることを確認する必要があります。 これも行われていない場合、次のエラーが表示されます。

次のリソース プロバイダーを登録するためのアクセス許可が *サブスクリプション \<Subscription name> にありません:Microsoft.DataBoxEdge*


現在のサブスクリプションに登録されているリソース プロバイダーの一覧を取得するには、次のコマンドを実行します。

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge Pro デバイスの場合、`Microsoft.DataBoxEdge` を登録する必要があります。 `Microsoft.DataBoxEdge` を登録するには、サブスクリプション管理者が次のコマンドを実行する必要があります。

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

リソース プロバイダーの登録方法の詳細については、[リソース プロバイダー登録エラーの解決](../azure-resource-manager/templates/error-register-resource-provider.md)に関する記事を参照してください。

## <a name="manage-connectivity-mode"></a>接続モードを管理する

デバイスは、既定の完全接続モードだけでなく、部分接続モードまたは完全切断モードで実行することもできます。 ここでは、これらの各モードについて説明します。

- **完全接続** - これは、デバイスが動作する通常の既定モードです。 このモードでは、データのクラウドのアップロードとダウンロードの両方が有効です。 Azure portal またはローカル Web UI を使用して、デバイスを管理できます。

- **部分切断** – このモードでは、デバイスは共有データをアップロードもダウンロードもできませんが、Azure portal からデバイスを管理することはできます。

    このモードは通常、従量制課金のサテライト ネットワークで使用され、目的はネットワーク帯域幅の消費量を最小限に抑えることです。 デバイス監視操作のため、最小限のネットワーク消費が発生する可能性があります。

- **切断** – このモードでは、デバイスはクラウドから完全に切断されており、クラウドのアップロードとダウンロードの両方が無効になっています。 デバイスは、ローカル Web UI からのみ管理できます。

    このモードは通常、デバイスをオフラインにするときに使用されます。

デバイスのモードを変更するには、次の手順のようにします。

1. デバイスのローカル Web UI で、 **[構成] > [クラウド]** に移動します。
2. デバイスを操作するモードをドロップダウン リストから選びます。 **[Fully connected]\(完全接続\)** 、 **[Partially connected]\(部分接続\)** 、 **[Fully disconnected]\(完全切断\)** から選択することができます。 デバイスを部分切断モードで実行するには、 **[Azure portal management]\(Azure portal 管理\)** を有効にします。

 
## <a name="manage-power"></a>電源を管理する

ローカル Web UI を使用して、物理デバイスをシャットダウンまたは再起動できます。 再起動する前に、データ サーバーの共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 この操作により、データ破損の可能性を最小限に抑えられます。

1. ローカル Web UI で、 **[メンテナンス] > [電源]** に移動します。
2. 何を行うかに応じて、 **[シャットダウン]** または **[再起動]** を選択します。

    ![電源設定](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 確認を求められたら、 **[はい]** を選択して続行します。

> [!NOTE]
> 物理デバイスをシャット ダウンした場合、デバイスの電源を入れるには、電源ボタンを押す必要があります。

## <a name="next-steps"></a>次のステップ

- [共有を管理する](azure-stack-edge-manage-shares.md)方法を確認します。