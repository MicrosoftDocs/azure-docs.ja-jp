title: Azure の暗号化で個人の保存データを保護する | Microsoft Docs description: この記事は、Azure を使用して個人データを保護する方法を説明するシリーズの 1 つです。services: security documentationcenter: na author: Barclayn manager: MBaldwin editor: TomSh

ms.assetid: ms.service: security ms.devlang: na ms.topic: article ms.tgt_pltfrm: na ms.workload: na ms.date: 08/22/2017 ms.author: barclayn ms.custom: 

---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure の暗号化テクノロジ: 個人の保存データを暗号化で保護する

この記事では、保存データをセキュリティで保護する Azure の暗号化テクノロジについて説明します。

保存データの暗号化は、機密データや個人データを保護し、コンプライアンスとデータのプライバシー要件を満たすベスト プラクティスです。
Encryption at Rest は、データをディスク上で確実に暗号化することで、暗号化されていないデータに攻撃者がアクセスできないよう設計されています。

## <a name="scenario"></a>シナリオ 

米国に本社を置く大規模なクルーズ会社が、地中海とバルト海だけでなくイギリス諸島での旅程を提案して、その事業を拡大しています。 こうした取り組みを支援するために、イタリア、ドイツ、デンマーク、英国に拠点を置くいくつかの小規模なクルーズ会社を買収しました。

この会社は、Microsoft Azure を使用して、クラウドに会社のデータを保存しています。 このデータには、次のような顧客や従業員の情報が含まれています。

- 住所
- 電話番号
- 納税者番号
- クレジット カード情報

この会社では、顧客や従業員のデータのプライバシーを保護する必要があり、関係部署が必要な時にいつでもデータにアクセスできるようにしておく必要があります。 (給与を管理する部署や、予約を受け付ける部署など)

このクルーズ会社は報酬やロイヤリティ プログラム メンバーについての大規模なデータベースも管理しており、このデータベースには現在および過去の顧客との関係を追跡できる個人情報なども含まれています。

### <a name="problem-statement"></a>問題の説明

顧客や従業員の個人データのプライバシーを保護する必要がある一方で、関係部署が必要な時にいつでもデータにアクセスできるようにしておく必要があります (給与を管理する部署や、予約を受け付ける部署など)。 この個人データは、この会社が管理するデータ センターの外に保存されており、この会社の物理的な管理下にはありません。

### <a name="company-goal"></a>会社の目標

マルチレイヤーの多層防御セキュリティ戦略の一環として、個人データを含むすべてのデータ ソースがクラウド ストレージに格納されるなどして暗号化されることが会社の目標です。 権限のない人物が個人データにアクセスしてきた場合に備えて、データが判読できない形式に変換されている必要があります。 暗号化は、ユーザーと管理者にとって適用が簡単であるか、透過的である必要があります。

## <a name="solutions"></a>解決方法

Azure サービスでは、暗号化による個人の保存データの保護に役立つ複数のツールとテクノロジを提供しています。

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) は、Azure サービス内の保存データの暗号化に役立つキーをセキュリティ保護されたストレージに提供する、推奨のキー ストレージ管理ソリューションです。 暗号化キーの管理は、保存データをセキュリティ保護するために不可欠です。

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Azure Key Vault を使用して個人データの暗号化キーを保護する方法

Azure Key Vault を使用するには、Azure アカウントのサブスクリプションが必要です。 Azure PowerShell もインストールしておく必要があります。 PowerShell コマンドレットの使用方法などの手順は次のとおりです。

1. サブスクリプションへの接続

2. Key Vault を作成します

3. キーやシークレットを Key Vault に追加します

4. Azure Active Directory と連携して Key Vault を使用するためのアプリケーションを登録します

5. キーまたはシークレットを使用するために、それらのアプリケーションを承認します

キー コンテナーを作成するには、New-AzureRmKeyVault PowerShell コマンドレットを使用します。 コンテナー名、リソース グループ名、地理的な場所を割り当てます。 他のコマンドレットを使用してキーを管理するときは、そのコンテナー名を使用します。 REST API 経由でコンテナーを使用するアプリケーションでは、コンテナー URI を使用します。

Azure Key Vault はソフトウェアで保護されたキーを提供しますが、.PFX ファイルを使用して既存のキーをインポートすることもできます。 コンテナーにシークレット (パスワード) を格納することもできます。

ローカルの HSM でキーを生成して、キーを HSM の境界内から出すことなく、Key Vault サービスで HSM に転送することができます。

Azure Key Vault の使用方法の詳細については、「[Azure Key Vault の概要](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started)」の手順をご覧ください。

Azure Key Vault で使用する PowerShell コマンドレットの一覧については、「[AzureRM.KeyVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)」をご覧ください。

### <a name="azure-disk-encryption-for-windows"></a>Windows 用の Azure Disk Encryption

[Windows と Linux の IaaS VM 用の Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) は、Azure 仮想マシン上の個人の保存データを保護するサービスで、Azure Key Vault と統合されています。 Azure Disk Encryption では、Windows の [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) や Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) を使用して、OS とデータ ディスクの両方を暗号化します。 Azure Disk Encryption は、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows 8 と Windows 10 のクライアントでサポートされています。

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Azure Disk Encryption を使用して個人データを保護する方法

Azure Disk Encryption を使用するには、Azure アカウントのサブスクリプションが必要です。 Windows と Linux の VM の Azure Disk Encryption を有効にするには、次の操作を行います。

