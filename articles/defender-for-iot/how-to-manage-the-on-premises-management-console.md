---
title: オンプレミスの管理コンソールを管理する
description: バックアップと復元、ホスト名の定義、センサーへのプロキシの設定など、オンプレミスの管理コンソールのオプションについて説明します。
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 871c74eee4b74538a8a09188953916ff7376bc8d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781723"
---
# <a name="manage-the-on-premises-management-console"></a>オンプレミスの管理コンソールを管理する

この記事では、バックアップと復元、コミット済みデバイスのアクティブ化ファイルのダウンロード、証明書の更新、センサーへのプロキシの設定など、オンプレミスの管理コンソールのオプションについて説明します。

Azure portal からオンプレミスの管理コンソールをオンボードします。

## <a name="upload-an-activation-file"></a>アクティブ化ファイルをアップロードする

初めてサインインすると、オンプレミスの管理コンソールのアクティブ化ファイルがダウンロードされます。 このファイルには、オンボード プロセス中に定義された、コミット済みのデバイスの集合が含まれます。 この一覧には、複数のサブスクリプションに関連付けられているセンサーが含まれます。

最初にアクティブ化した後に、監視するデバイスの数が、オンボード中に定義されたコミット済みのデバイスの数を超えることがあります。 このイベントは、たとえば、管理コンソールにより多くのセンサーを接続した場合に発生する可能性があります。 監視するデバイスの数とコミット済みデバイスの数が一致しない場合は、管理コンソールに警告が表示されます。 このイベントが発生した場合は、新しいアクティブ化ファイルをアップロードする必要があります。

アクティブ化ファイルをアップロードするには:

1. Azure Defender for IoT の **[価格]** ページに移動します。
1. **[管理コンソールのアクティブ化ファイルのダウンロード]** タブを選択します。アクティブ化ファイルがダウンロードされます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="アクティブ化ファイルをダウンロードします。":::

1. 管理コンソールで **[システム設定]** を選択します。
1. **[アクティブ化]** を選択します。
1. **[ファイルの選択]** を選択し、保存したファイルを選択します。

## <a name="manage-certificates"></a>証明書の管理

オンプレミスの管理コンソールのインストール後に、ローカルの自己署名証明書が生成され、管理コンソールの Web アプリケーションにアクセスするために使用されます。 管理者ユーザーが管理コンソールに初めてサインインすると、SSL/TLS 証明書を指定するように求められます。 初回セットアップの詳細については、「[オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)」を参照してください。

次のセクションでは、証明書の更新、証明書の CLI コマンドの使用、サポートされている証明書と証明書のパラメーターについて説明します。

### <a name="about-certificates"></a>証明書について

Azure Defender for IoT は、SSL と TLS 証明書を使用して次のことを行います。

- CA 署名証明書をアップロードして、組織によって要求された特定の証明書と暗号化の要件を満たす。

- 管理コンソールと接続されたセンサーの間、および管理コンソールと高可用性管理コンソールの間の検証を行えるようにする。 検証は、証明書失効リストと証明書の有効期限に対して評価されます。 *検証が失敗すると、管理コンソールとセンサー間の通信が停止され、検証エラーがコンソールに表示されます*。 このオプションは、インストール後に既定で有効になります。

サードパーティの転送ルールは検証されません。 たとえば、SYSLOG、Splunk、ServiceNow に送信されるアラート情報、Active Directory との通信などです。

#### <a name="ssl-certificates"></a>SSL 証明書の数

Defender for IoT センサーとオンプレミスの管理コンソールは、次の機能のために SSL と TLS 証明書を使用します。 

 - ユーザーとアプライアンスの Web コンソールとの間のセキュリティで保護された通信。 
 
 - センサーおよびオンプレミスの管理コンソール上での REST API へのセキュリティで保護された通信。
 
 - センサーとオンプレミスの管理コンソールとの間のセキュリティで保護された通信。 

