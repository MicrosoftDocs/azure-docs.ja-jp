---
title: Azure Site Recovery での Azure VM レプリケーションのトラブルシューティング
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするときに発生するエラーに関するトラブルシューティング
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900418"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure 間の VM レプリケーションに関するエラーのトラブルシューティング

この記事では、Azure 仮想マシン (VM) のあるリージョンから別のリージョンへのレプリケーションおよび復旧中に Azure Site Recovery で一般的なエラーをトラブルシューティングする方法について説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](azure-to-azure-support-matrix.md)に関するページをご覧ください。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure リソースのクォータに関する問題 (エラー コード 150097)

ディザスター リカバリー (DR) リージョンとして使用するターゲット リージョンで Azure VM を作成するには、サブスクリプションが有効であることを確認してください。 必要なサイズの VM を作成するには、サブスクリプションに十分なクォータが必要です。 既定では、Site Recovery ではソース VM のサイズと同じターゲット VM サイズが選択されます。 同じサイズを利用できない場合、Site Recovery では利用可能な最も近いサイズが自動的に選択されます。

ソース VM 構成をサポートするサイズが存在しない場合、次のメッセージが表示されます。

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>考えられる原因

- ターゲット リージョンの場所で VM を作成するための、サブスクリプション ID が有効になっていません。
- ターゲット リージョンの場所で特定の VM サイズを作成するための、サブスクリプション ID が有効になっていないか、十分なクォータが確保されていません。
- ターゲット リージョンの場所のサブスクリプション ID について、ソース VM の ネットワーク インターフェイス カード (NIC) の数 (2) と一致する適切なターゲットVM サイズが見つかりません。

### <a name="fix-the-problem"></a>問題の解決

[Azure 課金のサポート](../azure-portal/supportability/resource-manager-core-quotas-request.md)に連絡して、サブスクリプションで、必要なサイズの VM をターゲットの場所に作成できるようにします。 その後、失敗した操作をやり直してください。

ターゲットの場所に容量の制約がある場合は、その場所へのレプリケーションを無効にします。 次に、必要なサイズの VM を作成できるだけのクォータがサブスクリプションに確保されている別の場所へのレプリケーションを有効にします。

## <a name="trusted-root-certificates-error-code-151066"></a>信頼されたルート証明書 (エラー コード 151066)

最新の信頼されたルート証明書の一部が VM にない場合、Site Recovery のレプリケーションを有効にするジョブが失敗することがあります。 これらの証明書がないと、VM からの Site Recovery サービス呼び出しの認証と承認が失敗します。

レプリケーションを有効にするジョブが失敗すると、次のメッセージが表示されます。

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>考えられる原因

承認と認証に必要な信頼されたルート証明書が仮想マシンに存在しません。

### <a name="fix-the-problem"></a>問題の解決

#### <a name="windows"></a>Windows

Windows オペレーティング システムを実行中の VM の場合、最新 Windows 更新プログラムをインストールし、すべての信頼されたルート証明書が VM に存在するようにします。 組織の一般的な Windows 更新管理プロセスまたは証明書更新管理プロセスに従って、VM で最新のルート証明書と更新済み証明書失効リストを取得します。

- 接続されていない環境の場合は、組織の標準の Windows 更新プロセスに従って、証明書を取得してください。
- VM に必要な証明書がない場合は、セキュリティ上の理由から、Site Recovery サービスへの呼び出しが失敗します。

問題が解決されたことを確認するには、VM のブラウザーから `login.microsoftonline.com` にアクセスします。

詳細については、「[信頼されたルートおよび許可されない証明書を構成する](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))」を参照してください。

#### <a name="linux"></a>Linux

お使いの Linux オペレーティング システム バージョンのディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と最新の証明書失効リストを取得します。

SUSE Linux ではシンボリック リンク (symlink) を使用して証明書リストを保持しているため、次の手順に従います。

1. **ルート** ユーザーとしてサインインします。 ハッシュ記号 (`#`) は、既定のコマンド プロンプトです。

1. ディレクトリを変更するには、次のコマンドを実行します。

   `cd /etc/ssl/certs`

