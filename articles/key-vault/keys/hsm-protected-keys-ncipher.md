---
title: Azure Key Vault の HSM 保護キーを生成し、転送する方法 - Azure Key Vault
description: この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: f7761cf011a3a678bb7609e1063ac6ebec90d395
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499188"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Key Vault の HSM で保護されたキー (nCipher) をインポートする

> [!WARNING]
> このドキュメントで説明している HSM キー インポート方法は **非推奨** となっており、今後はサポートされません。 対応するのは、ファームウェア 12.40.2 または 12.50 と修正プログラムがインストールされた nCipher nShield ファミリの HSM のみです。 [新しい方法を使用して HSM キーをインポート](hsm-protected-keys-byok.md)することを強くお勧めします。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Key Vault の使用時にさらに安心感を高める場合、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。 Azure Key Vault では、HSM の nCipher nShield ファミリ (FIPS 140-2 レベル 2 で検証済み) を使用してキーが保護されます。


このトピックの情報は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 

この機能は Azure China 21Vianet では使用できません。

> [!NOTE]
> Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../general/overview.md)  
> HSM で保護されたキーの Key Vault 作成を含む入門チュートリアルについては、「[Azure Key Vault とは](../general/overview.md)」を参照してください。

HSM 保護キーを生成し、インターネットで転送する方法:

* 攻撃を減らすオフライン ワークステーションからキーを生成します。
* キーは Key Exchange Key (KEK) で暗号化されます。Azure Key Vault HSM に転送されるまで暗号化が維持されます。 暗号化されたキーだけが元のワークステーションを離れます。
* ツールセットにより、キーを Azure Key Vault セキュリティ ワールドにバインディングするテナント キーのプロパティが設定されます。 そのため、Azure Key Vault HSM がキーを受け取り、復号化すると、これらの HSM のみでそれを使用できます。 キーはエクスポートできません。 このバインディングは nCipher HSM により強制されます。
* キーの暗号化に使用される Key Exchange Key (KEK) は Azure Key Vault HSM 内で生成され、エクスポートできません。 HSM により、HSM の外部では平文の KEK がありません。 また、ツールセットには、KEK はエクスポート不可であり、nCipher で作成された本物の HSM 内で生成されたことを示す nCipher からの構成証明が含まれます。
* ツールセットには、Azure Key Vault セキュリティ ワールドも nCipher で作成された本物の HSM 内で生成されたことを示す nCipher からの証明が含まれます。 これにより、Microsoft が本物のハードウェアを使用していることが証明されます。
* Microsoft では、リージョンごとに個別の KEK と個別のセキュリティ ワールドを使用します。 この分離により、キーを暗号化したリージョンのデータ センターでのみ、そのキーを使用できるようにします。 たとえば、欧州のお客様からのキーは北米やアジアのデータ センターでは使用できません。

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>nCipher HSM と Microsoft のサービスの詳細

Entrust Datacard 企業である nCipher Security は、汎用 HSM 市場のトップ企業であり、ビジネスに重要な情報やアプリケーションに信頼、整合性、制御を提供することで、世界有数の組織を支援しています。 nCipher の暗号化ソリューションでは、最新のテクノロジ (クラウド、IoT、ブロックチェーン、デジタル支払い) をセキュリティで保護し、グローバル組織が現在利用しているものと同じ実証済みのテクノロジを使用して、新しいコンプライアンス要件を満たすように支援し、機密データ、ネットワーク通信、およびエンタープライズ インフラストラクチャに対する脅威から保護しています。 nCipher は、現在、将来、そして常に、データの整合性を確保し、顧客を完全に制御してビジネス クリティカル アプリケーションの信頼を実現します。

Microsoft は nCipher Security と連携し、HSM の最新技術を強化しています。 この改良により、キーの管理を放棄することなく、ホステッド サービスの一般的な長所を得ることができます。 具体的には、この改良により、Microsoft があなたの代わりに HSM を管理できます。 クラウド サービスとしては、Azure Key Vault は短期間でスケールアップされ、組織の利用率急増に対応します。 同時に、キーは Microsoft の HSM 内で保護されます。ユーザーがキーを生成し、それを Microsoft の HSM に転送するため、キーが存続する間、ユーザーがそれを管理します。

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Azure Key Vault の Bring Your Own Key (BYOK) の実装

