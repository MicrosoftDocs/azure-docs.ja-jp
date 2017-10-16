---
title: "転送中の個人データを Azure の暗号化で保護する | Microsoft Docs"
description: "Azure の暗号化を使って個人データを保護する"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure 暗号化テクノロジ: 転送中の個人データを暗号化で保護する

この記事では、転送中のデータをセキュリティで保護する Azure 暗号化テクノロジを理解して使用できるようにします。 

ネットワーク経由でやり取りされる個人データのプライバシーの保護は、マルチレイヤーの多層防御セキュリティ戦略の重要な部分です。 転送中の暗号化は、伝送を傍受する攻撃者によるデータの表示や使用を防ぐように設計されています。

## <a name="scenario"></a>シナリオ

米国に本社を置く大手クルーズ会社が、地中海、アドリア海、バルト海だけでなくイギリス諸島での旅程を提供できるように、事業を拡張しています。 この会社は、これらの取り組みを支援するために、イタリア、ドイツ、デンマーク、英国に拠点を置くいくつかの小規模なクルーズ会社を買収しました。 

その会社では、クラウドに会社のデータを保存するために Microsoft Azure を使います。 これには、グローバルな顧客ベースの名前や住所、電話番号、クレジット カード情報など、個人を特定できる情報が含まれます。 これには、住所、電話番号、納税者番号など、あらゆる場所の会社の従業員に関する従来の人事情報も含まれます。 このクルーズ会社では、現在と過去の顧客とのリレーションシップを追跡するための個人情報を含む、特典やロイヤリティ プログラム メンバーの大規模なデータベースも管理しています。

顧客の個人データは、会社の支社と、世界各地に存在する旅行代理店から、データベースに入力されます。 顧客情報を含むドキュメントは、ネットワーク経由で Azure Storage に転送されます。

## <a name="problem-statement"></a>問題の説明

会社では、顧客と従業員の個人データを Azure サービスとの間で転送するときに、そのプライバシーを保護する必要があります。

## <a name="company-goal"></a>会社の目標

会社の目標は、ディスクを離れた個人データが暗号化されるようにすることです。 ディスクを離れた個人データが許可なく傍受された場合に、判読できない形式になっている必要があります。 暗号化の適用は、ユーザーと管理者にとって簡単または完全に透過的である必要があります。

## <a name="solutions"></a>解決方法

Azure サービスでは、転送中の個人データの保護に役立つ複数のツールとテクノロジを提供しています。

### <a name="azure-storage"></a>Azure Storage (Azure Storage)

クラウドに格納されるデータは、物理的に世界中のどこにでも配置できるクライアントから Azure データ センターに移動する必要があります。 データは、ユーザーによって取得されるときに、もう一度反対方向に移動します。 パブリック インターネット経由で転送中のデータは、常に攻撃者による傍受のリスクにさらされます。 場所の間で移動するときにセキュリティで保護するトランスポート レベルの暗号化を使って、個人データのプライバシーを保護することが重要です。

HTTPS プロトコルは、インターネット上にセキュリティで保護され、暗号化された通信チャネルを提供します。 Azure Storage でオブジェクトにアクセスするとき、また REST API を呼び出すときには、HTTPS を使う必要があります。 [Shared Access Signature](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) を 使って Azure Storage オブジェクトへのアクセスを委任するときには、HTTPS プロトコルの使用を強制します。 SAS には、サービス SAS とアカウント SAS の 2 種類があります。

#### <a name="how-do-i-construct-a-service-sas"></a>サービス SAS を構成する方法

サービス SAS は、1 つのストレージ サービス (BLOB、Queue、Table、File のいずれかのサービス) のリソースへのアクセスを委任します。 サービス SAS を構成するには、以下の手順を実行します。

1. 署名済みバージョン フィールドを指定する

2. 署名済みリソースを指定する (Blob Service と File Service のみ)

3. 応答ヘッダーを上書きするクエリ パラメーターを指定する (Blob Service と File Service のみ)

4. テーブル名を指定する (Table Service のみ)

5. アクセス ポリシーを指定する

6. 署名の検証間隔を指定する

8. アクセス許可を指定する

9. IP アドレスまたは IP 範囲を指定する

10. HTTP プロトコルを指定する

11. テーブル アクセスの範囲を指定する

12. 署名済み識別子を指定する

13. 署名を指定する

詳しい手順については、「[Constructing a Service SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN)」(サービス SAS を構成する) をご覧ください。

#### <a name="how-do-i-construct-an-account-sas"></a>アカウント SAS を構成する方法

アカウント SAS は、1 つ以上のストレージ サービスのリソースへのアクセスを委任します。 サービス SAS で許可されていない BLOB コンテナー、テーブル、キューおよびファイル共有の読み取り、書き込みおよび削除操作へのアクセスも委任できます。 アカウント SAS の構成は、サービス SAS の作成と同様です。 詳しい手順については、「[Constructing an Account SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)」(アカウント SAS を構成する) をご覧ください。

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>REST API を呼び出すときに HTTPS を強制する方法

ストレージ アカウント内のオブジェクトにアクセスするための REST API を呼び出す際に HTTPS の使用を強制するには、ストレージ アカウントの [安全な転送が必須] を有効にします。 

1. Azure Portal で、**[ストレージ アカウントの作成]** を選択するか、既存のストレージ アカウントの場合は **[設定]**、**[構成]** の順に選択します。

2. **[安全な転送が必須]** で、**[有効]** を選択します。

