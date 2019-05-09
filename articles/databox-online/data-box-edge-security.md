---
title: Data Box Edge のセキュリティ | Microsoft Docs
description: Data Box Edge のデバイス、サービス、オンプレミスまたはクラウド上のデータを保護するセキュリティ機能とプライバシー機能について説明します。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 197c2c021dd9f674d196fb1169155bc6015f2e79
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578612"
---
# <a name="data-box-edge-security-and-data-protection"></a>Data Box Edge のセキュリティとデータ保護

新しいテクノロジを採用する際に、セキュリティは大きな懸案事項となります。そのテクノロジで機密データや財産的価値のあるデータを扱うとなれば、なおさらです。 Microsoft Azure Data Box Edge ソリューションは、承認済みのエンティティ以外データの閲覧、変更、削除ができないよう徹底するうえで役立ちます。

この記事では、Data Box Edge のセキュリティ機能に注目し、このソリューションの各コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。

Azure Data Box Edge ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Data Box Edge/Data Box Gateway サービス** - デバイスを発注して構成し、その注文を最後まで追跡するための管理リソース。
- **Data Box Edge デバイス** - オンプレミスのデータを Azure にインポートするためにお客様に発送される転送デバイス。
- **デバイスに接続されるクライアント/ホスト** - お客様のインフラストラクチャにおいて保護の対象となるデータを格納しているクライアント。Data Box Edge デバイスに接続されます。
- **クラウド ストレージ** – Azure クラウド内のデータの保存先となる場所。 通常、この場所は、お客様が作成した Data Box Edge リソースにリンクされたストレージ アカウントになります。


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Data Box Edge/Data Box Gateway のサービス保護

Data Box Edge/Data Box Gateway サービスは、Microsoft Azure にホストされている管理サービスです。 このサービスは、デバイスを構成および管理するために使用します。

- Data Box Edge/Data Box Gateway サービスにアクセスするには、お客様の組織がマイクロソフトエンタープライズ契約 (EA) とクラウド ソリューション プロバイダー (CSP) のサブスクリプションを保有している必要があります。 詳細については、「[Azure サブスクリプションにサインアップする](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)」にアクセスしてください。
- お客様の管理サービスは Azure にホストされるため、Azure のセキュリティ機能によって保護されます。 Microsoft Azure のセキュリティ機能の詳細については、「 [Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/security/)」をご覧ください。
- SDK の管理操作で使用する Data Box Edge または Data Box Gateway リソースの暗号化キーは、**[デバイスのプロパティ]** で入手できます。 暗号化キーを表示できるのは、Resource Graph API のアクセス許可がある場合のみです。

## <a name="data-box-edge-device-protection"></a>Data Box Edge デバイスの保護

Data Box Edge デバイスは、データをローカルで処理して、そのデータを Azure に送信することで、データを変換できるオンプレミス デバイスです。 お客様のデバイスは...

- Data Box Edge/Data Box Gateway のサービスにアクセスするためのアクティブ化キーが必要です。
- デバイスのパスワードで常に保護されます。
- ロックダウンされます。 デバイスの BMC と BIOS は、パスワードで保護され、BIOS に対するユーザー アクセスは制限されます。
- セキュア ブートが有効です。
- Windows Defender Device Guard を実行します。 Device Guard によって、コード整合性ポリシーで定義する信頼されたアプリケーションのみを実行することができます。
- 前面カバーの内側に、デバイスをロックするための鍵があります。 デバイスを構成したら、カバーを開けることをお勧めします。 デバイスの前面にあるデータ ディスクに許可なくアクセスできないよう、鍵を探してカバーをロックしてください。

### <a name="protect-the-device-via-activation-key"></a>アクティブ化キーでデバイスを保護する

