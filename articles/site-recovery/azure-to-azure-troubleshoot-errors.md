---
title: Azure 間レプリケーションのエラーに関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするときに発生するエラーに関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: baf7a21d04e8f9bcf86c67abde302a558dfba01c
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910394"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure 間の VM レプリケーションに関するエラーのトラブルシューティング

この記事では、リージョン間で Azure 仮想マシン (VM) をレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的なエラーをトラブルシューティングする方法について説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure リソースのクォータに関する問題 (エラー コード 150097)

ディザスター リカバリー リージョンとして使用するターゲット リージョンで Azure VM を作成するには、サブスクリプションが有効であることを確認してください。 また、必要なサイズの VM を作成するための十分なクォータがサブスクリプションにあることを確認してください。 既定では、Site Recovery ではソース VM のサイズと同じターゲット VM サイズが選択されます。 同じサイズを利用できない場合、Site Recovery では利用可能な最も近いサイズが自動的に選択されます。

ソース VM 構成をサポートするサイズが存在しない場合、次のメッセージが表示されます。

> "Replication couldn't be enabled for the virtual machine *VmName*." (仮想マシン *VmName* でレプリケーションを有効にできませんでした。)

### <a name="possible-causes"></a>考えられる原因

- ターゲット リージョンの場所で VM を作成するための、サブスクリプション ID が有効になっていません。
- ターゲット リージョンの場所で特定の VM サイズを作成するための、サブスクリプション ID が有効になっていないか、十分なクォータが確保されていません。
- ターゲット リージョンの場所のサブスクリプション ID について、ソース VM の ネットワーク インターフェイス カード (NIC) の数 (2) と一致する適切なターゲットVM サイズが見つかりません。

### <a name="fix-the-problem"></a>問題の解決

[Azure 課金のサポート](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)に連絡して、サブスクリプションで、必要なサイズの VM をターゲットの場所に作成できるようにします。 その後、失敗した操作をやり直してください。

ターゲットの場所に容量の制約がある場合は、その場所へのレプリケーションを無効にします。 次に、必要なサイズの VM を作成できるだけのクォータがサブスクリプションに確保されている別の場所へのレプリケーションを有効にします。

## <a name="trusted-root-certificates-error-code-151066"></a>信頼されたルート証明書 (エラー コード 151066)

最新の信頼されたルート証明書の一部が VM にない場合、"レプリケーションを有効にする" Site Recovery ジョブが失敗することがあります。 これらの証明書がないと、VM からの Site Recovery サービス呼び出しの認証と承認が失敗します。 

"レプリケーションを有効にする" ジョブが失敗すると、次のメッセージが表示されます。

> "Site Recovery の構成に失敗しました"

### <a name="possible-cause"></a>考えられる原因

承認と認証に必要な信頼されたルート証明書が仮想マシンに存在しません。

### <a name="fix-the-problem"></a>問題の解決

#### <a name="windows"></a>Windows

Windows オペレーティング システムを実行中の VM の場合、すべての最新 Windows 更新プログラムを VM にインストールし、すべての信頼されたルート証明書をマシンに用意します。 組織の一般的な Windows 更新管理プロセスまたは証明書更新管理プロセスに従って、VM で最新ルート証明書と更新済み証明書失効リストを取得します。

接続されていない環境の場合は、組織の標準の Windows 更新プロセスに従って、証明書を取得してください。 VM に必要な証明書がない場合は、セキュリティ上の理由から、Site Recovery サービスへの呼び出しが失敗します。

問題が解決されたことを確認するには、VM のブラウザーから login.microsoftonline.com にアクセスします。