1. Symantec ルート CA 証明書が存在するかどうかを確認します。

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Symantec ルート CA 証明書が見つからない場合は、次のコマンドを実行してファイルをダウンロードします。 エラーがないか確認し、ネットワーク障害に対する推奨アクションに従います。

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Baltimore ルート CA 証明書が存在するかどうかを確認します。

   `ls Baltimore_CyberTrust_Root.pem`

   - Baltimore ルート CA 証明書が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. DigiCert_Global_Root_CA 証明書が存在するかどうかを確認します。

   `ls DigiCert_Global_Root_CA.pem`

    - DigiCert_Global_Root_CA が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 新たにダウンロードした証明書のサブジェクト ハッシュを更新するには、再ハッシュ スクリプトを実行します。

   `c_rehash`

1. シンボリック リンクとしてのサブジェクト ハッシュが証明書に対して作成されているかどうかを確認するには、次のコマンドを実行します。

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. ファイル _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ のコピーを _b204d74a.0_ というファイル名で作成します。

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. ファイル _Baltimore_CyberTrust_Root.pem_ のコピーを _653b494a.0_ というファイル名で作成します。

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. ファイル _DigiCert_Global_Root_CA.pem_ のコピーを _3513523f.0_ というファイル名で作成します。

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. これらのファイルが存在するかどうかを確認します。

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>送信 URL または IP 範囲 (エラー コード 151037 または 151072)

Site Recovery レプリケーションを動作させるには、VM から特定の URL への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。 URL を介した送信アクセスについては引き続きサポートされていますが、IP 範囲の許可リストを使用することはサポートされなくなりました。

#### <a name="possible-causes"></a>考えられる原因

- ドメイン ネーム システム (DNS) を解決できないため、Site Recovery エンドポイントとの接続を確立できません。
- この問題が頻繁に発生するのは、仮想マシンをフェールオーバーして再保護を行っているときに、ディザスター リカバリー (DR) リージョンから DNS サーバーに到達できない場合です。

#### <a name="fix-the-problem"></a>問題の解決

カスタム DNS を使っている場合は、ディザスター リカバリー リージョンから DNS サーバーにアクセスできることを確認します。

VM がカスタム DNS 設定を使用するかどうかを確認するには、次を実行します。

1. **[仮想マシン]** を開いて、VM を選択します。
1. VM の **[設定]** に移動し、 **[ネットワーク]** を選択します。
1. **[仮想ネットワーク/サブネット]** で、仮想ネットワークのリソース ページを開くためのリンクを選択します。
1. **[設定]** に移動して、 **[DNS サーバー]** を選択します。

仮想マシンから DNS サーバーにアクセスを試みます。 DNS サーバーにアクセスできない場合は、DNS サーバーをフェールオーバーするか、または DR ネットワークと DNS の間にサイトのラインを作成して、アクセスできるようにします。

> [!NOTE]
> プライベート エンドポイントを使用する場合は、確実に VM によるプライベート DNS レコードの解決が可能であるようにします。

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error。":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2:Site Recovery の構成に失敗しました (151196)

#### <a name="possible-cause"></a>考えられる原因

Microsoft 365 認証と ID IP4 エンドポイントへの接続を確立できません。

#### <a name="fix-the-problem"></a>問題の解決

Azure Site Recovery では、認証のために Microsoft 365 の IP 範囲にアクセスする必要がありました。
Azure ネットワーク セキュリティ グループ (NSG) 規則またはファイアウォール プロキシを使用して VM 上で発信ネットワーク接続を制御している場合、Azure Active Directory (AAD) へのアクセスを許可するには [AAD サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)に基づく NSG 規則を確実に使用してください。 IP アドレスベースの NSG 規則はサポートしなくなりました。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3:Site Recovery の構成に失敗しました (151197)

#### <a name="possible-cause"></a>考えられる原因

Azure Site Recovery サービス エンドポイントへの接続を確立できません。

#### <a name="fix-the-problem"></a>問題の解決

VM での発信ネットワーク接続の制御に Azure ネットワーク セキュリティ グループ (NSG) 規則またはファイアウォール プロキシを使用している場合は、確実にサービス タグを使用してください。 Azure Site Recovery の NSG を介して IP アドレスの許可リストを使用することはサポートされなくなりました。

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>問題 4:ネットワーク トラフィックでオンプレミスのプロキシ サーバーが使用されるとレプリケーションが失敗する (151072)

#### <a name="possible-cause"></a>考えられる原因

カスタム プロキシ設定が無効であり、Mobility Service エージェントが Internet Explorer (IE) からプロキシ設定を自動検出しませんでした。

