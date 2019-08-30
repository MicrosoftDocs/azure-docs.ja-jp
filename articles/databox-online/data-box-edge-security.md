---
title: Azure Data Box Edge のセキュリティ | Microsoft Docs
description: Azure Data Box Edge デバイス、サービス、オンプレミスのデータ、クラウド内のデータを保護するセキュリティおよびプライバシー機能について説明します。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970879"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge のセキュリティとデータ保護

セキュリティは、新しいテクノロジを導入している場合、特にそのテクノロジが機密データや独自のデータに対して使用される場合の大きな関心事です。 Azure Data Box Edge は、承認されたエンティティのみがデータを表示、変更、または削除できるようにするために役立ちます。

この記事では、各ソリューション コンポーネントや、そこに格納されているデータを保護するのに役立つ Data Box Edge のセキュリティ機能について説明します。

Azure Data Box Edge は、互いに連携する次の 4 つの主なコンポーネントで構成されています。

- **Azure でホストされる Data Box Edge サービス**。 デバイスの注文を作成し、そのデバイスを構成した後、その注文を完了まで追跡するために使用する管理リソース。
- **Data Box Edge デバイス**。 ユーザーがオンプレミスのデータを Azure にインポートできるように、ユーザーに発送される転送デバイス。
- **デバイスに接続されるクライアント/ホスト**。 Data Box Edge デバイスに接続し、保護する必要があるデータを含んでいるインフラストラクチャ内のクライアント。
- **クラウド ストレージ**。 データが格納されている Azure クラウド プラットフォーム内の場所。 この場所は通常、作成する Data Box Edge リソースにリンクされたストレージ アカウントです。

## <a name="data-box-edge-service-protection"></a>Data Box Edge サービスの保護

Data Box Edge サービスは、Azure でホストされる管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Data Box Edge デバイスの保護

Data Box Edge デバイスは、データをローカルで処理し、それを Azure に送信することによってデータを変換するのに役立つオンプレミスのデバイスです。 お客様のデバイスは...

- Data Box Edge サービスにアクセスするためのアクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
- ロックダウンされます。 デバイスの BMC と BIOS はパスワードで保護されています。 BIOS は、制限されたユーザー アクセスによって保護されています。
- セキュア ブートが有効です。
- Windows Defender Device Guard を実行します。 Device Guard を使用すると、コード整合性ポリシーで定義した信頼されたアプリケーションのみを実行できます。

### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Data Box Edge サービスへの参加が許可されるのは、承認された Data Box Edge デバイスだけです。 デバイスを承認するには、アクティブ化キーを使用して Data Box Edge サービスでデバイスをアクティブ化する必要があります。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

詳細については、「[アクティブ化キーの取得](data-box-edge-deploy-prep.md#get-the-activation-key)」を参照してください。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードにより、承認されたユーザーのみがデータにアクセスできるようになります。 Data Box Edge デバイスは、ロックされた状態で起動されます。

次のようにすることができます。

- ブラウザー経由でデバイスのローカル Web UI に接続し、パスワードを指定してデバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 デバイスのパスワードを指定してデバイスにサインインできます。 詳細については、[Data Box Edge デバイスへのリモート接続](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)に関するページを参照してください。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- ローカル Web UI を使用して[パスワードを変更します](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 パスワードを変更する場合は、サインインの問題が発生しないように、必ずすべてのリモート アクセス ユーザーに通知してください。

## <a name="protect-your-data"></a>データを保護する

このセクションでは、移動中のデータや格納されたデータを保護する Data Box Edge のセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- ローカル データの保護には BitLocker XTS-AES 256 ビット暗号化が使用されます。


### <a name="protect-data-in-flight"></a>移動中のデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>ストレージ アカウントによるデータの保護

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 承認されていないユーザーからのストレージ アカウントの保護に役立つように、定期的にストレージ アカウント キーをローテーションし、その後[同期](data-box-edge-manage-shares.md#sync-storage-keys)します。

## <a name="manage-personal-information"></a>個人情報の管理

Data Box Edge サービスは、次のシナリオで個人情報を収集します。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Data Box Edge での共有の管理](data-box-edge-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)で Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次の手順

[Data Box Edge デバイスをデプロイする](data-box-edge-deploy-prep.md)
