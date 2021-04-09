---
title: Azure Stack Edge Pro R のセキュリティ | Microsoft Docs
description: Azure Stack Edge Pro R および Azure Stack Edge Mini R のデバイス、サービス、オンプレミスとクラウド内のデータを保護するセキュリティおよびプライバシー機能について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: bd90a16c09dce65115cea2f097d18f2e0ced931a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632035"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R および Azure Stack Edge Mini R のセキュリティとデータ保護

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

セキュリティは、新しいテクノロジを導入している場合、特にそのテクノロジが機密データや独自のデータに対して使用される場合の大きな関心事です。 Azure Stack Edge Pro R と Azure Stack Edge Mini R は、承認されたエンティティのみがデータを表示、変更、または削除できるようにするために役立ちます。

この記事では、各ソリューション コンポーネントや、そこに格納されているデータを保護するのに役立つ Azure Stack Edge Pro R と Azure Stack Edge Mini R のセキュリティ機能について説明します。

このソリューションは、相互連携する 4 つの主要コンポーネントで構成されます。

- **Azure パブリックまたは Azure Government クラウド内でホストされている Azure Stack Edge サービス**。 デバイスの注文を作成し、そのデバイスを構成した後、その注文を完了まで追跡するために使用する管理リソース。
- **Azure Stack Edge ラグド デバイス**。 オンプレミスのデータを Azure パブリックまたは Azure Government クラウドにインポートできるように、ユーザーに発送される物理ラグド デバイス。 そのデバイスは、Azure Stack Edge Pro R または Azure Stack Edge Mini R の可能性があります。
- **デバイスに接続されるクライアント/ホスト**。 デバイスに接続し、保護する必要があるデータが含まれるインフラストラクチャ内のクライアント。
- **クラウド ストレージ**。 データが格納されている Azure クラウド プラットフォーム内の場所。 この場所は通常、作成する Azure Stack Edge リソースにリンクされたストレージ アカウントです。

## <a name="service-protection"></a>サービスの保護

Azure Stack Edge サービスは、Azure でホストされる管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>デバイスの保護

ラグド デバイスは、データをローカルで処理し、それを Azure に送信することによってデータを変換するのに役立つオンプレミスのデバイスです。 お客様のデバイスは...

- Azure Stack Edge サービスにアクセスするには、アクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
- ロックダウンされます。 デバイスのベースボード管理コントローラー (BMC) と BIOS は、パスワードで保護されています。 BMC は、制限されたユーザー アクセスによって保護されています。
- セキュア ブートが有効になっています。これにより、Microsoft が提供する信頼されたソフトウェアのみを使用してデバイスが起動されるようになります。
- Windows Defender アプリケーション制御 (WDAC) を実行しています。 WDAC を使用すると、コード整合性ポリシーで定義した信頼されたアプリケーションのみを実行できます。
- ハードウェア ベースのセキュリティ関連機能を実行するトラステッド プラットフォーム モジュール (TPM) を搭載しています。 具体的には、TPM により、デバイスに保存する必要があるシークレットとデータを管理および保護します。
- 必要なポートだけがデバイス上で開かれ、他のすべてのポートはブロックされます。 詳細については、[デバイスのポート要件](azure-stack-edge-pro-r-system-requirements.md)に関するページを参照してください。
- デバイス ハードウェアおよびソフトウェアへのすべてのアクセスがログされます。 
    - デバイス ソフトウェアについては、受信および送信トラフィックを対象に既定のファイアウォール ログがデバイスから収集されます。 これらのログは、サポート パッケージにバンドルされます。
    - デバイス ハードウェアについては、デバイスのシャーシの開閉などのすべてのデバイス シャーシ イベントがデバイスに記録されます。

    ハードウェアおよびソフトウェアの侵入イベントを含む特定のログと、ログを取得する方法の詳細については、[高度なセキュリティ ログの収集](azure-stack-edge-gpu-troubleshoot.md)に関するページを参照してください。


### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Azure Stack サービスに参加できるのは、承認された Azure Stack Edge Pro R または Azure Stack Edge Mini R デバイスのみです。 デバイスを承認するには、アクティブ化キーを使用して Azure Stack Edge サービスでデバイスをアクティブ化する必要があります。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

詳細については、「[アクティブ化キーの取得](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)」を参照してください。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードにより、承認されたユーザーのみがデータにアクセスできるようになります。 Azure Stack Edge Pro R デバイスはロックされた状態で起動します。

次のようにすることができます。

