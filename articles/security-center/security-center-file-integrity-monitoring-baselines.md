---
title: Azure Security Center のファイルの整合性の監視とベースラインを比較する | Microsoft Docs
description: Azure Security Center のファイルの整合性の監視とベースラインを比較する方法を説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: afc03baa71f17deb0b923f483fde214a86c5e9b4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296466"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>ファイルの整合性の監視 (FIM) を使用してベースラインを比較する

ファイルの整合性の監視 (FIM) では、リソース内の機密性の高い領域に対する変更が発生すると通知されるため、不正なアクティビティを調査して対処することができます。 FIM では、Windows ファイル、Windows レジストリ、および Linux ファイルが監視されます。

このトピックでは、ファイルとレジストリに対して FIM を有効にする方法について説明します。 FIM の詳細については、「[Azure Security Center のファイルの整合性の監視](security-center-file-integrity-monitoring.md)」を参照してください。

## <a name="why-use-fim"></a>FIM を使用する理由

オペレーティング システム、アプリケーション、および関連付けられている構成は、リソースの動作とセキュリティの状態を制御します。 そのため攻撃者は、リソースのオペレーティング システムに襲い掛かったり、検出されずにアクティビティを実行したりするために、リソースを制御するファイルを標的にします。

実際に、FIM の制御を実装するには、PCI DSS や ISO 17799 などのさまざまな規制コンプライアンス基準が必要です。  

## <a name="enable-built-in-recursive-registry-checks"></a>組み込みの再帰レジストリ チェックを有効にする

FIM レジストリ ハイブの既定値は、一般的なセキュリティ領域内で再帰的な変更を監視する便利な手段を提供します。  たとえば、敵対者は、起動時やシャットダウン時に実行を構成することによって、LOCAL_SYSTEM コンテキストで実行されるスクリプトを構成することがあります。  この種類の変更を監視するには、組み込みのチェックを有効にします。  

![レジストリ](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 再帰的なチェックは、カスタムのレジストリ パスではなく、推奨されるセキュリティ ハイブにのみ適用されます。  

## <a name="adding-a-custom-registry-check"></a>カスタム のレジストリ チェックの追加

FIM ベースラインは、オペレーティング システムとサポートするアプリケーションの既知の正常な状態の特性を識別することから始めます。  この例では、Windows Server 2008 以降のパスワード ポリシーの構成について説明します。


|ポリシー名                 | レジストリ設定|
|---------------------------------------|-------------|
|ドメイン コントローラー:マシン アカウントのパスワードの変更を拒否します| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|ドメインのメンバー: セキュリティで保護されたチャネルのデータをデジタルで暗号化または署名します (常時)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|ドメインのメンバー: セキュリティで保護されたチャネルのデータをデジタルで暗号化します (可能な場合)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|ドメインのメンバー: セキュリティで保護されたチャネルのデータにデジタル署名します (可能な場合)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|ドメインのメンバー: マシン アカウントのパスワードの変更を無効にします|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|ドメインのメンバー: マシン アカウントのパスワード変更の最大有効期間|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|ドメインのメンバー: 強力な (Windows 2000 以降) セッション キーが必要です|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|ネットワークのセキュリティ:NTLMP の制限:このドメインの NTLM 認証|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|ネットワークのセキュリティ:NTLMP の制限:このドメインにサーバーの例外を追加します|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|ネットワークのセキュリティ:NTLMP の制限:このドメインの NTLM 認証を監査します|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> さまざまなオペレーティング システムのバージョンでサポートされているレジストリ設定の詳細については、[グループ ポリシー設定リファレンスのスプレッドシート](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250)を参照してください。

*レジストリのベースラインを監視するように FIM を構成するには、次の手順に従います。*

1. **[変更履歴用の Windows レジストリを追加する]** ウィンドウの **[Windows レジストリ キー]** テキスト ボックスに、レジストリ キーを入力します。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![レジストリに対する FIM を有効にする](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows ファイルに対する変更の追跡

1. **[Windows ファイル変更を追跡するための追加]** ウィンドウの **[パスの入力]** テキスト ボックスに、追跡するファイルが含まれているフォルダーを入力します。次の図の例では、**Contoso の Web アプリ**は **ContosWebApp** フォルダー構造内の D:\ ドライブにあります。  
1. 設定クラスの名前を入力し、再帰を有効にして、ワイルドカード (*) サフィックスで最上位のフォルダーを指定することで、カスタムの Windows ファイルのエントリを作成します。

    ![ファイルに対する FIM を有効にする](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>変更データの取得

ファイルの整合性の監視データは、Azure Log Analytics / ConfigurationChange テーブル セット内にあります。  

 1. リソースごとの変更の概要を取得する時間の範囲を設定します。
次の例では、レジストリとファイルのカテゴリの過去 14 日間のすべての変更を抽出しています。

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. レジストリの変更の詳細を確認するには、次の手順に従います。

    1. **where** 句から **Files** を削除します。 
    1. 集計行を削除し、並べ替え句に置き換えます。

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

レポートは、アーカイブや Power BI レポートへのチャンネル用に CSV にエクスポートできます。  

![FIM データ](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)