![ストレージ アカウントの作成](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

[安全な転送が必須] をプログラムで有効にする方法を含む詳しい手順については、「[安全な転送が必要](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer)」をご覧ください。

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Azure File Storage のデータを暗号化する方法

[Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal) で転送中のデータを暗号化するには、SMB 3.x を Windows 8、Windows 8.1、Windows 10、Windows Server 2012 R2、Windows Server 2016 と共に使います。 Azure Files サービスを使用している場合、[安全な転送が必須] を有効にすると、暗号化をしない接続は失敗します。 これには、暗号化なしの SMB 2.1、SMB 3.0、および Linux SMB クライアントのフレーバーを使用するシナリオも含まれます。

#### <a name="azure-client-side-encryption"></a>Azure のクライアント側の暗号化

クライアント アプリケーションと Azure Storage 間で転送される個人データを保護するためのもう 1 つのオプションは、[クライアント側の暗号化](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)です。 データは Azure Storage に転送される前に暗号化され、Azure Storage からデータを取得するときはクライアント側で受信された後に復号化されます。

### <a name="azure-site-to-site-vpn"></a>Azure サイト間 VPN

企業ネットワークまたはユーザーと Azure 仮想ネットワークの間で転送中の個人データを保護する効果的な方法は、[サイト間](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)または[ポイント対サイト](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)仮想プライベート ネットワーク (VPN) を使うことです。 VPN 接続では、インターネット上にセキュリティで保護され暗号化されたトンネルが作成されます。

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>サイト間 VPN 接続の作成方法

サイト間 VPN は、企業ネットワーク上の複数のユーザーを Azure に接続します。 Azure Portal でサイト間接続を作成するには、以下の手順を実行します。

1. 仮想ネットワークを作成します。

2. DNS サーバーを指定します。

3. ゲートウェイ サブネットを作成します。

4. VPN ゲートウェイを作成します。 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. ローカル ネットワーク ゲートウェイを作成します。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. VPN デバイスを構成します。

7. VPN 接続を作成します。

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. VPN 接続を確認します。

Azure Portal でサイト間接続を作成する詳しい手順については、「[Azure Portal でサイト間接続を作成する] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)」をご覧ください

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>ポイント対サイト VPN 接続の作成方法

ポイント対サイト VPN では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 これは、自宅、ホテル、会議センターなど、リモートの場所から Azure に接続する場合に便利です。 Azure Portal を使ってポイント対サイト接続を作成するには

1. 仮想ネットワークを作成します。

2. ゲートウェイ サブネットを追加します。

3. DNS サーバーを指定します。 (省略可能)

4. 仮想ネットワーク ゲートウェイを作成します。

5. 証明書を生成します。

6. クライアント アドレス プールを追加します。

7. ルート証明書の公開証明書データをアップロードします。

8. VPN クライアント構成パッケージの生成とインストールを行います。

9. エクスポートしたクライアント証明書をインストールします。

10. Azure に接続します。

11. 接続を確認します。

詳しい手順については、「[証明書認証を使用した VNet へのポイント対サイト接続の構成: Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)」をご覧ください。

### <a name="ssltls"></a>SSL/TLS

異なる場所間でのデータの交換には、常に SSL/TLS プロトコルを使うことをお勧めします。 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) を使って専用の高速 WAN リンク上で大規模データ セットを移動することを選んだ組織は、SSL/TLS などのプロトコルを使ってアプリケーション レベルでデータを暗号化することで、さらに保護を強化できます。

### <a name="encryption-by-default"></a>既定での暗号化

Microsoft では、暗号化を使って、顧客と Azure Cloud Services の間で転送中のデータを保護します。 Azure Portal で Azure Storage を操作する場合、すべてのトランザクションは HTTPS 経由で行われます。

[トランスポート層セキュリティ](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) は、Microsoft データ センターが Microsoft クラウド サービスに接続するクライアント システムとのネゴシエートに使うプロトコルです。 TLS には、強力な認証、メッセージの機密性、整合性 (メッセージの改ざん、傍受、偽造の検出が有効)、相互運用性、アルゴリズムの柔軟性、デプロイと使用のしやすさといったメリットがあります。

顧客のクライアント システムと Microsoft のクラウド サービスとの間の各接続が一意のキーを使うようにするために、[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) も採用されています。 Microsoft クラウド サービスへの接続では、RSA ベースの 2,048 ビット長の暗号化キーも利用します。 TLS、RSA 2,048 ビット長のキー、PFS を組み合わせると、Microsoft クラウド サービスと顧客間で転送中のデータの傍受やアクセスがはるかに難しくなります。

転送中のデータは常に [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview) で暗号化されます。 データは永続メディアに格納される前に暗号化されるうえ、転送中も HTTPS を使用して常にセキュリティ保護されます。 HTTPS は、Data Lake Store REST インターフェイスでサポートされる唯一のプロトコルです。

## <a name="summary"></a>概要

会社は、Azure Storage への HTTPS 接続を強制し、Shared Access Signatures を使い、ストレージ アカウントで [安全な転送が必須] を有効にして、個人データとそのプライバシーの保護の目標を達成できます。 SMB 3.0 接続を使い、クライアント側暗号化を実装して、個人データを保護することもできます。 企業ネットワークから Azure 仮想ネットワークへのサイト間 VPN 接続と、個々のユーザーからのポイント対サイト VPN 接続では、個人データを安全に移動できるセキュリティで保護されたトンネルが作成されます。 Microsoft の既定の暗号化プラクティスは、個人データのプライバシーをさらに保護します。

## <a name="next-steps"></a>次のステップ

- [Azure のデータ セキュリティと暗号化のベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [VPN Gateway の計画と設計](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN Gateway に関する FAQ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Azure App Service の SSL 証明書を購入して構成する](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
