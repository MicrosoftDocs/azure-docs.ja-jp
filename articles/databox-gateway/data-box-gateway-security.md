---
title: Azure Data Box Gateway のセキュリティ | Microsoft Docs
description: Azure Data Box Gateway の仮想デバイス、サービス、オンプレミスまたはクラウド上のデータを保護するセキュリティ機能とプライバシー機能について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786658"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway のセキュリティとデータ保護

セキュリティは、新しいテクノロジを導入している場合、特にそのテクノロジが機密データや独自のデータに対して使用される場合の大きな関心事です。 Azure Data Box Gateway は、承認されたエンティティのみがデータを表示、変更、または削除できるようにするために役立ちます。

この記事では、Azure Data Box Gateway のセキュリティに注目し、各ソリューション コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。

Data Box Gateway ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Data Box Gateway サービス**。 デバイスの注文を作成し、そのデバイスを構成した後、その注文を完了まで追跡するために使用する管理リソース。
- **Data Box Gateway デバイス**。 お客様が用意するシステムのハイパーバイザー内にプロビジョニングする仮想デバイスです。 この仮想デバイスは、オンプレミス データを Azure にインポートするために使用されます。
- **デバイスに接続されるクライアント/ホスト**。 Data Box Gateway デバイスに接続し、保護する必要があるデータを含んでいるインフラストラクチャ内のクライアント。
- **クラウド ストレージ**。 データが格納されている Azure クラウド プラットフォーム内の場所。 通常、この場所は、お客様が作成した Data Box Gateway リソースにリンクされたストレージ アカウントになります。

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway サービスの保護

Data Box Gateway サービスは、Azure でホストされる管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway デバイスの保護

Data Box Gateway デバイスは、お客様が用意するオンプレミス システムのハイパーバイザー内にプロビジョニングする仮想デバイスです。 このデバイスは、データを Azure に送信するために役立ちます。 お客様のデバイスは...

- Azure Stack Edge Pro または Data Box Gateway のサービスにアクセスするためのアクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Data Box Gateway サービスに参加できるのは、承認されている Data Box Gateway デバイスだけです。 デバイスを承認するには、アクティブ化キーを使用して、Data Box Gateway サービスを使用するデバイスをアクティブにする必要があります。

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

詳細については、「[アクティブ化キーの取得](data-box-gateway-deploy-prep.md#get-the-activation-key)」を参照してください。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードにより、承認されたユーザーのみがデータにアクセスできるようになります。 Data Box Gateway デバイスは、ロックされた状態で起動されます。

次のようにすることができます。

- ブラウザー経由でデバイスのローカル Web UI に接続し、パスワードを指定してデバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 デバイスのパスワードを指定してデバイスにサインインできます。 詳細については、[Data Box Gateway デバイスにリモートで接続する方法](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)に関する記事を参照してください。

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- ローカル Web UI を使用して[パスワードを変更します](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 パスワードを変更する場合は、サインインの問題が発生しないように、必ずすべてのリモート アクセス ユーザーに通知してください。

## <a name="protect-your-data"></a>データを保護する

ここでは、移動中のデータと保管されているデータを保護する Data Box Gateway のセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>移動中のデータの保護

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>ストレージ アカウントを使用したデータの保護

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- 承認されていないユーザーからのストレージ アカウントの保護に役立つように、定期的にストレージ アカウント キーをローテーションし、その後[同期](data-box-gateway-manage-shares.md#sync-storage-keys)します。

### <a name="protect-the-device-data-using-bitlocker"></a>BitLocker を使用したデバイス データの保護

Data Box Gateway の仮想マシン上の仮想ディスクをセキュリティで保護するには、BitLocker を有効にすることをお勧めします。 既定では、BitLocker は有効になっていません。 詳細については、次を参照してください。

- [Hyper-V マネージャーでの暗号化サポート設定](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [仮想マシンでの BitLocker のサポート](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>個人情報の管理

Data Box Gateway サービスは、次のシナリオの個人情報を収集します。

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Data Box Gateway での共有の管理](data-box-gateway-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)で Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次のステップ

[Data Box Gateway デバイスをデプロイする](data-box-gateway-deploy-prep.md)