Azure サブスクリプションで作成した Data Box Edge/Data Box Gateway サービスに参加できるのは、承認されている Data Box Edge デバイスだけです。 デバイスを承認するには、アクティブ化キーを使用して、Data Box Edge/Data Box Gateway サービスを使用するデバイスをアクティブにする必要があります。 詳細については、「[アクティブ化キーの取得](data-box-edge-deploy-prep.md#get-the-activation-key)」をご覧ください。

使用するアクティブ化キーは...

- Azure Active Directory (AAD) ベースの認証キーです。
- 3 日で有効期限が切れます。
- デバイスがアクティブ化された後は使用されません。
 
アクティブにされたデバイスは、トークンを使用して Microsoft Azure と通信を行います。

### <a name="protect-the-device-via-password"></a>パスワードでデバイスを保護する

パスワードによって、承認されたユーザーだけがお客様のデータにアクセスできるようにします。 Data Box Edge と Data Box Gateway デバイスは、ロックされた状態で起動されます。

次のようにすることができます。

- ブラウザーを介してデバイスのローカル Web UI に接続し、パスワードを指定して、デバイスにサインインします。
- HTTP 経由でデバイスの PowerShell インターフェイスにリモートで接続します。 既定ではリモート管理がオンになっています。 デバイスのパスワードを入力して、デバイスにサインインできます。 詳細については、[Data Box Edge デバイスにリモートで接続する方法](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)に関する記事をご覧ください。

次のベスト プラクティスに留意してください。

- Data Box Edge サービスで既存のパスワードを取得することはできません。このサービスでは、Azure portal を介して既存のパスワードをリセットすることのみを行えます。 パスワードを忘れた場合にリセットしなくても済むように、パスワードはすべて安全な場所に保管しておくことをお勧めします。 パスワードをリセットする場合は、リセットする前にすべてのユーザーに通知してください。
- ローカル Web UI を使用して[パスワードを変更します](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 パスワードを変更する場合は、サインインできなくなることを防ぐために、すべてのリモート アクセス ユーザーにその旨を通知してください。
- HTTP 経由でお使いのデバイスの Windows PowerShell インターフェイスにリモートでアクセスすることができます。 セキュリティのベスト プラクティスとして、信頼できるネットワークのみで HTTP を使用する必要があります。
- デバイスのパスワードが強力であること、また適切に保護されていることを確認します。 [パスワードのベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)に従ってください。

## <a name="protect-the-data"></a>データの保護

ここでは、移動中のデータと保管されているデータを保護する Data Box Edge のセキュリティ機能について説明します。

### <a name="protect-data-at-rest"></a>保存データの保護

保存データの場合:

- 保存データの場合、Data Box Edge は、BitLocker XTS AES-256 暗号化を使用して、ローカル データを保護します。
- 共有内にあるデータの場合、共有へのアクセスを制限します。

    - 共有データにアクセスする SMB クライアントでは、共有に関連付けられているユーザーの資格情報が必要になります。 これらの資格情報は、共有の作成時点で定義されます。
    - 共有にアクセスする NFS クライアントでは、共有作成時に、クライアントの IP アドレスを追加する必要があります。


### <a name="protect-data-in-flight"></a>移動中のデータの保護

移動中のデータの場合:

- デバイスと Azure 間で移動するデータの場合、標準 TLS 1.2 を使用します。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信はブロックされます。 ポータルや SDK の管理操作にも TLS 1.2 が必要です。
- クライアントがブラウザーのローカル Web UI を介してデバイスにアクセスする場合、標準 TLS 1.2 が既定の安全なプロトコルとして使用されます。

    - ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。
    - お使いのブラウザーが TLS 1.2 をサポートしていない場合、TLS 1.1 または TLS 1.0 を使用できます。
- データ サーバーからデータをコピーするときに、そのデータを保護するには、暗号化に SMB 3.0 を使用することをお勧めします。

### <a name="protect-data-via-storage-accounts"></a>ストレージ アカウントによるデータの保護

デバイスは、Azure のデータの宛先として使用するストレージ アカウントと関連付けられます。 ストレージ アカウントへのアクセスは、各ストレージ アカウントに関連付けられたサブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって制御されます。

Data Box Edge デバイスがストレージ アカウントにアクセスするときに、いずれかのキーが認証に使用されます。 もう 1 つは、キーの定期的なローテーションを見越して、あらかじめ作成されるものです。

セキュリティ上の理由から、多くのデータ センターでキーのローテーションが義務化されています。 キーのローテーションに関しては、以下のベスト プラクティスに従うようお勧めします。

- ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 アカウント キーは慎重に保護してください。 このパスワードを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキストで保存したりしないでください。
- アカウント キーが侵害されたと思われる場合は、Azure Portal を使用して[キーを再生成](../storage/common/storage-account-manage.md#regenerate-access-keys)してください。
- 承認されていないユーザーがストレージ アカウントにアクセスできないよう[ストレージ アカウント キーの同期](data-box-gateway-manage-shares.md#sync-storage-keys)とローテーションは日常的に行う。
- Azure 管理者が Azure ポータルの [ストレージ] セクションを使用してストレージ アカウントに直接アクセスして、プライマリ キーまたはセカンダリ キーを定期的に変更または再生成する。


## <a name="manage-personal-information"></a>個人情報の管理

Data Box Edge/Data Box Gateway サービスは、次の主要なインスタンスの個人情報を収集します。

- **注文の詳細** - 発注後、ユーザーの配送先住所、メール、連絡先情報が Azure portal に格納されます。 たとえば、次の情報が保存の対象となります。
  - 連絡先の名前
  - 電話番号
  - Email
  - 番地
  - City
  - 郵便番号
  - 状態
  - 国/地方/地域
  - 配送追跡番号

    注文の詳細は暗号化され、サービスに格納されます。 サービスでは、リソースまたは注文を明示的に削除するまで、情報が保持されます。 さらに、リソースの削除および対応する注文は、デバイスが出荷されたときから、デバイスが Microsoft に戻るまでブロックされます。

- **配送先住所** - Data Box サービスは、注文を受けると、サード パーティの運送業者 (UPS など) に配送先住所を提供します。

- **共有ユーザー** - デバイスのユーザーは、共有に存在するデータにアクセスできます。 共有データにアクセスできるユーザーの一覧が表示され、確認できるようになります。 共有が削除されると、この一覧も削除されます。 共有にアクセスできる、または削除できるユーザーの一覧を表示するには、[Data Box Edge での共有の管理](data-box-gateway-manage-shares.md)に関するページにある手順に従ってください。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。

## <a name="next-steps"></a>次の手順

[Data Box Edge デバイスをデプロイします](data-box-edge-deploy-prep.md)。