1. Azure Disk Encryption Resource Manager テンプレート、PowerShell、またはコマンド ライン インターフェイス (CLI) を使用し、ディスク暗号化を有効にして暗号化構成を指定します。 

2. Azure プラットフォームへのアクセスを許可して、暗号化マテリアルをキー コンテナーから読み取ります。

3. キー コンテナーに暗号化キー マテリアルを書き込むための Azure Active Directory (AAD) アプリケーション ID を指定します。

Azure が VM とキー コンテナー構成を更新し、暗号化された VM を設定します。

キー コンテナーを設定して Azure Disk Encryption をサポートすると、追加セキュリティとしてキー暗号化キー (KEK) を追加して、暗号化された仮想マシンのバックアップをサポートできます。

![](media/protect-personal-data-at-rest/create-key.png)

具体的なデプロイ シナリオとユーザー エクスペリエンスの詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)」をご覧ください。

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

[Azure Storage Service Encryption (SSE) for Data at Rest](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) は、データを保護して安全性を確保し、組織のセキュリティとコンプライアンスの要件を満たすお手伝いをします。 Azure Storage は、ストレージに保存する前に 256 ビット AES 暗号化を使用してデータを自動的に暗号化したり、取得する前に復号化したりします。 このサービスは、Azure Blob と Azure Files で利用できます。

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Storage Service Encryption を使用して個人データを保護する方法

Storage Service Encryption を有効にするには、次の操作を行います。

1. Azure Portal にログインします。

2. ストレージ アカウントを選択します。

3. [設定] の [Blob Service] セクションで、[暗号化] を選択します。

4. [File サービス] セクションで、[暗号化] を選択します。

[暗号化] をクリックした後、Storage Service Encryption を有効または無効にできます。

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

新しいデータは暗号化されます。 このストレージ アカウントの既存ファイル内のデータは暗号化されずに残ります。

暗号化を有効にしたあと、次のいずれかの方法でデータをストレージ アカウントにコピーします。

1. [AzCopy コマンド ライン ユーティリティ](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy)を使用して BLOB またはファイルをコピーします。

2. [SMB を使用してファイル共有をマウントする](https://docs.microsoft.com/en-us/azure/storage/storage-file-how-to-use-files-windows)と、Robocopy などのユーティリティを使用してファイルをコピーできます。

3. [.NET などのストレージ クライアント ライブラリ](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs)を使用して BLOB またはファイルのデータをコピーし、BLOB ストレージやストレージ アカウント間でやりとりできます。

4.  [ストレージ エクスプローラー](https://docs.microsoft.com/en-us/azure/storage/storage-explorers)を使用して、暗号化を有効にした状態で BLOB をストレージ アカウントにアップロードします。

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

Transparent Data Encryption (TDE) は SQL Azure の機能の 1 つで、データベース レベルとサーバー レベルの両方のデータを暗号化できます。 現在、新しく作成されたデータベースではすべて、TDE が既定で有効になっています。 TDE は、データとログ ファイルの I/O 暗号化と複合化をリアルタイムで実行します。

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>TDE を使用して個人データを保護する方法

REST API または PowerShell を使用して、Azure Portal で TDE を構成できます。 Azure Portal を使用して既存のデータベースで TDE を有効にするには、次の操作を行います。

1. Azure Portal (<https://portal.azure.com>) にアクセスして、Azure 管理者または共同作成者のアカウントでサインインします。

2. 左側のバナーで 参照をクリックし、SQL データベースをクリックします。

3. 左側のウィンドウで SQL データベースを選択して、ご利用のユーザー データベースをクリックします。

4. そのデータベースのブレードで [すべての設定] をクリックします。

5. [設定] ブレードで [Transparent Data Encryption] の箇所をクリックして、[Transparent Data Encryption] ブレードを開きます。

6. Data Encryption ブレードで Data Encryption ボタンをオンにしてから、ページの上部にある 保存をクリックして設定を適用します。 暗号化の状態は、透過的なデータ暗号化の進行状況とほぼ同様です。

![データの暗号化を有効にする](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

TDE を有効にする方法と、TDE で保護されたデータベースの複合化に関する情報の詳細については、[Azure SQL Database での Transparent Data Encryption](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) に関する記事をご覧ください。

## <a name="summary"></a>概要

Azure のクラウドに個人データを格納して暗号化することで、会社の目標を達成できます。 Azure Disk Encryption を使用してボリューム全体を保護することで実現します。 これには、オペレーティング システム ファイルと、個人を特定できる情報などの機密データを含むデータ ファイルの両方が含まれている場合があります。 Azure Storage サービスの暗号化は、BLOB やファイルに格納される個人データを保護するために使用されます。 Azure SQL データベースに格納されるデータは、Transparent Data Encryption によって個人情報への不正アクセスから保護されます。

Azure でデータを暗号化するために使用されるキーを保護するには、Azure Key Vault を使用します。 これにより、キー管理プロセスを合理化し、会社は個人データにアクセスして暗号化するキーの制御を維持できます。

## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption トラブルシューティング ガイド](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-tsg)

- [Azure Virtual Machine の暗号化](https://docs.microsoft.com/en-us/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Azure Data Lake Store でのデータの暗号化](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption)

- [保存時の Azure Cosmos DB データベースの暗号化](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)