独自の HSM 保護キーを生成し、それを Azure Key Vault に転送する場合 (Bring Your Own Key (BYOK) シナリオ)、次の情報と手順を利用します。

## <a name="prerequisites-for-byok"></a>BYOK の前提条件

Azure Key Vault の Bring Your Own Key (BYOK) の前提条件の一覧については、次の表を参照してください。

| 要件 | 詳細情報 |
| --- | --- |
| Azure のサブスクリプション |Azure Key Vault を作成するには、Azure サブスクリプションが必要です: [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/) |
| HSM で保護されたキーをサポートする Azure Key Vault Premium サービス レベル |Azure Key Vault のサービス層と機能に関する詳細については、 [Azure Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/) Web サイトを参照してください。 |
| nCipher nShield HSM、スマート カード、サポート ソフトウェア |nCipher ハードウェア セキュリティ モジュールにアクセスできることと nCipher nShield HSM の基本操作知識が必要です。 互換性のあるモデルの一覧については、あるいは所有していない場合に HSM を購入する方法については、[nCipher nShield ハードウェア セキュリティ モジュール](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206)に関するページを参照してください。 |
| 次のハードウェアとソフトウェア:<ol><li>Windows 7 以降のオペレーティング システムと、バージョン 11.50 以降の nCipher nShield ソフトウェアを搭載したオフラインの x64 ワークステーション。<br/><br/>ワークステーションで Windows 7 を実行する場合は、まず [Microsoft .NET Framework 4.5 をインストール](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)する必要があります。</li><li>インターネットに接続している、Windows 7 以降および [Azure PowerShell](/powershell/azure/) **1.1.0 以降** の Windows オペレーティング システムがインストールされたワークステーション。</li><li>USB ドライブまたは 16 MB 以上の空き領域を持つその他のポータブル ストレージ デバイス。</li></ol> |セキュリティ上の理由から、最初のワークステーションをネットワークに接続しないことをお勧めします。 ただし、プログラムを使用して強制的に接続が切断されることはありません。<br/><br/>次の手順では、このワークステーションを "未接続ワークステーション" と呼んでいます。</p></blockquote><br/>さらに、テナント キーが実稼動ネットワークにある場合は、別の 2 台目のワークステーションを使用してツールセットをダウンロードし、テナント キーをアップロードすることを勧めします。 ただし、テスト目的で1 台目のワークステーションとして同じワークステーションを使用できます。<br/><br/>次の手順では、この 2 台目のワークステーションを "インターネット接続ワークステーション" と呼んでいます。</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>キーを生成し、Azure Key Vault HSM に転送する

次の 5 つの手順でキーを生成し、Azure Key Vault HSM に転送します。

