---
title: Azure 間レプリケーションの問題とエラーに関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするとき発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034795"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 間の VM レプリケーションに関する問題のトラブルシューティング

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題と、そのトラブルシューティング方法について説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。

## <a name="list-of-errors"></a>エラー一覧
- **[Azure リソースのクォータに関する問題 (エラー コード 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[信頼されたルート証明書 (エラー コード 151066)](#trusted-root-certificates-error-code-151066)**
- **[Site Recovery の送信接続 (エラー コード 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure リソースのクォータに関する問題 (エラー コード 150097)
ディザスター リカバリー リージョンとして使用するターゲット リージョンで Azure VM を作成するには、サブスクリプションを有効にする必要があります。 また、特定のサイズの VM を作成するには、サブスクリプションで十分なクォータが有効になっている必要もあります。 Site Recovery では、既定で、ソース VM と同じサイズがターゲット VM に対して選択されます。 同じサイズを使用できない場合は、最も近いサイズが自動的に取得されます。 このエラー メッセージは、ソース VM 構成をサポートするサイズと一致するものがない場合に表示されます。

**エラー コード** | **考えられる原因** | **推奨事項**
--- | --- | ---
150097<br></br>**メッセージ**:仮想マシン VmName のレプリケーションを有効にできませんでした。 | - ターゲット リージョンの場所で VM を作成するための、サブスクリプション ID が有効になっていない可能性があります。</br></br>- ターゲット リージョンの場所で特定の VM サイズを作成するための、サブスクリプション ID が有効になっていない可能性があります。または、十分なクォータが確保されていません。</br></br>- ターゲット リージョンの場所のサブスクリプション ID について、ソース VM の NIC 数 (2) と一致する適切なターゲットVM サイズが見つかりません。| [Azure 課金のサポート](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)に連絡して、サブスクリプションのターゲットの場所で、必要な VM サイズの VM 作成を有効にしてください。 有効にした後、失敗した操作を再試行してください。

### <a name="fix-the-problem"></a>問題の解決
[Azure 課金のサポート](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)に連絡して、サブスクリプションが、必要なサイズの VM をターゲットの場所で作成できるようにしてください。

ターゲットの場所に容量の制約がある場合は、レプリケーションを無効にしてから、必要なサイズの VM を作成できるだけのクォータが確保されているサブスクリプションがある別の場所へのレプリケーションを有効にします。

## <a name="trusted-root-certificates-error-code-151066"></a>信頼されたルート証明書 (エラー コード 151066)

すべての最新の信頼されたルート証明書が VMにない場合、"レプリケーションの有効化" ジョブが失敗することがあります。 証明書がないと、VM からの Site Recovery サービス呼び出しの認証と承認が失敗します。 失敗した "レプリケーションの有効化" Site Recovery ジョブのエラー メッセージが表示されます。

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
151066<br></br>**メッセージ**:Site Recovery の構成に失敗しました。 | 承認と認証に使用される、必要な信頼されたルート証明書がマシンに存在しません。 | - Windows オペレーティング システムを実行している VM については、信頼されたルート証明書がマシンに存在することを確認します。 詳細については、「[信頼されたルートおよび許可されない証明書を構成する](https://technet.microsoft.com/library/dn265983.aspx)」を参照してください。<br></br>- Linux オペレーティング システムを実行している VM については、Linux オペレーティング システム バージョンのディストリビューターによって発行された、信頼されたルート証明書のガイダンスに従ってください。

### <a name="fix-the-problem"></a>問題の解決
**Windows**

すべての最新 Windows 更新プログラムを VM にインストールし、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows 更新プロセスに従って、証明書を取得してください。 VM に必要な証明書がない場合は、セキュリティ上の理由から、Site Recovery サービスへの呼び出しが失敗します。

組織の一般的な Windows 更新管理プロセスまたは証明書更新管理プロセスに従って、VM ですべての最新ルート証明書と更新済み証明書失効リストを取得します。

問題が解決されたことを確認するには、VM のブラウザーから login.microsoftonline.com にアクセスします。

**Linux**

VM の最新の信頼されたルート証明書と最新の証明書失効リストを取得するには、Linux ディストリビューターによって提供されるガイダンスに従います。

SuSE Linux ではシンボリック リンクを使用して証明書リストを保持しているため、次の手順に従います。

1.  ルート ユーザーとしてサインインします。

2.  次のコマンドを実行してディレクトリを変更します。

      ``# cd /etc/ssl/certs``

1. Symantec ルート CA 証明書が存在するかどうかを確認します。

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Symantec ルート CA 証明書が見つからない場合は、次のコマンドを実行してファイルをダウンロードします。 エラーがないか確認し、ネットワーク障害に対する推奨アクションに従います。

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Baltimore ルート CA 証明書が存在するかどうかを確認します。

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Baltimore ルート CA 証明書が見つからない場合は、証明書をダウンロードします。  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. DigiCert_Global_Root_CA 証明書が存在するかどうかを確認します。

    ``# ls DigiCert_Global_Root_CA.pem``

6. DigiCert_Global_Root_CA が見つからない場合は、次のコマンドを実行して証明書をダウンロードします。

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. 再ハッシュ スクリプトを実行して、新たにダウンロードした証明書のサブジェクト ハッシュを更新します。

    ``# c_rehash``

8.  シンボリック リンクとしてのサブジェクト ハッシュが証明書用に作成されているかどうかを確認します。

    - command

      ``# ls -l | grep Baltimore``

    - Output

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - command

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - command

      ``# ls -l | grep DigiCert_Global_Root``

    - Output

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  ファイル VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem のコピーを b204d74a.0 というファイル名で作成します。

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. ファイル Baltimore_CyberTrust_Root.pem のコピーを 653b494a.0 というファイル名で作成します。

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. ファイル DigiCert_Global_Root_CA.pem のコピーを 3513523f.0 というファイル名で作成します。

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. これらのファイルが存在するかどうかを確認します。  

    - command

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL または IP 範囲の送信接続 (エラー コード 151037 または 151072)

Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1: Azure 仮想マシンを Site Recovery に登録できませんでした (151195) </br>
- **考えられる原因** </br>
  - DNS を解決できないため、Site Recovery エンドポイントへの接続を確立できません。
  - これが頻繁に発生するのは、仮想マシンをフェールオーバーして再保護を行っているときに、DR リージョンから DNS サーバーに到達できない場合です。

- **解決策**
   - カスタム DNS を使っている場合は、ディザスター リカバリー リージョンから DNS サーバーにアクセスできることを確認します。 カスタム DNS かどうかを確認するには、VM > ディザスター リカバリー ネットワーク > DNS サーバーに移動します。 仮想マシンから DNS サーバーにアクセスしてみます。 アクセスできない場合は、DNS サーバーをフェールオーバーするか、または DR ネットワークと DNS の間にサイトのラインを作成して、アクセスできるようにします。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2:Site Recovery の構成に失敗しました (151196)
- **考えられる原因** </br>
  - Office 365 認証と ID IP4 エンドポイントへの接続を確立できません。

- **解決策**
  - Azure Site Recovery では、認証のために Office 365 の IP 範囲にアクセスする必要がありました。
    VM での発信ネットワーク接続の制御に Azure ネットワーク セキュリティ グループ (NSG) のルール/ファイアウォール プロキシを使っている場合は、確実に O365 の IP 範囲への通信を許可します。 AAD に対応するすべての IP アドレスへのアクセスを許可するには、[Azure Active Directory (AAD) サービス タグ](../virtual-network/security-overview.md#service-tags) ベースの NSG ルールを作成します。
      - 今後、新しいアドレスが Azure Active Directory (AAD) に追加された場合は、新しい NSG ルールを作成する必要があります。

> [!NOTE]
> 仮想マシンは、**Standard** 内部ロード バランサーの背後にある場合、既定では O365 の IP、つまり login.microsoftonline.com にアクセスできません。 これを **Basic** 内部ロード バランサーの種類に変更するか、この[記事](https://aka.ms/lboutboundrulescli)の説明に従ってアウトバインド アクセスを作成します。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3:Site Recovery の構成に失敗しました (151197)
- **考えられる原因** </br>
  - Azure Site Recovery サービスのエンドポイントに対する接続を確立できません。

- **解決策**
  - Azure Site Recovery では、リージョンに基づいて [Site Recovery の IP 範囲](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)にアクセスする必要がありました。 必要な IP 範囲に仮想マシンからアクセスできることを確認します。


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4:ネットワーク トラフィックがオンプレミス プロキシ サーバーを経由するときに A2A レプリケーションが失敗しました (151072)
- **考えられる原因** </br>
  - カスタム プロキシ設定が無効であり、Azure Site Recovery Mobility Service エージェントが IE からプロキシ設定を自動検出しませんでした


- **解決策**
  1. モビリティ サービス エージェントは、Windows では IE から、Linux では /etc/environment からプロキシ設定を検出します。
  2. プロキシを Azure Site Recovery Mobility Service にのみ設定する場合は、次の場所にある ProxyInfo.conf 内にプロキシの詳細を指定できます。</br>
     - ***Linux*** の場合は ``/usr/local/InMage/config/``
     - ***Windows*** の場合は ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
  3. ProxyInfo.conf 内のプロキシ設定は、次の INI 形式になっている必要があります。</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery Mobility Service エージェントは、"***認証されていないプロキシ***" のみをサポートします。


### <a name="fix-the-problem"></a>問題の解決
[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または[必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)を許可するには、[ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)の次の手順に従ってください。

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>ディスクがマシンに見つかりません (エラー コード 150039)

VM に接続された新しいディスクを初期化する必要があります。

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
150039<br></br>**メッセージ**:論理ユニット番号 (LUN) が (LUNValue) の Azure データ ディスク (DiskName) (DiskURI) が、VM 内部から報告されている同じ LUN 値のディスクにマップされませんでした。 | - 新しいデータ ディスクが VM に接続されているが、初期化されませんでした。</br></br>- VM 内部のデータ ディスクは、そのディスクが VM に接続されている LUN 値を正しく報告していません。| データ ディスクが初期化されていることを確認し、操作を再試行します。</br></br>Windows の場合:[新しいディスクを接続し、初期化する](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。</br></br>Linux の場合:[Linux で新しいデータ ディスクを初期化する](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

### <a name="fix-the-problem"></a>問題の解決
データ ディスクが初期化されていることを確認し、操作を再試行します。

- Windows の場合:[新しいディスクを接続し、初期化する](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)。
- Linux の場合: [Linux で新しいデータ ディスクを追加する](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)。

問題が解決しない場合は、サポートにお問い合わせください。

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>1 つ以上のディスクで保護が使用可能です (エラー コード 153039)
- **考えられる原因** </br>
  - 保護の後に、1 つまたは複数のディスクが最近仮想マシンに追加された。 
  - 仮想マシンの保護の後に、1 つまたは複数のディスクが初期化された。

### <a name="fix-the-problem"></a>問題の解決
これらのディスクを保護するか、警告を無視して、VM のレプリケーション状態をもう一度正常にします。</br>
1. ディスクを保護するには、 [レプリケートされたアイテム] > [VM] > [ディスク] に移動し、保護されていないディスクで [レプリケーションの有効化] をクリックします。
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. 警告を無視するには、 [レプリケートされたアイテム] > [VM] に移動し、概要セクションで [アラートを無視] をクリックします。
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>情報メッセージ (エラー コード 150225) が表示されて完了したコンテナーから仮想マシンを削除する
仮想マシンを保護すると、Azure Site Recovery によって、ソース仮想マシンにいくつかのリンクが作成されます。 保護を削除するか、レプリケーションを無効にすると、Azure Site Recovery によるクリーンアップ ジョブの一環として、これらのリンクが削除されます。 仮想マシンにリソース ロックが設定されている場合、ジョブが完了するときに情報メッセージが表示されます。 そのメッセージには、仮想マシンが Recovery Services コンテナーから削除されたが、ソース マシンから古いリンクの一部をクリーンアップできなかったことが表示されます。

今後この仮想マシンを保護する予定がない場合、この警告は無視してもかまいません。 ただし、後でこの仮想マシンを保護する必要がある場合は、以下の手順の説明に従ってリンクをクリーンアップします。 

**クリーンアップしないと、次のような事態が発生します。**

1.  Recovery Services コンテナーを介したレプリケーションが有効になっている間、仮想マシンが表示されません。 
2.  **仮想マシン > [設定] > [ディザスター リカバリー]** を使用して VM を保護しようとすると、"*VM の既存のリソース リンクが古いため、レプリケーションを有効にすることはできません*" というエラー メッセージが表示されて失敗します。


### <a name="fix-the-problem"></a>問題の解決

>[!NOTE]
>
>以下の手順の実行中は、Azure Site Recovery でソース仮想マシンを削除することも、何らかの方法でソース仮想マシンに影響を与えることもできません。
>

1. VM または VM リソース グループから、ロックを削除します。 例: VM 名 "MoveDemo" の下に、削除する必要があるリソース ロックがあります。

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. [古い Azure Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
3. *Cleanup-stale-asr-config-Azure-VM.ps1* スクリプトを実行します。
4. パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
5. Azure の資格情報を求められたら それを入力し、スクリプトがエラーなしに実行されたことを確認してください。 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>VM に既存の古いリソース リンクがあるためレプリケーションを有効にできない (エラー コード 150226)

**原因: 仮想マシンに、以前の Site Recovery 保護の古い構成が残されている**

古い構成は、次の場合に Azure VM に残る可能性があります。

- Site Recovery を使用して Azure VM のレプリケーションを有効にし、後でレプリケーションを無効にしたが、**ソース VM にリソース ロックが設定されていた**。
- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。

### <a name="fix-the-problem"></a>問題の解決

>[!NOTE]
>
>以下の手順の実行中は、Azure Site Recovery でソース仮想マシンを削除することも、何らかの方法でソース仮想マシンに影響を与えることもできません。


1. VM または VM リソース グループから、ロックを削除します (ロックが設定されている場合)。 *例:* VM 名 "MoveDemo" の下に、削除する必要があるリソース ロックがあります。
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. [古い Azure Site Recovery 構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
3. *Cleanup-stale-asr-config-Azure-VM.ps1* スクリプトを実行します。
4. パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
5. Azure の資格情報を求められたら それを入力し、スクリプトがエラーなしに実行されたことを確認してください。  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>"レプリケーションの有効化" で、選択する Azure VM またはリソース グループが表示されない

 **原因 1:リソース グループとソース仮想マシンが別々の場所にある**
 
Azure Site Recovery では現在、ソース リージョンのリソース グループと仮想マシンが同じ場所にあることが必須となっています。 そうでない場合、保護されている間は、仮想マシンもリソース グループも見つけることができません。 

**この問題の回避策として**、Recovery Services コンテナーではなく、VM からレプリケーションを有効にします。 ソース VM > [プロパティ] > [ディザスター リカバリー] に移動し、レプリケーションを有効にします。

**原因 2:このリソース グループが、選択されたサブスクリプションの一部でない**

特定のサブスクリプションの一部ではない場合、保護されたときにリソース グループを見つけられない可能性があります。 リソース グループが、使用されているサブスクリプションに属していることを確認します。

 **原因 3:構成が古い**
 
レプリケーションを有効にする VM が表示されない場合は、Azure VM 上に古い Site Recovery 構成が残されていることが原因である可能性があります。 古い構成は、次の場合に Azure VM に残る可能性があります。

- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを削除した。
- Site Recovery を使用して Azure VM のレプリケーションを有効にし、VM でレプリケーションを明示的に無効にせずに、Site Recovery コンテナーを含むリソース グループを削除した。

- Site Recovery を使用して Azure VM のレプリケーションを有効にし、後でレプリケーションを無効にしたが、ソース VM にリソース ロックが設定されていた。

### <a name="fix-the-problem"></a>問題の解決

> [!NOTE]
>
> 以下のスクリプトを使用する前に、""AzureRM.Resources"" モジュールを必ず更新するようにしてください。 以下の手順の実行中は、Azure Site Recovery でソース仮想マシンを削除することも、何らかの方法でソース仮想マシンに影響を与えることもできません。
>

1. VM または VM リソース グループから、ロックを削除します (ロックが設定されている場合)。 *例:* VM 名 "MoveDemo" の下に、削除する必要があるリソース ロックがあります。

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. [古い構成を削除する](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)ためのスクリプトをダウンロードします。
3. *Cleanup-stale-asr-config-Azure-VM.ps1* スクリプトを実行します。
4. パラメーターとして、サブスクリプション ID、VM リソース グループ、および VM 名を指定します。
5. Azure の資格情報を求められたら それを入力し、スクリプトがエラーなしに実行されたことを確認してください。

## <a name="unable-to-select-virtual-machine-for-protection"></a>保護対象の仮想マシンを選択できない
 **原因 1:仮想マシンの何らかの拡張機能のインストールが失敗したかまたは応答しない状態にある** <br>
 [仮想マシン]、[設定]、[拡張機能] の順に進み、拡張機能のいずれかが失敗した状態になっていないか確認します。 失敗した拡張機能をアンインストールして、仮想マシンの保護を再試行します。<br>
 **原因 2:[VM のプロビジョニングの状態が有効ではありません](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM のプロビジョニングの状態が正しくありません (エラー コード 150019)

VM でレプリケーションを有効にするには、プロビジョニングの状態が **[成功]** になっている必要があります。 次の手順に従って、VM の状態を確認できます。

1.  Azure Portal の **[All Services]\(すべてのサービス\)** から **[リソース エクスプローラー]** を選択します。
2.  **[サブスクリプション]** 一覧を展開して、自分のサブスクリプションを選択します。
3.  **[ResourceGroups]** 一覧を展開して、VM のリソース グループを選択します。
4.  **[リソース]** 一覧を展開して、お使いの仮想マシンを選択します。
5.  右側にあるインスタンス ビューで、 **[provisioningState]** フィールドを確認します。

### <a name="fix-the-problem"></a>問題の解決

- **[provisioningState]** が **[失敗]** になっている場合は、サポートに詳細を問い合わせてトラブルシューティングします。
- **[provisioningState]** が **[更新中]** になっている場合は、別の拡張機能がデプロイされている可能性があります。 VM 上に何らかの進行中の操作があるかを確認し、それらの操作が完了するまで待機してから、失敗した Site Recovery の **[レプリケーションを有効にする]** ジョブを再試行します。

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>ターゲットの仮想ネットワークを選択できない -[ネットワークの選択] タブが淡色表示される

**原因 1:"ターゲット ネットワーク" に既にマップされているネットワークに VM が接続されている場合。**
- ソース VM が仮想ネットワークの一部であるときに、同じ仮想ネットワークの別の VM がターゲット リソース グループ内のネットワークに既にマップされている場合、[ネットワークの選択] ドロップダウンは既定で無効になります。

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**原因 2:Azure Site Recovery を使用して VM を保護し、レプリケーションを無効にしたことがある場合。**
 - VM のレプリケーションを無効にしても、ネットワーク マッピングは削除されません。 VM が保護されていた Recovery Service コンテナーからそれを削除する必要があります。 </br>
 Recovery Service コンテナー > [Site Recovery インフラストラクチャ] > [ネットワーク マッピング] に移動します。 </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - ディザスター リカバリーのセットアップ中に構成されたターゲット ネットワークが、初期セットアップ後に VM が保護された後で変更された可能性があります。 </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - ネットワーク マッピングを変更すると、その特定のネットワーク マッピングを使用するすべての保護された VM に影響することに注意してください。


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/ボリューム シャドウ コピー サービス エラー (エラー コード 151025)

**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
151025<br></br>**メッセージ**:Site Recovery 拡張機能をインストールできませんでした | - COM+ システム アプリケーション サービスが無効になっています。</br></br>- ボリューム シャドウ コピー サービスが無効になっています。| COM+ システム アプリケーション サービスとボリューム シャドウ コピー サービスを、自動または手動スタートアップ モードに設定します。

### <a name="fix-the-problem"></a>問題の解決

[サービス] コンソールを開いて、COM+ システム アプリケーションとボリューム シャドウ コピーの [スタートアップの種類] が [無効] に設定されていないことを確認します。
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>サポートされていないマネージド ディスクのサイズ (エラー コード 150172)


**エラー コード** | **考えられる原因** | **Recommendations (推奨事項)**
--- | --- | ---
150172<br></br>**メッセージ**:1024 MB のサポートされている最小サイズ未満のサイズ (DiskSize) を持つ (DiskName) が存在するため、仮想マシンの保護を有効にできませんでした。 | - このディスクは 1024 MB のサポートされているサイズ未満です| ディスク サイズがサポートされているサイズの範囲内にあることを確認し、操作を再試行してください。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>GRUB 構成で UUID ではなくデバイス名が指定されているため、保護を有効にできなかった (エラー コード 151126)

**考えられる原因:** </br>
GRUB 構成ファイル ("/boot/grub/menu.lst"、"/boot/grub/grub.cfg"、"/boot/grub2/grub.cfg"、または "/etc/default/grub") で、**root** パラメーターと **resume** パラメーターの値として、UUID でなく、実際のデバイス名が含まれている可能性があります。 デバイス名は VM のリブートによって変更される可能性があり、フェールオーバー時に VM が同じ名前で起動されないことで問題が発生するため、Site Recovery では UUID を使用する必要があります。 例: </br>


- GRUB ファイル **/boot/grub2/grub.cfg** の次の行。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- GRUB ファイル **/boot/grub/menu.lst** の次の行。
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

上記の太字の文字列がある場合、GRUB では、UUID ではなく、"resume" パラメーターと "root" パラメーターの実際のデバイス名が使用されています。

**修正方法:**<br>
デバイス名を対応する UUID に置き換える必要があります。<br>


1. "blkid \<デバイス名>" コマンドを実行して、デバイスの UUID を検出します。 例:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. 次に、"root=UUID=\<UUID>" のような形式で、デバイス名を UUID に置き換えます。 たとえば、"/boot/grub2/grub.cfg"、"/boot/grub2/grub.cfg" または "/etc/default/grub" ファイルで、デバイス名を上記の root および resume パラメーターの UUID に置き換えた場合、ファイル内の行は次のようになります。 <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. 保護を再起動します

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>GRUB 構成で指定されているデバイスが存在しないため、保護を有効にできなかった (エラー コード 151124)
**考えられる原因:** </br>
GRUB 構成ファイル ("/boot/grub/menu.lst"、"/boot/grub/grub.cfg"、"/boot/grub2/grub.cfg"、または "/etc/default/grub") には、起動時に検出される必要がある LVM デバイスを示す "rd.lvm.lv" または "rd_LVM_LV" というパラメーターを含めることができます。 これらの LVM デバイスが存在しない場合、保護されたシステム自体は起動せず、起動プロセスでスタックします。 フェールオーバー VM でも同じことが見られます。 次に例をいくつか示します。

いくつかの例: </br>

1. 次の行は、RHEL7 上の GRUB ファイル **"/boot/grub2/grub.cfg"** からのものです。 </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   ここで強調されている部分は、 **"root"** および **"swap"** という名前の 2 つの LVM デバイスを、GRUB がボリューム グループ "rootvg" から検出する必要があることを示しています。
1. 次の行は、RHEL7 上の GRUB ファイル **"/etc/default/grub"** からのものです。 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   ここで強調されている部分は、 **"root"** および **"swap"** という名前の 2 つの LVM デバイスを、GRUB がボリューム グループ "rootvg" から検出する必要があることを示しています。
1. 次の行は、RHEL6 上の GRUB ファイル **"/boot/grub/menu.lst"** からのものです。 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   ここで強調されている部分は、 **"root"** および **"swap"** という名前の 2 つの LVM デバイスを、GRUB がボリューム グループ "rootvg" から検出する必要があることを示しています。<br>

**修正方法:**<br>

LVM デバイスが存在しない場合は、それを作成するか、同じものに対するパラメーターを GRUB 構成ファイルから削除することによって問題を修正し、保護の有効化を再試行します。 </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Site Recovery モビリティ サービスの更新が完了したが、警告が発生した (エラー コード 151083)
Site Recovery モビリティ サービスには多数のコンポーネントがありますが、そのうちの 1 つに、フィルター ドライバーと呼ばれるものがあります。 フィルター ドライバーは、システムの再起動時にのみ、システム メモリ内に読み込まれます。 フィルター ドライバーの変更を伴う Site Recovery モビリティ サービスの更新があるたびに、マシンが更新されますが、一部の修正には再起動が必要であるという警告がまだ表示されます。 つまり、フィルター ドライバーの修正は、新しいフィルター ドライバーが読み込まれたときにのみ実現できます。そしてその読み込みは、システムの再起動時にのみ発生します。<br>
なお、これは単なる警告であり、既存のレプリケーションは、新しいエージェントの更新後も動作し続けることに**注意してください**。 再起動はいつでも実行でき、それにより新しいフィルター ドライバーのメリットを享受できますが、再起動しなかった場合も、古いフィルター ドライバーは動作を継続します。 フィルター ドライバーとは別に、**モビリティ サービスの他の機能強化や修正のメリットは、エージェントが更新されたときに再起動しなくても実現されます。**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>予期されるタグがないレプリカ マネージド ディスク 'diskname-replica' がターゲット リソース グループにすでに存在するため、保護を有効にできなかった (エラー コード 150161)

**原因**:これは、仮想マシンが以前に保護されていて、レプリケーションの無効化中に何らかの理由でレプリカ ディスクがクリーンにされなかった場合に発生する可能性があります。</br>
**修正方法:** エラー メッセージに示されているレプリカ ディスクを削除し、失敗した保護ジョブを再度開始します。

## <a name="next-steps"></a>次の手順
[Azure 仮想マシンのレプリケート](site-recovery-replicate-azure-to-azure.md)