詳細については、「[信頼されたルートおよび許可されない証明書を構成する](https://technet.microsoft.com/library/dn265983.aspx)」を参照してください。

#### <a name="linux"></a>Linux

お使いの Linux オペレーティング システム バージョンのディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と最新の証明書失効リストを取得します。

SuSE Linux ではシンボリック リンク (*symlink*) を使用して証明書リストを保持しているため、次の手順に従います。

1. ルート ユーザーとしてサインインします。

1. 次のコマンドを実行してディレクトリを変更します。

    **# cd /etc/ssl/certs**

1. Symantec ルート CA 証明書が存在するかどうかを確認します。

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Symantec ルート CA 証明書が見つからない場合は、次のコマンドを実行してファイルをダウンロードします。 エラーがないか確認し、ネットワーク障害に対する推奨アクションに従います。

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Baltimore ルート CA 証明書が存在するかどうかを確認します。

    **# ls Baltimore_CyberTrust_Root.pem**

1. Baltimore ルート CA 証明書が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. DigiCert_Global_Root_CA 証明書が存在するかどうかを確認します。

    **# ls DigiCert_Global_Root_CA.pem**

1. DigiCert_Global_Root_CA が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. 再ハッシュ スクリプトを実行して、新たにダウンロードした証明書のサブジェクト ハッシュを更新します。

    **# c_rehash**

1. 次のコマンドを実行して、サブジェクト ハッシュがシンボリック リンクとして証明書に対して作成されているかどうかを確認します。

    - コマンド:

        **# ls -l | grep Baltimore**

    - 出力:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - コマンド:

        **# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 出力:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - コマンド:

        **# ls -l | grep DigiCert_Global_Root**

    - 出力:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. ファイル VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem のコピーを b204d74a.0 というファイル名で作成します。

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. ファイル Baltimore_CyberTrust_Root.pem のコピーを 653b494a.0 というファイル名で作成します。

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. ファイル DigiCert_Global_Root_CA.pem のコピーを 3513523f.0 というファイル名で作成します。

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. これらのファイルが存在するかどうかを確認します。

    - コマンド:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Output

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL または IP 範囲の送信接続 (エラー コード 151037 または 151072)

Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1: Azure 仮想マシンを Site Recovery に登録できませんでした (エラー コード 151195)

#### <a name="possible-cause"></a>考えられる原因 

DNS を解決できないため、Site Recovery エンドポイントへの接続を確立できません。

この問題が頻繁に発生するのは、仮想マシンをフェールオーバーして再保護を行っているときに、ディザスター リカバリー (DR) リージョンから DNS サーバーに到達できない場合です。

#### <a name="fix-the-problem"></a>問題の解決

カスタム DNS を使っている場合は、ディザスター リカバリー リージョンから DNS サーバーにアクセスできることを確認します。 カスタム DNS があるかどうかを確認するには、VM で、*ディザスター リカバリー ネットワーク* > **DNS サーバー**にアクセスします。

![カスタム DNS サーバーの一覧](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

仮想マシンから DNS サーバーにアクセスしてみます。 サーバーにアクセスできない場合は、DNS サーバーをフェールオーバーするか、または DR ネットワークと DNS の間にサイトのラインを作成して、アクセスできるようにします。

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>問題 2:Site Recovery の構成に失敗しました (エラー コード 151196)

#### <a name="possible-cause"></a>考えられる原因

Office 365 認証と ID IP4 エンドポイントへの接続を確立できません。

#### <a name="fix-the-problem"></a>問題の解決

Site Recovery では、認証のために Office 365 の IP 範囲にアクセスする必要があります。
VM での発信ネットワーク接続の制御に Azure NSG のルールまたはファイアウォール プロキシを使っている場合は、Office 365 の IP 範囲への通信を許可してください。 Azure Active Directory (Azure AD) に対応するすべての IP アドレスへのアクセスを許可するには、[Azure AD サービス タグ](../virtual-network/security-overview.md#service-tags)に基づく NSG 規則を作成します。 今後、新しいアドレスが Azure AD に追加された場合は、新しい NSG 規則を作成する必要があります。

> [!NOTE]
> VM が *Standard* 内部ロード バランサーの背後にある場合、ロード バランサーは既定では Office 365 IP 範囲 (つまり login.microsoftonline.com) にアクセスできません。 [負荷分散および送信規則の構成](https://aka.ms/lboutboundrulescli)に関する記事の説明に従って、内部ロード バランサーの種類を *Basic* に変更するか、発信アクセスを作成します。

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>問題 3:Site Recovery の構成に失敗しました (エラー コード 151197)

#### <a name="possible-cause"></a>考えられる原因

Site Recovery サービスのエンドポイントに対する接続を確立できません。

#### <a name="fix-the-problem"></a>問題の解決

Site Recovery では、リージョンに基づいて [Site Recovery の IP 範囲](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)にアクセスする必要があります。 必要な IP 範囲に仮想マシンからアクセスできることを確認します。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>問題 4:ネットワーク トラフィックがオンプレミス プロキシ サーバーを経由するときに Azure から Azure へのレプリケーションが失敗しました (エラー コード 151072)

#### <a name="possible-cause"></a>考えられる原因

カスタム プロキシ設定が無効であり、Site Recovery Mobility Service エージェントが Internet Explorer からプロキシ設定を自動検出しませんでした

#### <a name="fix-the-problem"></a>問題の解決

モビリティ サービス エージェントは、Windows では Internet Explorer から、Linux では /etc/environment からプロキシ設定を検出します。

プロキシをモビリティ サービスにのみ設定する場合は、次の場所にある ProxyInfo.conf ファイル内にプロキシの詳細を指定できます。

- **Linux**: /usr/local/InMage/config/
- **Windows**:C:\ProgramData\Microsoft Azure Site Recovery\Config

ProxyInfo.conf 内では、プロキシ設定を次の初期化ファイル形式で指定します。

> [*proxy*]

> Address= *http://1.2.3.4*

> Port=*567*


> [!NOTE]
> Site Recovery Mobility Service エージェントは、"*認証されていないプロキシ*" のみをサポートします。

### <a name="more-information"></a>詳細情報

[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または[必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)を指定するには、「[Azure から Azure へのレプリケーションのネットワークについて](site-recovery-azure-to-azure-networking-guidance.md)」のガイダンスに従ってください。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>ディスクがマシンに見つかりません (エラー コード 150039)

VM に接続された新しいディスクを初期化する必要があります。 ディスクが見つからない場合は、次のメッセージが表示されます。

> 論理ユニット番号 *LUN* が *LUNValue* の Azure データ ディスク *DiskName* (*DiskURI*) が、VM 内部から報告されている同じ LUN 値のディスクにマップされませんでした。

### <a name="possible-causes"></a>考えられる原因

- 新しいデータ ディスクが VM に接続されているが、初期化されませんでした。
- VM 内部のデータ ディスクは、そのディスクが VM に接続されている論理ユニット番号 (LUN) 値を正しく報告していません。

### <a name="fix-the-problem"></a>問題の解決

データ ディスクが初期化されていることを確認し、操作を再試行します。

- **Windows**:[新しいディスクを接続し、初期化する](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。

- **Linux**:[Linux で新しいデータ ディスクを初期化する](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

問題が解決しない場合は、サポートにお問い合わせください。

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>1 つ以上のディスクで保護が使用可能です (エラー コード 153039)

### <a name="possible-causes"></a>考えられる原因

- 保護の後に、1 つまたは複数のディスクが最近仮想マシンに追加された。
- 仮想マシンの保護の後に、1 つまたは複数のディスクが初期化された。

### <a name="fix-the-problem"></a>問題の解決

VM のレプリケーション状態をもう一度正常にするには、ディスクを保護するか、警告を無視します。

#### <a name="to-protect-the-disks"></a>ディスクを保護するには、次の手順に従います。

1. **[レプリケートされたアイテム]**  >  *[VM 名]*  >  **[ディスク]** にアクセスします。
1. 保護されていないディスクを選択し、 **[レプリケーションを有効にする]** を選択します。

    ![VM ディスクのレプリケーションを有効にする](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>警告を無視するには、次の手順に従います。

1. **[レプリケートされたアイテム]**  >  *[VM 名]* に移動します。
1. **[概要]** セクションで警告を選択し、 **[OK]** を選択します。

    ![新規ディスクの警告を無視する](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>コンテナーからの仮想マシンの削除が完了しましたが、情報が表示されます (エラー コード 150225)

仮想マシンを保護すると、Site Recovery によって、ソース仮想マシンにいくつかのリンクが作成されます。 保護を削除するか、レプリケーションを無効にすると、Site Recovery によるクリーンアップ ジョブの一環として、これらのリンクが削除されます。 仮想マシンにリソース ロックが設定されている場合、クリーンアップ ジョブが完了するときに情報が表示されます。 その情報には、仮想マシンが Recovery Services コンテナーから削除されたが、ソース マシンから古いリンクの一部をクリーンアップできなかったことが表示されます。

この仮想マシンを保護する予定がない場合、この警告は無視してもかまいません。 ただし、後でこの仮想マシンを保護する必要がある場合は、「問題の解決」の手順に従ってリンクをクリーンアップしてください。

> [!WARNING]
> クリーンアップしないと、次のような事態が発生します。
>
> - Recovery Services コンテナーを使用してレプリケーションを有効にしたときに、仮想マシンが表示されません。
> - **[仮想マシン]**  >  **[設定]**  >  **[ディザスター リカバリー]** を使用して VM を保護しようとすると、"VM の既存のリソース リンクが古いため、レプリケーションを有効にすることはできません" というエラー メッセージが表示されて操作が失敗します。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> 以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループから、ロックを削除します。 たとえば次の図で、"MoveDemo" という名前の VM のリソース ロックを削除する必要があります。

    ![VM からロックを削除する](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. Cleanup-stale-asr-config-Azure-VM.ps1 というスクリプトを実行します。 パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
1. Azure 資格情報を求められたら、入力します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>VM に古いリソース リンクがあるためレプリケーションを有効にできません (エラー コード 150226)

### <a name="possible-cause"></a>考えられる原因

仮想マシンに、以前の Site Recovery 保護の古い構成が残されている。

Site Recovery を使用して Azure VM のレプリケーションを有効にした後、次のことを行った場合、Azure VM で古い構成が残る可能性があります。

- レプリケーションを無効にしたが、ソース VM にリソース ロックがある。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> 以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループから、ロックを削除します。 たとえば次の図で、"MoveDemo" という名前の VM のリソース ロックを削除する必要があります。

    ![VM からロックを削除する](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. Cleanup-stale-asr-config-Azure-VM.ps1 というスクリプトを実行します。 パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
1. Azure 資格情報を求められたら、入力します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>"レプリケーションを有効にする" ジョブで、選択する Azure VM またはリソース グループが表示されない

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>原因 1:リソース グループとソース仮想マシンが別々の場所にある

Site Recovery では現在、ソース リージョンのリソース グループと仮想マシンが同じ場所にあることが必須となっています。 そうでない場合、保護を適用しようとするときに、仮想マシンもリソース グループも見つけることができません。

回避策として、Recovery Services コンテナーではなく、VM からレプリケーションを有効にします。 **[ソース VM]**  >  **[プロパティ]**  >  **[ディザスター リカバリー]** に移動し、レプリケーションを有効にします。

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>原因 2:このリソース グループが、選択されたサブスクリプションの一部でない

リソース グループが選択されたサブスクリプションの一部ではない場合、保護されたときにリソース グループを見つけられない可能性があります。 リソース グループが、使用中のサブスクリプションに属していることを確認します。

### <a name="cause-3-stale-configuration"></a>原因 3:構成が古い

Azure VM 上に古い Site Recovery 構成が残されている場合、レプリケーションを有効にする VM が表示されない可能性があります。 Site Recovery を使用して Azure VM のレプリケーションを有効にした後、次のことを行った場合、この状態が発生する可能性があります。

- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。
- レプリケーションを無効にしたが、ソース VM にリソース ロックがある。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
> このセクションで説明するスクリプトを使用する前に、"AzureRM.Resources" モジュールを必ず更新するようにしてください。  以下の手順の実行中は、Site Recovery でソース仮想マシンを削除することも、何らかの方法で影響を与えることもできません。

1. VM または VM リソース グループにロックが設定されている場合、ロックを削除します。 たとえば次の図で、"MoveDemo" という名前の VM のリソース ロックを削除する必要があります。

    ![VM からロックを削除する](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. [古い Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
1. Cleanup-stale-asr-config-Azure-VM.ps1 というスクリプトを実行します。 パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
1. Azure 資格情報を求められたら、入力します。 次に、スクリプトがエラーを出さずに実行されることを確認します。

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>保護対象の仮想マシンを選択できない

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>原因 1:仮想マシンの拡張機能のインストールが失敗したかまたは応答しない状態にある

**[仮想マシン]**  >  **[設定]**  >  **[拡張機能]** の順に進み、拡張機能のいずれかが失敗した状態になっていないか確認します。 失敗したすべての拡張機能をアンインストールしてから、仮想マシンの保護を再試行してください。

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>原因 2:VM のプロビジョニングの状態が正しくない

この記事で後述する[「VM のプロビジョニングの状態が正しくありません](#the-vms-provisioning-state-is-not-valid-error-code-150019)」のトラブルシューティングの手順を参照してください。

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM のプロビジョニングの状態が正しくありません (エラー コード 150019)

VM でレプリケーションを有効にするには、プロビジョニングの状態が **[成功]** になっている必要があります。 プロビジョニングの状態を確認するには、次の手順に従います。

1. Azure portal の **[すべてのサービス]** から **[リソース エクスプローラー]** を選択します。
1. **[サブスクリプション]** 一覧を展開して、自分のサブスクリプションを選択します。
1. **[ResourceGroups]** 一覧を展開して、VM のリソース グループを選択します。
1. **[リソース]** 一覧を展開して、お使いの VM を選択します。
1. 右側にあるインスタンス ビューで、 **[provisioningState]** フィールドを確認します。

### <a name="fix-the-problem"></a>問題の解決

- **[provisioningState]** が **[失敗]** になっている場合は、サポートに詳細を問い合わせてトラブルシューティングします。
- **[provisioningState]** が **[更新中]** になっている場合は、別の拡張機能がデプロイされている可能性があります。 VM 上に何らかの進行中の操作があるかを確認し、それらの操作が完了するまで待機してから、失敗した Site Recovery の "レプリケーションを有効にする" ジョブを再試行します。

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>ターゲットの VM を選択できない (ネットワークの選択タブが選択できない)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>原因 1:ターゲット ネットワークに既にマップされているネットワークに VM が接続されている。

ソース VM が仮想ネットワークの一部であるときに、同じ仮想ネットワークの別の VM がターゲット リソース グループ内のネットワークに既にマップされている場合、ネットワークの選択ドロップダウン リスト ボックスは既定で選択できなくなります (淡色表示されます)。

![ネットワーク選択リストを使用できない](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>原因 2:Site Recovery を使用して VM を保護し、レプリケーションを無効にしたことがある。

VM のレプリケーションを無効にしても、ネットワーク マッピングは削除されません。 VM が保護されていた Recovery Service コンテナーからマッピングを削除する必要があります。 *[Recovery Services コンテナー]*  >  **[Site Recovery インフラストラクチャ]**  >  **[ネットワーク マッピング]** の順に移動します。

![ネットワーク マッピングの削除](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

ディザスター リカバリーのセットアップ中に構成されたターゲット ネットワークが、初期セットアップ後に VM が保護された後で変更された可能性があります。

![ネットワーク マッピングの変更](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

ネットワーク マッピングを変更すると、その同じネットワーク マッピングを使用するすべての保護された VM に影響することに注意してください。

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ またはボリューム シャドウ コピー サービス エラー (エラー コード 151025)

このエラーが発生すると、次のメッセージが表示されます。

> "Site Recovery 拡張機能をインストールできませんでした"

### <a name="possible-causes"></a>考えられる原因

- COM+ システム アプリケーション サービスが無効になっています。
- ボリューム シャドウ コピー サービスが無効になっています。

### <a name="fix-the-problem"></a>問題の解決

COM+ システム アプリケーション サービスとボリューム シャドウ コピー サービスを、自動または手動スタートアップ モードに設定します。

1. Windows の "サービス" コンソールを開きます。
1. COM+ システム アプリケーションとボリューム シャドウ コピーの **[スタートアップの種類]** が **[無効]** に設定されていないことを確認します。

    ![COM+ システム アプリケーションとボリューム シャドウ コピー サービスのスタートアップの種類を確認する](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>サポートされていないマネージド ディスクのサイズ (エラー コード 150172)

このエラーが発生すると、次のメッセージが表示されます。

> "Protection couldn't be enabled for the virtual machine as it has *DiskName* with size *DiskSize*)* that is lesser than the minimum supported size 1024 MB." (1024 MB のサポートされている最小サイズ未満のサイズ *DiskSize* を持つ *DiskName* が存在するため、仮想マシンの保護を有効にできませんでした。)

### <a name="possible-cause"></a>考えられる原因

ディスクが、サポートされているサイズの 1024 MB を下回っています。

### <a name="fix-the-problem"></a>問題の解決

ディスク サイズがサポートされているサイズの範囲内にあることを確認し、操作を再試行してください。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>GRUB 構成に UUID ではなくデバイス名が含まれているため、保護が有効になりませんでした (エラー コード 151126)

### <a name="possible-cause"></a>考えられる原因

Linux GRUB 構成ファイル (/boot/grub/menu.lst"、/boot/grub/grub.cfg、/boot/grub2/grub.cfg、または /etc/default/grub) で、*root* パラメーターと *resume* パラメーターの値として、UUID でなく、実際のデバイス名が指定されている可能性があります。 デバイス名は変更できるため、Site Recovery には UUID が必要です。 再起動時のフェールオーバー時に VM が同じ名前で表示されず、問題が発生する可能性があります。

次の例は、必要な UUID ではなくデバイス名 (太字で示されています) が表示される、GRUB ファイルの行です。

- ファイル: /boot/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- ファイル: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>問題の解決

各デバイス名を対応する UUID に置き換えます。

1. **blkid** ***device name*** コマンドを実行して、デバイスの UUID を検出します。 例:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 次に、**root=UUID**=*UUID* および **resume=UUID**=*UUID* のような形式で、デバイス名を UUID に置き換えます。 たとえば、前述の /boot/grub/menu.lst の行は置換後に次のようになります。

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. 保護を再試行してください。

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>GRUB 構成で指定されているデバイスが存在しないため、保護を有効にできませんでした (エラー コード 151124)

### <a name="possible-cause"></a>考えられる原因

GRUB 構成ファイル (/boot/grub/menu.lst、/boot/grub/grub.cfg、/boot/grub2/grub.cfg、または /etc/default/grub) には、*rd.lvm.lv* または *rd_LVM_LV* というパラメーターを含めることができます。 これらのパラメーターは、起動時に検出される論理ボリューム マネージャ (LVM) デバイスを識別します。 これらの LVM デバイスが存在しない場合、保護されたシステム自体は起動せず、起動プロセスでスタックします。 フェールオーバー VM にも同じ問題が確認されます。 次に例をいくつか示します。

- ファイル: /boot/grub2/grub.cfg (RHEL7 の場合):

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8

- ファイル: /etc/default/grub (RHEL7 の場合):

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- ファイル: /boot/grub/menu.lst (RHEL6 の場合):

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root**  KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

それぞれの例で、太字の部分は、"root" および "swap" という名前の 2 つの LVM デバイスを、GRUB がボリューム グループ "rootvg" から検出する必要があることを示しています。

### <a name="fix-the-problem"></a>問題の解決

LVM デバイスが存在しない場合は、このデバイスを作成するか、GRUB 構成ファイルから対応するパラメーターを削除します。 次に、再び保護を有効にしてください。

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Site Recovery モビリティ サービスの更新が完了しましたが、警告が表示されます (エラー コード 151083)

Site Recovery モビリティ サービスには多数のコンポーネントがありますが、そのうちの 1 つに、フィルター ドライバーと呼ばれるものがあります。 フィルター ドライバーは、システムの再起動時にのみ、システム メモリ内に読み込まれます。 モビリティ サービスの更新にフィルター ドライバーの変更が含まれている場合、マシンは更新されますが、一部の修正プログラムで再起動が必要であることを示す警告が表示されます。 この警告が表示される理由は、フィルター ドライバーの修正は、新しいフィルター ドライバーが読み込まれたときにのみ有効になるためであり、これは再起動中にのみ発生します。

> [!NOTE]
> これはただの警告です。 既存のレプリケーションは、新しいエージェントが更新された後も引き続き機能します。 再起動は、新しいフィルター ドライバーの利点を活用したい任意のタイミングで実行できますが、再起動しない場合は古いフィルター ドライバーが動作し続けます。
>
> フィルター ドライバーを除く、モビリティ サービスの更新におけるその他の機能強化や修正の利点は、再起動を必要とせずに有効になります。  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>予期されるタグがないレプリカ マネージド ディスクがターゲット リソース グループに既に存在するため、保護を有効にできませんでした (エラー コード 150161)

### <a name="possible-cause"></a>考えられる原因

この問題は、仮想マシンが以前保護されていて、レプリケーションを無効にしたときにレプリカ ディスクが消去されていない場合に発生する可能性があります。

### <a name="fix-the-problem"></a>問題の解決

エラー メッセージに示されているレプリカ ディスクを削除し、失敗した保護ジョブを再度開始します。

## <a name="next-steps"></a>次の手順

[Azure 仮想マシンのレプリケート](site-recovery-replicate-azure-to-azure.md)