インストールされると、アプライアンスによって、ローカルの自己署名証明書が生成され、Web コンソールへの暫定的なアクセスが可能になります。 エンタープライズ SSL および TLS 証明書は、[`cyberx-xsense-certificate-import`](#cli-commands) コマンドライン ツールを使用してインストールできます。 

 > [!NOTE]
 > アプライアンスがセッションのクライアントかつイニシエーターである統合および転送ルールでは、特定の証明書が使用され、システム証明書に関連付けられません。  
 >
 >このような場合、証明書を通常サーバーから受け取るか、統合をセットアップするための特定の証明書が提供される非対称暗号化を使用します。 

### <a name="update-certificates"></a>証明書を更新する

オンプレミスの管理コンソールの管理者ユーザーは、証明書を更新できます。

証明書を更新するには:  

1. **[システム設定]** を選択します。

1. **[SSL/TLS Certificates]\(SSL/TLS 証明書\)** を選択します。
1. 証明書を削除または編集し、新しいものを追加します。
   
   - 証明書名を追加します。
   
   - CRT ファイルとキー ファイルをアップロードし、パスフレーズを入力します。
   - 必要に応じて PEM ファイルをアップロードします。

検証設定を変更するには:

1. **[Enable Certificate Validation]\(証明書の検証を有効にする\)** トグルをオンまたはオフにします。

1. **[保存]** を選択します。

オプションが有効になり、検証が失敗した場合は、管理コンソールとセンサー間の通信が停止し、検証エラーがコンソールに表示されます。

### <a name="certificate-support"></a>証明書のサポート

次の証明書がサポートされています。

- プライベートおよびエンタープライズ キー インフラストラクチャ (プライベート PKI)
 
- 公開キー基盤 (パブリック PKI) 

- アプライアンスのローカルで生成された (ローカルで自己署名された) もの 

  > [!IMPORTANT]
  > 自己署名証明書の使用は推奨されません。 この種類の接続はセキュリティで保護されていないため、テスト環境でのみ使用してください。 証明書の所有者を検証できず、システムのセキュリティを維持することもできないため、自己署名証明書は実稼働ネットワークには使用しないでください。

### <a name="supported-ssl-certificates"></a>サポートされている SSL 証明書 

サポートされているパラメーターは次のとおりです。 

**証明書 CRT**

- ドメイン名のプライマリ証明書ファイル

- 署名アルゴリズム = SHA256RSA
- 署名ハッシュ アルゴリズム = SHA256
- 有効期間の開始 = 有効な過去の日付
- 有効期間の終了 = 有効な将来の日付
- 公開キー = RSA 2048 ビット (最小) または 4096 ビット
- CRL 配布ポイント = .crl ファイルへの URL
- サブジェクト CN = URL (ワイルドカード証明書を使用可能)。例: Sensor.contoso.<span>com または *.contoso.<span>com
- サブジェクトの国 (C) = 定義済み。例: US
- サブジェクトの組織単位 (OU) = 定義済み。例: Contoso Labs
- サブジェクトの組織 (O) = 定義済み。例: Contoso Inc

**キー ファイル**

- CSR を作成したときに生成されたキー ファイル

- RSA 2048 ビット (最小) または 4096 ビット

 > [!Note]
 > 4096 ビットのキー長を使用する場合:
 > - 各接続の開始時の SSL ハンドシェイクが遅くなります。  
 > - ハンドシェイク中に CPU 使用率が増加します。 

**証明書チェーン**

- CA によって提供された中間証明書ファイル (存在する場合)。

- サーバーの証明書を発行した CA の証明書をファイルの先頭に配置し、その後にルートまでの他のものを配置する必要があります。 
- チェーンには、バッグ属性を含めることができます。

**パスフレーズ**

- 1 つのキーがサポートされています。

- 証明書をインポートするときに設定します。

他のパラメーターを含む証明書が機能する場合がありますが、Microsoft ではサポートされません。

#### <a name="encryption-key-artifacts"></a>暗号化キー成果物

**.pem: 証明書コンテナー ファイル**

Privacy Enhanced Mail (PEM) ファイルは、メールをセキュリティで保護するために使用される一般的なファイルの種類です。 最近では、PEM ファイルは証明書と共に使用され、x509 ASN1 キーを使用します。  

コンテナー ファイルは、RFC 1421 から 1424 で定義されています。これは、公開証明書のみを含む可能性があるコンテナー形式です。 たとえば、Apache では、CA 証明書、ファイル、ETC、SSL、CERTS がインストールされます。 これには公開キー、秘密キー、ルート証明書など、証明書チェーン全体が含まれる場合があります。  

また、CSR を PKCS10 形式としてエンコードすることもでき、それを PEM に変換できます。

**.cert、.cer、.crt: 証明書コンテナー ファイル**

異なる拡張子を使用した `.pem`、または `.der` 形式のファイル。 このファイルは、エクスプローラーで証明書として認識されます。 エクスプローラーでは `.pem`  ファイルは認識されません。

**.key: 秘密キー ファイル**

キー ファイルは PEM ファイルと同じ形式ですが、拡張子は異なります。 

#### <a name="additional-commonly-available-key-artifacts"></a>その他の一般的に利用可能な主要成果物

**.csr  - 証明書署名要求**  

このファイルは、証明機関に送信するために使用されます。 実際の形式は、RFC 2986 で定義されている PKCS10 で、要求された証明書の一部またはすべてのキーの詳細が含まれている場合があります。 たとえば、サブジェクト、組織、都道府県などです。 これは、CA によって署名され、引き換えに証明書を受け取る、証明書の公開キーです。  

返される証明書は公開証明書であり、公開キーは含まれていますが、秘密キーは含まれていません。 

**.pkcs12 .pfx .p12 - パスワード コンテナー** 

当初は公開キー暗号化標準 (PKCS) として RSA によって定義されましたが、12 バリアントは当初は Microsoft によって拡張され、その後 RFC 7292 として提出されました。  

このコンテナー形式では、公開とプライベートの証明書のペアを含むパスワードが必要です。 `.pem`  ファイルと異なり、このコンテナーは完全に暗号化されています。  

OpenSSL を使用して、公開キーと秘密キーの両方を含む `.pem`  ファイルにファイルを変換できます。 `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der - バイナリでエンコードされた PEM**。

ASN.1 構文をバイナリでエンコードするには、`.pem`  ファイル (単に Base64 でエンコードされた `.der` ファイル) を使用します。 

OpenSSL では、これらのファイルを `.pem` に変換できます:  `openssl x509 -inform der -in to-convert.der -out converted.pem`  

これらのファイルは、Windows によって証明書ファイルとして認識されます。 既定では、Windows では証明書は別の拡張子を持つ `.der` 形式のファイルとしてエクスポートされます。

**.crl - 証明書の失効リスト**.  

証明機関によって、有効期限が切れる前に証明書を承認解除する方法としてこれらが生成されます。 

#### <a name="cli-commands"></a>CLI コマンド

`cyberx-xsense-certificate-import` CLI コマンドを使用して、証明書をインポートします。 このツールを使用するには、WinSCP や Wget などのツールを使用して、証明書ファイルをデバイスにアップロードする必要があります。

このコマンドは、次の入力フラグをサポートしています。

- `-h`:コマンドライン ヘルプの構文を表示します。

- `--crt`:証明書ファイル (.crt 拡張子) へのパス。

- `--key`: \*.key ファイル。 キーの長さは、2,048 ビット以上である必要があります。

- `--chain`:証明書チェーン ファイルへのパス (省略可能)。

- `--pass`:証明書の暗号化に使用されるパスフレーズ (省略可能)。

- `--passphrase-set`:既定値 = `False` (使用しない)。 前の証明書で指定された前のパスフレーズを使用するには、`True` に設定します (省略可能)。

CLI コマンドを使用する場合:

- アプライアンスで証明書ファイルが読み取り可能であることを確認します。

- 証明書のドメイン名と IP が、IT 部門が計画した構成と一致していることを確認します。

### <a name="use-openssl-to-manage-certificates"></a>OpenSSL を使用して証明書を管理する

次のコマンドを使用して証明書を管理します。

| [説明] | CLI コマンド |
|--|--|
| 新しい秘密キーと証明書署名要求を生成する | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 自己署名証明書の生成 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 既存の秘密キーの証明書署名要求 (CSR) を生成する | `openssl req -out CSR.csr -key privateKey.key -new` |
| 既存の証明書に基づく証明書署名要求を生成する | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 秘密キーからパスフレーズを削除する | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

証明書、CSR、または秘密キー内の情報を確認する必要がある場合は、次のコマンドを使用します。

| [説明] | CLI コマンド |
|--|--|
| 証明書署名要求 (CSR) を確認する | `openssl req -text -noout -verify -in CSR.csr` |
| 秘密キーを確認する | `openssl rsa -in privateKey.key -check` |
| 証明書を確認する | `openssl x509 -in certificate.crt -text -noout`  |

秘密キーが証明書と一致しない、またはサイトにインストールした証明書が信頼されていないというエラーが表示された場合は、次のコマンドを使用してエラーを修正します。

| [説明] | CLI コマンド |
|--|--|
| 公開キーの MD5 ハッシュを確認して、CSR または秘密キーの内容と一致していることを確認する | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

証明書とキーを別の形式に変換して、特定の種類のサーバーまたはソフトウェアと互換性を持たせるには、次のコマンドを使用します。

| [説明] | CLI コマンド |
|--|--|
| DER ファイル (.crt .cer .der) を PEM に変換する  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PEM ファイルを DER に変換する | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 秘密キーと証明書を含む PKCS#12 ファイル (.pfx. p12) を PEM に変換する | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />`-nocerts` を追加して秘密キーのみを出力するか、`-nokeys` を追加して証明書のみを出力することができます。 |
| PEM 証明書ファイルと秘密キーを PKCS#12 (.pfx .p12) に変換する | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>バックアップと復元の設定を定義する

オンプレミスの管理コンソールのシステム バックアップが毎日自動的に実行されます。 データは別のディスクに保存されます。 既定の場所は `/var/cyberx/backups` です。 

このファイルを内部ネットワークに自動的に転送できます。 

> [!NOTE]
> バックアップと復元の手順は、同じバージョンでのみ実行できます。 

オンプレミスの管理コンソール マシンをバックアップするには: 

- 管理アカウントにサインインし、「`sudo cyberx-management-backup -full`」と入力します。

最新のバックアップ ファイルを復元するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-management-system-restore`」と入力します。

外部の SMB サーバーにバックアップを保存するには:

1. 外部の SMB サーバーに共有フォルダーを作成します。  

   SMB サーバーへのアクセスに必要なフォルダー パス、ユーザー名、パスワードを取得します。 

2. Defender for IoT で、バックアップ用のディレクトリを作成します。

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. fstab を編集します。  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. 共有する SMB サーバーの資格情報を編集または作成します。 

   - `sudo nano /etc/samba/user` 

5. 次の項目を追加します。 

   - `username=<user name>`

   - `password=<password>` 

6. ディレクトリをマウントします。 

   - `sudo mount -a` 

7. Defender for IoT オンプレミス管理コンソールの共有フォルダーにバックアップ ディレクトリを構成します。  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>ホスト名を編集する

組織の DNS サーバーで構成されている管理コンソールのホスト名を編集するには:

1. 管理コンソールの左ペインで **[システム設定]** を選択します。  

2. コンソールのネットワーク セクションで、 **[ネットワーク]** を選択します。 

3. 組織の DNS サーバーで構成されているホスト名を入力します。 

4. **[保存]** を選択します。

## <a name="define-vlan-names"></a>VLAN 名を定義する

VLAN 名は、センサーと管理コンソールの間で同期されません。 コンポーネントで同じ名前を定義する必要があります。

ネットワーク領域で **[VLAN]** を選択し、検出された VLAN ID に名前を追加します。 次に、 **[保存]** を選択します。

## <a name="define-a-proxy-to-sensors"></a>センサーへのプロキシを定義する

ユーザーがセンサーに直接サインインできないようにすることで、システムのセキュリティを強化します。 代わりに、プロキシ トンネリングを使用して、ユーザーが 1 つのファイアウォール規則でオンプレミスの管理コンソールからセンサーにアクセスできるようにします。 この機能強化により、センサーを介してネットワーク環境に不正にアクセスされる可能性が減少します。

センサーへの直接接続がない環境では、プロキシを使用します。

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="ユーザー。":::

次の手順では、センサーをオンプレミスの管理コンソールに接続し、そのコンソールでトンネリングを有効にします。

1. 管理者資格情報を使用して、オンプレミスの管理コンソール アプライアンスの CLI にサインインします。

2. 「`sudo cyberx-management-tunnel-enable`」と入力し、**Enter** キーを押します。

4. 「`--port 10000`」と入力し、**Enter** キーを押します。

## <a name="adjust-system-properties"></a>システム プロパティを調整する

システム プロパティにより、管理コンソールのさまざまな操作と設定が制御されます。 それらを編集または変更すると、管理コンソールの操作に悪影響を及ぼす可能性があります。 設定を変更する前に、[Microsoft サポート](https://support.microsoft.com)にお問い合わせください。

システム プロパティにアクセスするには: 

1. オンプレミス管理コンソールまたはセンサーにサインインします。

2. **[システム設定]** を選択します。

3. **[全般]** セクションで **[システム プロパティ]** を選択します。

## <a name="change-the-name-of-the-on-premises-management-console"></a>オンプレミスの管理コンソールの名前を変更する

オンプレミスの管理コンソールの名前を変更できます。 コンソール Web ブラウザー、さまざまなコンソール ウィンドウ、トラブルシューティング ログに新しい名前が表示されます。 既定の名前は **管理コンソール** です。

名前を変更するには:

1. 左ペインの下部で、現在の名前を選択します。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="オンプレミスの管理コンソール バージョンのスクリーンショット。":::

2. **[Edit management console configuration]\(管理コンソールの構成の編集\)** ダイアログ ボックスで、新しい名前を入力します。 名前は 25 文字以下にする必要があります。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Defender for IoT プラットフォーム構成の編集のスクリーンショット。":::

3. **[保存]** を選択します。 新しい名前が適用されます。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="変更されたコンソール名を示すスクリーンショット。":::

## <a name="password-recovery"></a>パスワードの回復

オンプレミスの管理コンソールのパスワードの回復は、デバイスがアタッチされているサブスクリプションに関連付けられています。 デバイスがアタッチされているサブスクリプションがわからない場合、パスワードを回復することはできません。

パスワードをリセットするには:

1. オンプレミスの管理コンソールのサインイン ページに移動します。
1. **[パスワードの回復]** を選択します。
1. 一意識別子をコピーします。
1. Defender for IoT の **[サイトとセンサー]** ページに移動し、 **[パスワードの回復]** タブを選択します。
1. 一意識別子を入力し、 **[回復]** を選択します。 アクティブ化ファイルがダウンロードされます。
1. **[パスワードの回復]** ページに移動し、アクティブ化ファイルをアップロードします。
1. **[次へ]** を選択します。
 
   これで、ユーザー名と、システムによって生成された新しいパスワードが付与されます。

> [!NOTE]
> センサーは、最初に接続されていたサブスクリプションにリンクされます。 パスワードは、アタッチ先のサブスクリプションと同じものを使用してのみ回復できます。

## <a name="update-the-software-version"></a>ソフトウェアのバージョンを更新する

次の手順では、オンプレミス管理コンソール ソフトウェアのバージョンを更新する方法について説明します。 更新プロセスには約 30 分かかります。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

1. Defender for IoT にアクセスします。

1. **[更新プログラム]** ページに移動します。

1. オンプレミス管理コンソール セクションからバージョンを選択します。

1. **[Download]\(ダウンロード\)** を選択してファイルを保存します。

1. オンプレミス管理コンソールにログインし、サイド メニューから **[システム設定]** を選択します。

1. **[バージョンのアップグレード]** ペインで、 **[アップグレード]** を選択します。

1. Defender for IoT の **[更新プログラム]** ページからダウンロードしたファイルを選択します。

## <a name="mail-server-settings"></a>メール サーバーの設定

オンプレミス管理コンソールの SMTP メールサーバー設定を定義します。

定義するには:

1. 管理者資格情報を使用して、オンプレミス管理用の CLI にサインインします。
1. 「```nano /var/cyberx/properties/remote-interfaces.properties```」と入力します。
1. [Enter] を選択します。 次のプロンプトが表示されます。
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. SMTP サーバーの名前と送信者を入力し、Enter キーを押します。

## <a name="see-also"></a>関連項目

[管理コンソールからセンサーを管理する](how-to-manage-sensors-from-the-on-premises-management-console.md)

[個々のセンサーの管理](how-to-manage-individual-sensors.md)