#### <a name="fix-the-problem"></a>問題の解決

1. Mobility Service エージェントは、Windows では IE から、Linux では `/etc/environment` からプロキシ設定を検出します。
1. プロキシを Mobility Service にのみ設定する場合は、次の場所にある _ProxyInfo.conf_ 内にプロキシの詳細を指定できます。

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ 内のプロキシ設定は、次の _INI_ 形式になっている必要があります。

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Mobility Service エージェントは、**認証されていないプロキシ** のみをサポートします。

### <a name="more-information"></a>詳細情報

[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または[必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)を指定するには、「[Azure から Azure へのレプリケーションのネットワークについて](azure-to-azure-about-networking.md)」のガイダンスに従ってください。

## <a name="disk-not-found-in-vm-error-code-150039"></a>VM でディスクが見つからない (エラー コード 150039)

VM に接続された新しいディスクを初期化する必要があります。 ディスクが見つからない場合は、次のメッセージが表示されます。

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>考えられる原因

- 新しいデータ ディスクが VM に接続されているが、初期化されませんでした。
- VM 内部のデータ ディスクは、そのディスクが VM に接続されている論理ユニット番号 (LUN) 値を正しく報告していません。

### <a name="fix-the-problem"></a>問題の解決

データ ディスクが初期化されていることを確認し、操作を再試行します。

- **Windows**:[新しいディスクを接続し、初期化する](../virtual-machines/windows/attach-managed-disk-portal.md)。
- **Linux**:[Linux で新しいデータ ディスクを初期化する](../virtual-machines/linux/add-disk.md)。

問題が解決しない場合は、サポートにお問い合わせください。

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>複数のディスクで保護が使用可能である (エラー コード 153039)

### <a name="possible-causes"></a>考えられる原因

- 保護の後に、1 つまたは複数のディスクが最近仮想マシンに追加された。
- 仮想マシンの保護の後に、1 つまたは複数のディスクが初期化された。

### <a name="fix-the-problem"></a>問題の解決

VM のレプリケーション状態をもう一度正常にするには、ディスクを保護するか、警告を無視します。

#### <a name="to-protect-the-disks"></a>ディスクを保護するには、次の手順に従います。

1. **[レプリケートされたアイテム]**  >  _[VM 名]_  >  **[ディスク]** にアクセスします。
1. 保護されていないディスクを選択し、 **[レプリケーションを有効にする]** を選択します。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="VM ディスクのレプリケーションを有効にする。":::

#### <a name="to-dismiss-the-warning"></a>警告を無視するには、次の手順に従います。

1. **[レプリケートされたアイテム]**  >  _[VM 名]_ に移動します。
1. **[概要]** セクションで警告を選択し、 **[OK]** を選択します。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="新規ディスクの警告を無視する。":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>VM がコンテナーから削除され、完了時に情報が表示された (エラー コード 150225)

Site Recovery によって仮想マシンが保護されると、ソース仮想マシンにリンクが作成されます。 保護を削除するか、レプリケーションを無効にすると、Site Recovery によるクリーンアップ ジョブの一環として、これらのリンクが削除されます。 仮想マシンにリソース ロックが設定されている場合、クリーンアップ ジョブが完了するときに情報が表示されます。 その情報には、仮想マシンが Recovery Services コンテナーから削除されたが、ソース マシンから古いリンクの一部をクリーンアップできなかったことが表示されます。

この仮想マシンを保護する予定がない場合、この警告は無視してもかまいません。 ただし、後でこの仮想マシンを保護する必要がある場合は、このセクションの手順に従ってリンクをクリーンアップしてください。

> [!WARNING]
> クリーンアップしないと、次のような事態が発生します。
>
> - Recovery Services コンテナーを使用してレプリケーションを有効にしたときに、仮想マシンが表示されません。
> - **[仮想マシン]**  >  **[設定]**  >  **[ディザスター リカバリー]** を使用して VM を保護しようとすると、"**VM の既存のリソース リンクが古いため、レプリケーションを有効にすることはできません**" というエラー メッセージが表示されて操作が失敗します。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> 以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループから、ロックを削除します。 たとえば次の図で、`MoveDemo` という名前の VM のリソース ロックを削除する必要があります。

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM からロックを削除する。":::

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ スクリプトを実行します。 パラメーターとして、**サブスクリプション ID**、**VM リソース グループ**、および **VM 名** を指定します。
1. Azure 資格情報の入力を求められたら、それを指定します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>古いリソースを含む VM でレプリケーションが有効にならない (エラー コード 150226)

### <a name="possible-causes"></a>考えられる原因

仮想マシンに、以前の Site Recovery 保護の古い構成が残されている。

Site Recovery を使用して Azure VM のレプリケーションを有効にした後、次のことを行った場合、Azure VM で古い構成が残る可能性があります。

- レプリケーションを無効にしたが、ソース VM にリソース ロックがある。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> 以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループから、ロックを削除します。 たとえば次の図で、`MoveDemo` という名前の VM のリソース ロックを削除する必要があります。

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM からロックを削除する。":::

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ スクリプトを実行します。 パラメーターとして、**サブスクリプション ID**、**VM リソース グループ**、および **VM 名** を指定します。
1. Azure 資格情報の入力を求められたら、それを指定します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>レプリケーションの有効化ジョブで VM またはリソース グループを選択できない

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>問題 1:リソース グループとソース VM が別の場所にある

Site Recovery では現在、ソース リージョンのリソース グループと仮想マシンが同じ場所にあることが必須となっています。 そうでない場合、保護を適用しようとするときに、仮想マシンもリソース グループも見つけることができません。

回避策として、Recovery Services コンテナーではなく、VM からレプリケーションを有効にします。 **[ソース VM]**  >  **[プロパティ]**  >  **[ディザスター リカバリー]** に移動し、レプリケーションを有効にします。

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>問題 2:このリソース グループが、選択されたサブスクリプションの一部でない

リソース グループが選択されたサブスクリプションの一部ではない場合、保護されたときにリソース グループを見つけられない可能性があります。 リソース グループが、使用中のサブスクリプションに属していることを確認します。

### <a name="issue-3-stale-configuration"></a>問題 3:構成が古い

Azure VM 上に古い Site Recovery 構成が存在する場合、レプリケーションを有効にする VM が表示されない可能性があります。 Site Recovery を使用して Azure VM のレプリケーションを有効にした後、次のことを行った場合、この状態が発生する可能性があります。

- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。
- レプリケーションを無効にしたが、ソース VM にリソース ロックがある。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> このセクションで説明するスクリプトを使用する前に、`AzureRM.Resources` モジュールを必ず更新するようにしてください。 以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループにロックが設定されている場合、ロックを削除します。 たとえば次の図で、`MoveDemo` という名前の VM のリソース ロックを削除する必要があります。

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM からロックを削除する。":::

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. _Cleanup-stale-asr-config-Azure-VM.ps1_ スクリプトを実行します。 パラメーターとして、**サブスクリプション ID**、**VM リソース グループ**、および **VM 名** を指定します。
1. Azure 資格情報の入力を求められたら、それを指定します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="unable-to-select-a-vm-for-protection"></a>保護のために VM を選択できない

### <a name="possible-cause"></a>考えられる原因

仮想マシンの拡張機能のインストールが失敗したかまたは応答しない状態にある

### <a name="fix-the-problem"></a>問題の解決

**[仮想マシン]**  >  **[設定]**  >  **[拡張機能]** の順に進み、拡張機能のいずれかが失敗した状態になっていないか確認します。 失敗したすべての拡張機能をアンインストールしてから、仮想マシンの保護を再試行してください。

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM のプロビジョニング状態が有効でない (エラー コード 150019)

VM でレプリケーションを有効にするには、プロビジョニングの状態が **[成功]** になっている必要があります。 プロビジョニングの状態を確認するには、次の手順に従います。

1. Azure portal の **[すべてのサービス]** から **[リソース エクスプローラー]** を選択します。
1. **[サブスクリプション]** 一覧を展開して、自分のサブスクリプションを選択します。
1. **[ResourceGroups]** 一覧を展開して、VM のリソース グループを選択します。
1. **[リソース]** 一覧を展開して、お使いの VM を選択します。
1. 右側にあるインスタンス ビューで、 **[provisioningState]** フィールドを確認します。

### <a name="fix-the-problem"></a>問題の解決

- **[provisioningState]** が **[失敗]** になっている場合は、サポートに詳細を問い合わせてトラブルシューティングします。
- **[provisioningState]** が **[更新中]** になっている場合は、別の拡張機能がデプロイされている可能性があります。 VM 上に何らかの進行中の操作があるかを確認し、それらが完了するまで待機してから、レプリケーションを有効にする、失敗した Site Recovery ジョブを再試行します。

## <a name="unable-to-select-target-vm"></a>ターゲット VM を選択できない

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>問題 1:既にターゲット ネットワークにマップされているネットワークに VM が接続されている

ディザスター リカバリー構成中に、ソース VM が仮想ネットワークの一部であり、かつ同じ仮想ネットワークの別の VM が既にターゲット リソース グループ内のネットワークにマップされている場合は、既定で [ネットワークの選択] ドロップダウン リスト ボックスを使用できません (淡色表示されます)。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="ネットワーク選択リストを使用できない。":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>問題 2:以前に VM を保護した後、レプリケーションを無効にした

VM のレプリケーションを無効にしても、ネットワーク マッピングは削除されません。 VM が保護されていた Recovery Service コンテナーからマッピングを削除する必要があります。 **[Recovery Services コンテナー]** を選択し、 **[管理]**  >  **[Site Recovery インフラストラクチャ]**  >  **[For Azure virtual machines] (Azure 仮想マシンの場合)**  >  **[ネットワーク マッピング]** に移動します。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="ネットワーク マッピングの削除。":::

ディザスター リカバリーのセットアップ中に構成されたターゲット ネットワークが、初期セットアップ後で、かつ VM が保護された後に変更される場合があります。 **ネットワーク マッピングを変更する** には、ネットワーク名を選択します。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="ネットワーク マッピングの変更。":::


## <a name="com-or-vss-error-code-151025"></a>COM+ または VSS (エラー コード 151025)

COM+ またはボリューム シャドウ コピー サービス (VSS) エラーが発生すると、次のメッセージが表示されます。

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>考えられる原因

- COM+ システム アプリケーション サービスが無効になっています。
- ボリューム シャドウ コピー サービスが無効になっています。

### <a name="fix-the-problem"></a>問題の解決

COM+ システム アプリケーションとボリューム シャドウ コピー サービスを、自動または手動スタートアップ モードに設定します。

1. Windows の "サービス" コンソールを開きます。
1. COM+ システム アプリケーションとボリューム シャドウ コピー サービスの **[スタートアップの種類]** が **[無効]** に設定されていないことを確認します。

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="COM+ システム アプリケーションとボリューム シャドウ コピー サービスのスタートアップの種類を確認する。":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>サポートされていないマネージド ディスクのサイズ (エラー コード 150172)

このエラーが発生すると、次のメッセージが表示されます。

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>考えられる原因

ディスクが、サポートされているサイズの 1024 MB を下回っています。

### <a name="fix-the-problem"></a>問題の解決

ディスク サイズがサポートされているサイズの範囲内にあることを確認し、操作を再試行してください。

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>GRUB でデバイス名を使用するときに保護が有効にならない (エラー コード 151126)

### <a name="possible-causes"></a>考えられる原因

Linux Grand Unified Bootloader (GRUB) 構成ファイル ( _/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_、または _/etc/default/grub_) に、`root` と `resume` パラメーターに対してユニバーサル一意識別子 (UUID) の値ではなく実際のデバイス名が指定されている可能性があります。 デバイス名は変更できるため、Site Recovery には UUID が必要です。 再起動時のフェールオーバー時に VM が同じ名前で表示されず、問題が発生する可能性があります。

次の例は、必要な UUID ではなくデバイス名が表示される、GRUB ファイルの行です。

- ファイル: _/boot/grub2/grub.cfg_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- ファイル: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>問題の解決

各デバイス名を対応する UUID に置き換えます。

1. コマンド `blkid <device name>` を実行して、デバイスの UUID を検出します。 次に例を示します。

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. `root=UUID=<UUID>` および `resume=UUID=<UUID>` の形式で、デバイス名を UUID に置き換えます。 たとえば、 _/boot/grub/menu.lst_ の行は置換後に次の行のようになります。

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 保護を再試行してください。

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>GRUB デバイスが存在しないため保護に失敗した (エラー コード 151124)

### <a name="possible-cause"></a>考えられる原因

GRUB 構成ファイル ( _/boot/grub/menu.lst_、 _/boot/grub/grub.cfg_、 _/boot/grub2/grub.cfg_、または _/etc/default/grub_) に、`rd.lvm.lv` または `rd_LVM_LV` というパラメーターが含まれている可能性があります。 これらのパラメーターは、起動時に検出される論理ボリューム マネージャ (LVM) デバイスを識別します。 これらの LVM デバイスが存在しない場合、保護されたシステム自体は起動せず、起動プロセスでスタックします。 フェールオーバー VM にも同じ問題が確認されます。 次に例をいくつか示します。

- ファイル: _/boot/grub2/grub.cfg_ (RHEL7 の場合):

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- ファイル: _/etc/default/grub_ (RHEL7 の場合):

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- ファイル: _/boot/grub/menu.lst_ (RHEL6 の場合):

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

それぞれの例で、GRUB はボリューム グループ `rootvg` から `root` および `swap` という名前の 2 つの LVM デバイスを検出する必要があります。

### <a name="fix-the-problem"></a>問題の解決

LVM デバイスが存在しない場合は、このデバイスを作成するか、GRUB 構成ファイルから対応するパラメーターを削除します。 次に、再び保護を有効にしてください。

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Mobility Service の更新が完了し、警告が表示された (エラー コード 151083)

Site Recovery Mobility Service には多数のコンポーネントがありますが、そのうちの 1 つに、フィルター ドライバーと呼ばれるものがあります。 フィルター ドライバーは、システムの再起動時にのみ、システム メモリ内に読み込まれます。 Mobility Service の更新にフィルター ドライバーの変更が含まれている場合、マシンは更新されますが、一部の修正プログラムで再起動が必要であることを示す警告が表示されます。 この警告が表示される理由は、フィルター ドライバーの修正は、新しいフィルター ドライバーが読み込まれたときにのみ有効になるためであり、これは再起動中にのみ発生します。

> [!NOTE]
> これはただの警告です。 既存のレプリケーションは、新しいエージェントが更新された後も引き続き機能します。 再起動は、新しいフィルター ドライバーの利点を活用したい任意のタイミングで実行できますが、再起動しない場合は古いフィルター ドライバーが動作し続けます。
>
> フィルター ドライバーを除く、Mobility Service の更新におけるその他の機能強化や修正の利点は、再起動を必要とせずに有効になります。

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>レプリカ マネージド ディスクが存在する場合は保護が有効にならない

このエラーは、レプリカ マネージド ディスクが (予測されるタグなしで) ターゲット リソース グループに既に存在する場合に発生します。

### <a name="possible-cause"></a>考えられる原因

この問題は、仮想マシンが以前に保護されていて、レプリケーションを無効にしたときにレプリカ ディスクが削除されていない場合に発生する可能性があります。

### <a name="fix-the-problem"></a>問題の解決

エラー メッセージに示されているレプリカ ディスクを削除し、失敗した保護ジョブを再度開始します。

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>インストーラーがルート ディスクを検出できないため、保護の有効化に失敗した (エラー コード 151137)

このエラーは、Azure Disk Encryption (ADE) を使用して OS ディスクが暗号化されている Linux マシンで発生します。 この問題は、エージェント バージョン 9.35 でのみ有効です。

### <a name="possible-causes"></a>考えられる原因

インストーラーは、ルート ファイル システムをホストしているルート ディスクを検出できません。

### <a name="fix-the-problem"></a>問題の解決

この問題を解決するには、次の手順に従ってください。

1. 次のコマンドを使用して、RHEL および CentOS マシンのディレクトリ _/var/lib/waagent_ の下にあるエージェント ファイルを検索します。 <br>

    `# find /var/lib/ -name Micro\*.gz`

   予想される出力:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. 新しいディレクトリを作成し、ディレクトリをこの新しいディレクトリに変更します。
3. 次のコマンドを使用して、最初の手順で見つかったエージェント ファイルを抽出します。

    `tar -xf <Tar Ball File>`

4. _prereq_check_installer.json_ ファイルを開き、次の行を削除します。 その後、ファイルを保存します。

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. 次のコマンドを使用して、インストーラーを起動します。 <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. インストーラーが正常に完了したら、レプリケーションの有効化ジョブを再試行します。

## <a name="next-steps"></a>次のステップ

[Azure VM を別の Azure リージョンにレプリケートする](azure-to-azure-how-to-enable-replication.md)