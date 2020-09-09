---
title: Azure Data Box Gateway デバイスのアクセス、電源、接続モード
description: Azure へのデータ転送に役立つ Azure Data Box Gateway デバイスのアクセス、電源、および接続モードを管理する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 98431e7a451aa54dfdee2126d4ce94b8b0b0fb84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339214"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Azure Data Box Gateway のアクセス、電源、接続モードを管理する

この記事では、Azure Data Box Gateway のアクセス、電源、および接続モードを管理する方法について説明します。 これらの操作は、ローカル Web UI または Azure portal を使用して実行されます。 

この記事では、次の方法について説明します。

> [!div class="checklist"]
>
> * デバイスのアクセスを管理する
> * 接続モードを管理する
> * 電源を管理する

## <a name="manage-device-access"></a>デバイスのアクセスを管理する

Data Box Gateway デバイスへのアクセスは、デバイスのパスワードを使用して制御されます。 パスワードは、ローカル Web UI を使用して変更できます。 また、Azure portal ではデバイスのパスワードをリセットすることもできます。

### <a name="change-device-password"></a>デバイスのパスワードを変更する

デバイスのパスワードを変更するには、ローカル UI で次の手順のようにします。

1. ローカル Web UI で、 **[メンテナンス] > [Password change]\(パスワード変更\)** に移動します。
2. 現在のパスワードを入力し、新しいパスワードを入力ます。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 新しいパスワードを確認入力します。

    ![パスワードの変更](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. **[パスワードの変更]** をクリックします。
 
### <a name="reset-device-password"></a>デバイスのパスワードをリセットする

リセット ワークフローでは、ユーザーは古いパスワードを思い出す必要がなく、パスワードを忘れた場合に便利です。 このワークフローは、Azure portal で実行します。

1. Azure portal で、 **[概要] > [管理パスワードのリセット]** に移動します。

    ![[パスワードのリセット]](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 新しいパスワードを入力し、それを確認します。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 **[リセット]** をクリックします。

    ![[パスワードのリセット]](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>リソース アクセスの管理

Azure Stack Edge または Data Box Gateway、IoT Hub、Azure Storage リソースを作成するには、リソース グループ レベルで共同作成者以上のアクセス許可が必要です。 対応するリソース プロバイダーも登録する必要があります。 アクティブ化キーと資格情報が関係する操作には、Azure Active Directory Graph API へのアクセス許可も必要です。 これらについては以降のセクションで説明します。

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API のアクセス許可の管理

Azure Stack Edge デバイスのアクティブ化キーを生成するとき、または資格情報が必要な何らかの操作を実行するときは、Microsoft Graph API のアクセス許可が必要です。 資格情報が必要な操作には、次のものがあります。

-  ストレージ アカウントが関連付けられた共有の作成。
-  デバイス上の共有にアクセスできるユーザーの作成。

`Read all directory objects` を実行できる必要があるため、Active Directory テナントに対する `User` アクセス権が必要です。 Guest ユーザーは `Read all directory objects` を実行するアクセス許可がないため、使用できません。 ゲストの場合、アクティブ化キーの生成、Azure Stack Edge デバイス上の共有の作成、ユーザーの作成などの操作はすべて失敗します。

Microsoft Graph API へのアクセスをユーザーに提供する方法の詳細については、「[Microsoft Graph のアクセス許可のリファレンス](https://docs.microsoft.com/graph/permissions-reference)」を参照してください。

### <a name="register-resource-providers"></a>リソース プロバイダーを登録する

(Azure Resource Manager モデルで) Azure でリソースをプロビジョニングするには、そのリソースの作成をサポートするリソース プロバイダーが必要です。 たとえば、仮想マシンをプロビジョニングするには、サブスクリプションで利用可能な 'Microsoft.Compute' リソース プロバイダーが必要です。
 
リソース プロバイダーはサブスクリプションのレベルで登録されます。 既定では、新しい Azure サブスクリプションには、一般的に使用されるリソース プロバイダーの一覧が事前登録されています。 'Microsoft.DataBoxEdge' のリソース プロバイダーは、この一覧に含まれていません。

これらのリソースのリソース プロバイダーが既に登録されている限り、ユーザーが所有者権限を持っているユーザーのリソース グループ内で 'Microsoft.DataBoxEdge' などのリソースをユーザーが作成できるようにするために、サブスクリプション レベルへのアクセス許可を付与する必要はありません。

何らかのリソースを作成しようとする前に、サブスクリプションにリソース プロバイダーが登録されていることを確認してください。 リソース プロバイダーが登録されていない場合、新しいリソースを作成しようとしているユーザーが、必要なリソース プロバイダーをサブスクリプション レベルで登録するための十分な権限を持っていることを確認する必要があります。 これも行われていない場合、次のエラーが表示されます。

次のリソース プロバイダーを登録するためのアクセス許可が*サブスクリプション \<Subscription name> にありません:Microsoft.DataBoxEdge*


現在のサブスクリプションに登録されているリソース プロバイダーの一覧を取得するには、次のコマンドを実行します。

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge デバイスの場合、`Microsoft.DataBoxEdge` を登録する必要があります。 `Microsoft.DataBoxEdge` を登録するには、サブスクリプション管理者が次のコマンドを実行する必要があります。

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

リソース プロバイダーの登録方法の詳細については、[リソース プロバイダー登録エラーの解決](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)に関する記事を参照してください。

## <a name="manage-connectivity-mode"></a>接続モードを管理する

既定の標準モードとは別に、デバイスは部分切断モードまたは切断モードで動作することもできます。 ここでは、これらの各モードについて説明します。

- **部分切断** – このモードでは、デバイスはどのようなデータも共有にアップロードできませんが、Azure portal からデバイスを管理することはできます。

    このモードは通常、従量制課金のサテライト ネットワークで使用され、目的はネットワーク帯域幅の消費量を最小限に抑えることです。 デバイス監視操作のため、最小限のネットワーク消費が発生する可能性があります。

- **切断** – このモードでは、デバイスはクラウドから完全に切断されており、クラウドのアップロードとダウンロードの両方が無効になっています。 デバイスは、ローカル Web UI からのみ管理できます。

    このモードは通常、デバイスをオフラインにするときに使用されます。

デバイスのモードを変更するには、次の手順のようにします。

1. デバイスのローカル Web UI で、 **[構成] > [クラウドの設定]** に移動します。
2. **[Cloud upload and download]\(クラウドのアップロードとダウンロード\)** を無効にします。
3. デバイスを部分切断モードで実行するには、 **[Azure portal management]\(Azure portal 管理\)** を有効にします。

    ![接続モード](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. デバイスを切断モードで実行するには、 **[Azure portal management]\(Azure portal 管理\)** を無効にします。 デバイスは、ローカル Web UI からのみ管理できるようになります。

    ![接続モード](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>電源を管理する

ローカル Web UI を使用して、仮想デバイスをシャットダウンおよび再起動できます。 再起動する前に、ホストの共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 この操作により、データ破損の可能性を最小限に抑えられます。

1. ローカル Web UI で、 **[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. 何を行うかに応じて、 **[Shutdown]\(シャットダウン\)** または **[Restart]\(再起動\)** をクリックします。

    ![電源設定](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 確認を求められたら、 **[はい]** をクリックして続行します。

> [!NOTE]
> 仮想デバイスをシャットダウンした場合は、ハイパーバイザーの管理からデバイスを起動する必要があります。