- ブラウザー経由でデバイスのローカル Web UI に接続し、パスワードを指定してデバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 また、リモート管理は、ユーザーが実行できることを制限するために Just Enough Administration (JEA) を使用するように構成されています。 デバイスのパスワードを指定してデバイスにサインインできます。 詳細については、[デバイスへのリモート接続](azure-stack-edge-gpu-connect-powershell-interface.md)に関するページを参照してください。
- デバイス上のローカルの Edge ユーザーは、デバイスに対して、初期構成およびトラブルシューティングのための限定的なアクセス権を持ちます。 デバイスで実行されているコンピューティング ワークロード、データ転送、およびストレージはすべて、クラウド内のリソースの Azure パブリックまたは Government ポータルからアクセスできます。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- [パスワードを変更する](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)には、ローカル Web UI を使用します。 パスワードを変更する場合は、サインインの問題が発生しないように、必ずすべてのリモート アクセス ユーザーに通知してください。

### <a name="establish-trust-with-the-device-via-certificates"></a>証明書を使用してデバイスとの信頼関係を確立する

Azure Stack Edge のラグド デバイスを使用すると、独自の証明書を持ち込み、すべてのパブリック エンドポイントに使用するためにインストールできます。 詳細については、[証明書のアップロード](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)に関するページを参照してください。 デバイスにインストールできるすべての証明書の一覧については、[デバイスでの証明書の管理](azure-stack-edge-gpu-manage-certificates.md)に関するページを参照してください。

- デバイス上でコンピューティングを構成すると、IoT デバイスと IoT Edge デバイスが作成されます。 これらのデバイスには、対称のアクセス キーが自動的に割り当てられます。 セキュリティのベスト プラクティスとして、これらのキーは IoT Hub サービスを通じて定期的にローテーションされます。

## <a name="protect-your-data"></a>データを保護する

このセクションでは、移動中および格納されたデータを保護するセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

デバイス上の保存データはすべて二重に暗号化され、データへのアクセスが制御されます。デバイスが非アクティブになると、データはデータ ディスクから安全な方法で消去されます。

#### <a name="double-encryption-of-data"></a>データの二重暗号化

ディスク上のデータは、2 つの暗号化レイヤーによって保護されます。

- 暗号化の最初のレイヤーは、データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化です。
- 2 番目のレイヤーは、暗号化が組み込まれたハード ディスクです。
- OS ボリュームには、単一の暗号化レイヤーとして BitLocker が搭載されています。

> [!NOTE]
> OS ディスクには、単一レイヤーの BitLocker XTS-AES-256 ソフトウェア暗号化が使用されます。

デバイスがアクティブになると、キー ファイルを保存するように求められます。これには、デバイスが起動しない場合にデバイス上のデータを回復するのに役立つ回復キーが格納されます。 このファイル内には 2 つのキーがあります。

- 1 つ目は、OS ボリューム上のデバイス構成を回復するキーです。
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- 2 つ目は、データ ディスクのハードウェア暗号化のロックを解除するキーです。

> [!IMPORTANT]
> キー ファイルは、デバイス自体の外部にある安全な場所に保存してください。 デバイスが起動しない場合にキーがないと、データが失われる可能性があります。

- 特定の回復シナリオでは、保存したキー ファイルを求められます。 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>データへの制限付きアクセス

共有およびストレージ アカウントに格納されたデータへのアクセスは制限されます。

- 共有データにアクセスする SMB クライアントには、その共有に関連付けられたユーザー資格情報が必要です。 これらの資格情報は、その共有が作成されるときに定義されます。
- 共有にアクセスする NFS クライアントの IP アドレスが、その共有の作成時に明示的に追加される必要があります。
- デバイス上に作成される Edge ストレージ アカウントはローカルであり、データ ディスクに対する暗号化によって保護されます。 このような Edge ストレージ アカウントがマップされている Azure ストレージ アカウントは、Edge ストレージ アカウントに関連付けられているサブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって保護されます (これらのキーは、Azure ストレージ アカウントに関連付けられているものとは異なります)。 詳細については、「[ストレージ アカウント内のデータの保護](#protect-data-in-storage-accounts)」を参照してください。
- ローカル データの保護には BitLocker XTS-AES 256 ビット暗号化が使用されます。

#### <a name="secure-data-erasure"></a>データ消去のセキュリティ保護

デバイスがハード リセットされると、セキュリティで保護されたワイプがデバイスに対して実行されます。 セキュリティで保護されたワイプにより、NIST SP 800-88r1 purge を使用してディスク上のデータ消去が実行されます。

### <a name="protect-data-in-flight"></a>移動中のデータの保護

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>ストレージ アカウント内のデータの保護

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- 承認されていないユーザーからのストレージ アカウントの保護に役立つように、定期的にストレージ アカウント キーをローテーションし、その後[同期](azure-stack-edge-gpu-manage-storage-accounts.md)します。

## <a name="manage-personal-information"></a>個人情報の管理

Azure Stack Edge サービスは、次のシナリオで個人情報を収集します。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Azure Stack Edge での共有の管理](azure-stack-edge-gpu-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)で Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro R デバイスをデプロイする](azure-stack-edge-gpu-deploy-prep.md)