* [ステップ 1:インターネット接続ワークステーションを準備する](#step-1-prepare-your-internet-connected-workstation)
* [手順 2:未接続ワークステーションを準備する](#step-2-prepare-your-disconnected-workstation)
* [ステップ 3:キーを生成する](#step-3-generate-your-key)
* [手順 4:キーの転送準備をする](#step-4-prepare-your-key-for-transfer)
* [手順 5:キーを Azure Key Vault に転送する](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>手順 1:インターネット接続ワークステーションを準備する

この最初の手順では、インターネットに接続されているワークステーションで次の手順を実行します。

### <a name="step-11-install-azure-powershell"></a>手順 1.1: Azure PowerShell をインストールする

インターネット接続ワークステーションから、Azure Key Vault を管理するためのコマンドレットを含む Azure PowerShell をダウンロードしてインストールします。 インストール指示については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。

### <a name="step-12-get-your-azure-subscription-id"></a>手順 1.2: Azure サブスクリプション ID を取得する

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

```Powershell
   Connect-AzAccount
```
ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 次に [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドを使用します。

```powershell
   Get-AzSubscription
```
出力から、Azure Key Vault で使用するサブスクリプションの ID を見つけます。 このサブスクリプション ID は後で必要になります。

Azure PowerShell ウィンドウを閉じないでください。

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>手順 1.3: Azure Key Vault の BYOK ツールセットをダウンロードする

Microsoft ダウンロード センターにアクセスし、自分の地域リージョンまたは Azure のインスタンスの [Azure Key Vault BYOK ツールセットをダウンロード](https://www.microsoft.com/download/details.aspx?id=45345) します。 以下の情報を利用して、ダウンロードするパッケージの名前とそれに対応する SHA-256 パッケージ ハッシュを特定してください。

---
**米国:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**ヨーロッパ:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**アジア:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**ラテン アメリカ:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**日本:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**韓国:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**南アフリカ:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**アラブ首長国連邦:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**オーストラリア:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**米国防総省:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**カナダ:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**ドイツ:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**ドイツ パブリック:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**インド:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**フランス:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**イギリス:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**スイス:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


ダウンロードした BYOK ツールセットの整合性を検証するには、Azure PowerShell セッションから、 [Get FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) コマンドレットを使用します。

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

ツールセットの内容は次のとおりです。

* Key Exchange Key (KEK) パッケージ。この名前は「**BYOK-KEK-pkg-** 」から始まります。
* セキュリティ ワールド パッケージ。この名前は「**BYOK-SecurityWorld-pkg-** 」から始まります。
* **verifykeypackage.py** という名前の Python スクリプト。
* 「 **KeyTransferRemote.exe** 」という名前のコマンドライン実行可能ファイルと関連 DLL。
* 「**vcredist_x64.exe**」という名前の Visual C++ 再配布可能パッケージ。

USB ドライブまたはその他のポータブル ストレージにパッケージをコピーします。

## <a name="step-2-prepare-your-disconnected-workstation"></a>手順 2:未接続ワークステーションを準備する

この 2 つ目の手順では、ネットワーク (インターネットまたは内部ネットワーク) に接続されていないワークステーションで次の手順を実行します。

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>手順 2.1:nCipher nShield HSM で未接続ワークステーションを準備する

Windows コンピューターに nCipher サポート ソフトウェアをインストールし、そのコンピューターに nCipher nShield HSM をアタッチします。

nCipher ツールがパスにあることを確認します ( **%nfast_home%\bin**)。 たとえば、次を入力します。

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

詳細については、nShield HSM に付属のユーザー ガイドを参照してください。

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>手順 2.2:未接続ワークステーションに BYOK ツールセットをインストールする

USB ドライブまたはその他のポータブル ストレージから BYOK ツールセット パッケージをコピーし、次の操作します。

1. ダウンロードしたパッケージから任意のフォルダーにファイルを抽出します。
2. そのフォルダーから vcredist_x64.exe を実行します。
3. 指示に従い、Visual Studio 2013 用の Visual C++ ランタイム コンポーネントをインストールします。

## <a name="step-3-generate-your-key"></a>手順 3:キーを生成する

この 3 つ目の手順では、未接続ワークステーションで次の手順を実行します。 この手順を実行するには、HSM は初期化モードである必要があります。 

### <a name="step-31-change-the-hsm-mode-to-i"></a>手順 3.1: HSM モードを "I" に変更する

nCipher nShield Edge を使用している場合、モードを変更するには、次の手順を実行します。1. モード ボタンを使用して、必要なモードを強調表示します。 2. 数秒以内に、[クリア] ボタンを数秒押したままにします。 モードが変更されると、新しいモードの LED の点滅が止まり、点灯したままになります。 ステータス LED は、数秒間、不規則に点滅することがあります。その後、デバイスの準備が完了すると定期的に点滅します。 それ以外の場合、デバイスは現在のモードのままになり、適切なモード LED が点灯します。

### <a name="step-32-create-a-security-world"></a>手順 3.2: セキュリティ ワールドを作成する

コマンド プロンプトを起動し、nCipher new-world プログラムを実行します。

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

このプログラムにより **Security World** ファイルが %NFAST_KMDATA%\local\world で作成されます。これは C:\ProgramData\nCipher\Key Management Data\local フォルダーに対応します。 クォーラムにはさまざまな値を使用できますが、今回の例では、3 枚の空白カードと各カードのピンを入力するように求められます。 いずれかの 2 枚のカードがセキュリティ ワールドに完全アクセスを与えます。 その 2 枚のカードが新しいセキュリティ ワールドの **管理者カード セット** になります。

> [!NOTE]
> HSM で新しい暗号スイート DLf3072s256mRijndael がサポートされない場合は、--cipher-suite= DLf3072s256mRijndael を --cipher-suite=DLf1024s160mRijndael に置き換えることができます
> 
> nCipher ソフトウェア バージョン 12.50 に付属する new-world.exe を使用して作成されたセキュリティ ワールドは、BYOK プロシージャと互換性がありません。 次の 2 つの選択肢があります。
> 1) nCipher ソフトウェア バージョンを 12.40.2 にダウングレードし、新しいセキュリティ ワールドを作成します。
> 2) nCipher サポートに連絡し、12.50 ソフトウェア バージョンの修正プログラムを提供するように依頼します。これにより、この BYOK プロシージャと互換性のある 12.40.2 バージョンの new-world.exe を使用できるようになります。

次に、次を実行します。

* ワールド ファイルをバックアップします。 ワールド ファイル、管理者カード、そのピンを保護します。1 人の人間が複数のカードにアクセスできないようにします。

### <a name="step-33-change-the-hsm-mode-to-o"></a>手順 3.3: HSM モードを "O" に変更する

nCipher nShield Edge を使用している場合、モードを変更するには、次の手順を実行します。1. モード ボタンを使用して、必要なモードを強調表示します。 2. 数秒以内に、[クリア] ボタンを数秒押したままにします。 モードが変更されると、新しいモードの LED の点滅が止まり、点灯したままになります。 ステータス LED は、数秒間、不規則に点滅することがあります。その後、デバイスの準備が完了すると定期的に点滅します。 それ以外の場合、デバイスは現在のモードのままになり、適切なモード LED が点灯します。

### <a name="step-34-validate-the-downloaded-package"></a>手順 3.4: ダウンロードしたパッケージを検証する

この手順は省略可能ですが、次を検証できるので推奨されます。

* ツールセットに含まれる Key Exchange Key が本物の nCipher nShield HSM から生成されていること。
* ツールセットに含まれているセキュリティ ワールドのハッシュが本物の nCipher nShield HSM から生成されていること。
* Key Exchange Key がエクスポート不可であること。

> [!NOTE]
> ダウンロードしたパッケージを検証するには、HSM を接続し、電源を入れる必要があります。また、(たとえば、あなたが先ほど作成した) セキュリティ ワールドが入っている必要があります。

ダウンロードしたパッケージを検証するには:

1. 地域リージョンまたは Azure のインスタンスに応じて、次のいずれかを入力して、verifykeypackage.py スクリプトを実行します。

   * 北米:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * ヨーロッパ:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * アジア:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * ラテン アメリカ:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * 日本:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * 韓国:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * 南アフリカ:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * アラブ首長国連邦:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * オーストラリア:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * 米国防総省:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * カナダの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * ドイツの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * ドイツ パブリックの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * インドの場合:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * フランスの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * イギリスの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * スイスの場合:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > nCipher nShield ソフトウェアには、%NFAST_HOME%\python\bin に Python が含まれています
     >
     >
2. 次の表示を確認します。これは検証の成功を示します: **Result: SUCCESS**

このスクリプトは nShield ルート キーまで署名者のチェーンを検証します。 このルート キーのハッシュがスクリプトに埋め込まれており、その値は **59178a47 de508c3f 291277ee 184f46c4 f1d9c639** になります。 [nCipher Web サイト](https://www.ncipher.com)にアクセスすると、この値を個別に確認することもできます。

これで新しいキーを作成する準備が整いました。

### <a name="step-35-create-a-new-key"></a>手順 3.5: 新しいキーを作成する

nCipher nShield **generatekey** プログラムを利用してキーを生成します。

次のコマンドを実行してキーを生成します。

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

このコマンドを実行するとき、次の指示に従います。

* *protect* パラメーターの値は、コマンドに示したように **module** に設定する必要があります。 module に設定すると、モジュールで保護されたキーが作成されます。 BYOK ツールセットは、OCS で保護されたキーをサポートしていません。
* **ident** と **plainname** の *contosokey* の値を文字列値に置換します。 管理費を最小限に抑え、エラーを犯す可能性を減らすために、両方に同じ値を使用することをお勧めします。 **Ident** 値には数字、ダッシュ、小文字のみを使用できます。
* pubexp はこの例では空白のまま (既定) ですが、特定の値を指定できます。 詳細については、[nCipher のドキュメント](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)を参照してください。

このコマンドにより %NFAST_KMDATA%\local フォルダーにトークン化されたキーのファイルが作成されます。この名前は "**key_simple_** " で始まり、コマンドで指定した **ident** が続きます。 (例: **key_simple_contosokey**)。 このファイルには暗号化されたキーが含まれます。

安全な場所でこのトークン化されたキーのファイルをバックアップします。

> [!IMPORTANT]
> 後で Azure Key Vault にキーを転送すると、Microsoft はこのキーをあなたの元にエクスポートできません。そのため、キーとセキュリティ ワールドを安全にバックアップすることが極めて重要です。 キーのバックアップ方法のガイダンスとベスト プラクティスについては、[nCipher](https://www.ncipher.com/about-us/contact-us) にお問い合わせください。
>


これで Azure Key Vault にキーを転送する準備ができました。

## <a name="step-4-prepare-your-key-for-transfer"></a>手順 4:キーの転送準備をする

この 4 つ目の手順では、未接続ワークステーションで次の手順を実行します。

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>手順 4.1: アクセス権が制限されたキーのコピーを作成する

新しいコマンド プロンプトを開き、現在のディレクトリを、BYOK zip ファイルを解凍した場所に変更します。 キーのアクセス権を制限するには、地域リージョンまたは Azure のインスタンスによって、コマンド プロンプトから次のいずれかを実行します。

* 北米:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* ヨーロッパ:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* アジア:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* ラテン アメリカ:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* 日本:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* 韓国:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* 南アフリカ:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* アラブ首長国連邦:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* オーストラリア:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* 米国防総省:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* カナダの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* ドイツの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* ドイツ パブリックの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* インドの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* フランスの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* イギリスの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* スイスの場合:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

このコマンドを実行するとき、*contosokey* を、「**手順 3.5: 新しいキーを作成する**」(「[キーを生成する](#step-3-generate-your-key)」) でキーの生成に使用した ID で置き換えます。

セキュリティ ワールドの管理者カードを差し込むように求められます。

コマンドが完了すると、**Result: SUCCESS** と表示され、アクセス権が制限されたキーのコピーが "key_xferacId_\<contosokey>" という名前のファイルに表示されます。

nCipher nShield ユーティリティを使用すると、次のコマンドで ACL を確認できます。

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  これらのコマンドを実行するとき、contosokey を、「**手順 3.5: 新しいキーを作成する**」(「[キーを生成する](#step-3-generate-your-key)」) で指定した値に置き換えます。

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>手順 4.2: Microsoft の Key Exchange Key を使用してキーを暗号化する

地域リージョンまたは Azure のインスタンスによって、次のいずれかのコマンドを実行します。

* 北米:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* ヨーロッパ:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* アジア:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* ラテン アメリカ:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* 日本:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* 韓国:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* 南アフリカ:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* アラブ首長国連邦:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* オーストラリア:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* 米国防総省:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* カナダの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* ドイツの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* ドイツ パブリックの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* インドの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* フランスの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* イギリスの場合:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* スイスの場合:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


このコマンドを実行するとき、次の指示に従います。

* *contosokey* を、「**手順 3.5: 新しいキーを作成する**」(「[キーを生成する](#step-3-generate-your-key)」) でキーの生成に使用した ID で置き換えます。
* *SubscriptionID* を Key Vault が含まれる Azure サブスクリプションの ID で置換します。 この値は先に、「**手順 1.2: Azure サブスクリプション ID を取得する**」(「[インターネット接続ワークステーションを準備する](#step-1-prepare-your-internet-connected-workstation)」の手順) で取得しました。
* *ContosoFirstHSMKey* を出力ファイル名に使用するラベルで置換します。

完了すると、**Result: SUCCESS** と表示され、KeyTransferPackage-*ContosoFirstHSMkey*.byok という名前の新しいファイルが現在のフォルダーに表示されます。

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>手順 4.3: キー転送パッケージをインターネット接続ワークステーションにコピーする

USB ドライブまたはその他のポータブル ストレージを使用し、前の手順の出力ファイル (KeyTransferPackage-ContosoFirstHSMkey.byok) をインターネット接続ワークステーションにコピーします。

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>手順 5:キーを Azure Key Vault に転送する

この最後の手順では、インターネット接続ワークステーションで、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレットを使用し、未接続ワークステーションからコピーしたキー転送パッケージを Azure Key Vault HSM にアップロードします。

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

アップロードされると、追加したキーのプロパティが表示されます。

## <a name="next-steps"></a>次のステップ

これでこの HSM 保護キーを Key Vault で使用できます。 詳しくは、この価格と機能の[比較](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。