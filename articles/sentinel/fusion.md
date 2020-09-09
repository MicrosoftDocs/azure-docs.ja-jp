---
title: Azure Sentinel の高度なマルチステージ攻撃の検出
description: Azure Sentinel の Fusion テクノロジを利用することで、アラートによる疲労を減らし、高度なマルチステージ攻撃の検出に基づいて、すぐに使用可能なインシデント (事象) を作成します。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "77587925"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel の高度なマルチステージ攻撃の検出


> [!IMPORTANT]
> Azure Sentinel の一部の Fusion 機能は、現在パブリック プレビュー段階にあります。
> これらの機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



機械学習を基盤とする Fusion テクノロジを利用することで、Azure Sentinel では、キルチェーンのさまざまな段階で観察される異常な動作と疑わしい行動を組み合わせ、マルチステージ攻撃を自動的に検出することができます。 その後、Azure Sentinel では、Azure Sentinel 以外では検出が非常に困難であろうインシデントが生成されます。 このインシデントには、2 つ以上のアラートまたはアクティビティが含まれています。 設計上、このようなインシデントでは、ボリュームが低、忠実度が高、重大度が高になります。

この検出機能はご利用の環境によってカスタマイズされるため、誤検知率を減らすだけでなく、情報が制限されているか、不足している攻撃も検出できます。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>高度なマルチステージ攻撃の検出の構成

この検出は、Azure Sentinel では既定で有効になっています。 状態を確認するか、複数のアラートに基づくインシデントの作成に代替ソリューションを利用しているなどの理由から無効にするには、次の指示に従います。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[Azure Sentinel]** 、 **[構成]** 、 **[分析]** の順に移動します。

3. **[アクティブな規則]** を選択し、 **[名前]** 列で **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** を見つけます。 **[状態]** 列を見て、この検出が有効になっているか、無効になっているか確認します。

4. 状態を変更するには、このエントリを選択し、 **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** ブレードで **[編集]** を選択します。

5. **[Rule creation wizard]\(規則作成ウィザード\)** ブレードで、状態の変更が自動的に選択されます。 **[次へ:レビュー]** を選択し、 **[保存]** を選択してください。 

高度なマルチステージ攻撃の検出には、規則テンプレートを使用できません。

> [!NOTE]
> 現在、Azure Sentinel では 30 日間の履歴データを使用して機械学習システムをトレーニングしています。 このデータは、機械学習パイプラインを通過するときに、Microsoft のキーを使用して常に暗号化されます。 ただし、Azure Sentinel ワークスペースで CMK を有効にした場合、トレーニング データは[カスタマー マネージド キー (CMK)](customer-managed-keys.md) を使用して暗号化されません。 Fusion をオプトアウトするには、 **[Azure Sentinel]**  \> **[Configuration]**  \> **[Analytics] \> [アクティブな規則] \> [Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** に移動し、 **[状態]** 列で **[無効にする]** を選択します。

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Palo Alto Networks と Microsoft Defender ATP を使用した Fusion

これらのシナリオは、セキュリティ アナリストが使用する 2 つの基本的なログを組み合わせたものです。Palo Alto Networks のファイアウォール ログと Microsoft Defender ATP のエンドポイント検出ログ。 以下に示すいずれのシナリオでも、外部 IP アドレスを含むエンドポイントの疑わしいアクティビティが検出され、その後、外部 IP アドレスからファイアウォールに異常なトラフィックが戻されます。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名化サービスに対するネットワーク要求の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。

このシナリオの場合、Azure Sentinel では、まず Microsoft Defender Advanced Threat Protection によって異常なアクティビティにつながる TOR 匿名化サービスへのネットワーク要求が検出された、というアラートが検出されます。 これは、{time} に SID ID {sid} のアカウント {account name} で開始されました。 接続に対する発信 IP アドレスは {IndividualIp} でした。
次に、Palo Alto Networks ファイアウォールによって {TimeGenerated} に異常なアクティビティが検出されました。 これは、悪意のあるトラフィックがネットワークに入ったことを示しています。ネットワーク トラフィックの宛先 IP アドレスは {DestinationIP} です。

現在、このシナリオはパブリック プレビュー段階です。


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell で疑わしいネットワーク接続が行われた後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。

このシナリオでは、まず、PowerShell で疑わしいネットワーク接続が作成されたことが Microsoft Defender Advanced Threat Protection によって検出された、というアラートが Azure Sentinel によって検出されます。これは、Palo Alto Network Firewall によって検出された異常なアクティビティの原因になるものです。 これは、{time} に SID ID {sid} のアカウント {account name} で開始されました。 接続に対する発信 IP アドレスは {IndividualIp} でした。 次に、Palo Alto Networks ファイアウォールによって {TimeGenerated} に異常なアクティビティが検出されました。 これは、悪意のあるトラフィックがネットワークに入ったことを示します。 ネットワーク トラフィックの宛先 IP アドレスは {DestinationIP} です。

現在、このシナリオはパブリック プレビュー段階です。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>承認されていないアクセス試行の履歴がある IP への送信接続の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続く

このシナリオでは、Azure Sentinel によって、Microsoft Defender Advanced Threat Protection で IP アドレスへの送信接続が検出されたというアラートが検出されます。これには、Palo Alto Networks Firewall によって検出される異常なアクティビティにつながる不正アクセスの履歴が含まれます。 これは、{time} に SID ID {sid} のアカウント {account name} で開始されました。 接続に対する発信 IP アドレスは {IndividualIp} でした。 この後、Palo Alto Networks ファイアウォールによって {TimeGenerated} に異常なアクティビティが検出されました。 これは、悪意のあるトラフィックがネットワークに入ったことを示します。 ネットワーク トラフィックの宛先 IP アドレスは {DestinationIP} です。

現在、このシナリオはパブリック プレビュー段階です。



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Identity Protection と Microsoft Cloud App Security を使用した Fusion

高度なマルチステージ攻撃の検出を利用することで、Azure Sentinel では、Azure Active Directory Identity Protection と Microsoft Cloud App Security からの異常なイベントを組み合わせる次のシナリオがサポートされています。

- [特殊な場所へのあり得ない移動の後の異常な Office 365 のアクティビティ](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [知らない場所からのサインイン アクティビティの後の異常な Office 365 のアクティビティ](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [感染したデバイスからのサインイン アクティビティの後の異常な Office 365 のアクティビティ](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [送信元のわからない IP アドレスからのサインイン アクティビティの後の異常な Office 365 のアクティビティ](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [資格情報が漏洩したユーザーからのサインイン アクティビティの後の異常な Office 365 のアクティビティ](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

[Azure AD Identity Protection データ コネクタ](connect-azure-ad-identity-protection.md)と [Cloud App Security](connect-cloud-app-security.md) コネクタを構成しておく必要があります。

以下の説明では、Azure Sentinel にお使いのデータから実際の値が表示されるものとしています。本ページでは、かっこ内の変数として示されています。 たとえば、\<*account name*> ではなくアカウントの実際の表示名が、\<*number*> ではなく実際の数字が表示されます。

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>特殊な場所へのあり得ない移動の後の異常な Office 365 のアクティビティ

Azure AD Identity Protection で検出した普通でない場所へのあり得ない移動に関する警告と Microsoft Cloud App Security で発生した Office 365 での異常なアクティビティに関する警告を組み合わせると、7 とおりの Azure Sentinel インシデントが生成されます。

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスからの情報の流出**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントと、その後にユーザーの受信トレイで受信トレイの疑わしい転送規則が設定されたことを示しています。
    
    これはアカウントが奪われ、組織から情報を流出させる目的でメールボックスが使用されていることを示している可能性があります。 ユーザー \<*account name*> が、すべての受信電子メールを外部のアドレス \<*email address*> に転送する受信トレイの転送規則を作成または更新しました。

- **特殊な場所へのあり得ない移動後の、クラウド アプリでの疑わしい管理行為**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number*> 個を超える管理アクティビティを実行しました。

- **特殊な場所へのあり得ない移動後の、大量のファイルの削除**
    
    このアラートは、異常な場所 \<*location*> への \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個の一意のファイルを削除しました。

- **特殊な場所へのあり得ない移動後の、大量のファイルのダウンロード**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルをダウンロードしました。

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスの偽装**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで通常とは異なる量 (\<*number of activities*>) の偽装アクティビティを実行しました。

- **特殊な場所へのあり得ない移動後の、大量のファイル共有**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルを共有しました。

- **特殊な場所へのあり得ない移動後の、クラウド アプリ内にランサムウェア**
    
    このアラートは、異常な場所 \<*location*> へのあり得ない移動からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は \<*number of*> 個のファイルをアップロードし、合計 \<*number of*> 個のファイルを削除しました。 
    
    このアクティビティ パターンはランサムウェア攻撃の可能性を示します。


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>知らない場所からのサインイン アクティビティの後の異常な Office 365 のアクティビティ

Azure AD Identity Protection で検出した知らない場所からのサインイン アクティビティに関する警告と Microsoft Cloud App Security で発生した異常な Office 365 のアクティビティに関する警告を組み合わせると、7 とおりの Azure Sentinel インシデントが生成されます。

- **知らない場所からサインインされた後の、Exchange Online メールボックスからの情報の流出**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントと、その後にユーザーの受信トレイで受信トレイの疑わしい転送規則が設定されたことを示しています。
    
    これはアカウントが奪われ、組織から情報を流出させる目的でメールボックスが使用されていることを示している可能性があります。 ユーザー \<*account name*> が、すべての受信電子メールを外部のアドレス \<*email address*> に転送する受信トレイの転送規則を作成または更新しました。 

- **知らない場所からサインインされた後の、クラウド アプリでの疑わしい管理行為**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える管理アクティビティを実行しました。

- **知らない場所からサインインされた後の、大量のファイルの削除された**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個の一意のファイルを削除しました。

- **知らない場所からサインインされた後の、大量のファイルのダウンロード**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルをダウンロードしました。

- **知らない場所からサインインされた後の、Office 365 での偽装**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える異なるアカウントを偽装しました。

- **知らない場所からサインインされた後の、大量のファイル共有**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルを共有しました。

- **知らない場所からサインインされた後の、クラウド アプリ内のランサムウェア**
    
    このアラートは、不明な場所 \<*location*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は \<*number of*> 個のファイルをアップロードし、合計 \<*number of*> 個のファイルを削除しました。 
    
    このアクティビティ パターンはランサムウェア攻撃の可能性を示します。

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>感染したデバイスからのサインイン アクティビティの後の異常な Office 365 のアクティビティ

Azure AD Identity Protection で検出した感染デバイスからのサインイン アクティビティに関する警告と Microsoft Cloud App Security で発生した異常な Office 365 のアクティビティに関する警告を組み合わせると、7 とおりの Azure Sentinel インシデントが生成されます。

- **感染したデバイスからサインインされた後の、Office 365 メールボックスからの情報の流出**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントと、その後にユーザーの受信トレイで受信トレイの疑わしい転送規則が設定されたことを示しています。
    
    これはアカウントが奪われ、組織から情報を流出させる目的でメールボックスが使用されていることを示している可能性があります。 ユーザー \<*account name*> が、すべての受信電子メールを外部のアドレス \<*email address*> に転送する受信トレイの転送規則を作成または更新しました。 

- **感染したデバイスからサインインされた後の、クラウド アプリでの疑わしい管理行為**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える管理アクティビティを実行しました。

- **感染したデバイスからサインインされた後の、大量のファイルの削除**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個の一意のファイルを削除しました。

- **感染したデバイスからサインインされた後の、大量のファイルのダウンロード**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルをダウンロードしました。

- **感染したデバイスからサインインされた後の、Office 365 での偽装**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える異なるアカウントを偽装しました。

- **感染したデバイスからサインインされた後の、大量のファイル共有**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルを共有しました。

- **感染したデバイスからサインインされた後の、クラウド アプリ内のランサムウェア**
    
    このアラートは、マルウェアに感染した可能性があるデバイスからの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は \<*number of*> 個のファイルをアップロードし、合計 \<*number of*> 個のファイルを削除しました。 
    
    このアクティビティ パターンはランサムウェア攻撃の可能性を示します。

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>送信元のわからない IP アドレスからのサインイン アクティビティの後の異常な Office 365 のアクティビティ

Azure AD Identity Protection で検出した送信元のわからない IP アドレスからのサインイン アクティビティに関する警告と Microsoft Cloud App Security で発生した異常な Office 365 のアクティビティに関する警告を組み合わせると、7 とおりの Azure Sentinel インシデントが生成されます。

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 メールボックスからの情報の流出**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントと、その後にユーザーの受信トレイで受信トレイの疑わしい転送規則が設定されたことを示しています。
    
    これはアカウントが奪われ、組織から情報を流出させる目的でメールボックスが使用されていることを示している可能性があります。 ユーザー \<*account name*> が、すべての受信電子メールを外部のアドレス \<*email address*> に転送する受信トレイの転送規則を作成または更新しました。 

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリでの疑わしい管理行為**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える管理アクティビティを実行しました。

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイルの削除**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個の一意のファイルを削除しました。

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイルのダウンロード**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルをダウンロードしました。

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 での偽装**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える異なるアカウントを偽装しました。

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイル共有**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルを共有しました。

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリでのランサムウェア**
    
    このアラートは、匿名プロキシ IP アドレス \<*IP address*> からの \<*account name*> によるサインイン イベントを示しています。 
    
    次に、アカウント \<*account name*> は \<*number of*> 個のファイルをアップロードし、合計 \<*number of*> 個のファイルを削除しました。 
    
    このアクティビティ パターンはランサムウェア攻撃の可能性を示します。

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>資格情報が漏洩したユーザーからのサインイン アクティビティの後の異常な Office 365 のアクティビティ

Azure AD Identity Protection で検出された資格情報が漏洩したユーザーからのサインイン アクティビティに関する警告と Microsoft Cloud App Security で発生した異常な Office 365 のアクティビティに関する警告を組み合わせると、7 とおりの Azure Sentinel インシデントが生成されます。

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 メールボックスからの情報の流出**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことと、その後にユーザーの受信トレイで受信トレイの疑わしい転送規則が設定されたことを示しています。 
    
    これはアカウントが奪われ、組織から情報を流出させる目的でメールボックスが使用されていることを示している可能性があります。 ユーザー \<*account name*> が、すべての受信電子メールを外部のアドレス \<*email address*> に転送する受信トレイの転送規則を作成または更新しました。 

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリでの疑わしい管理行為**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える管理アクティビティを実行しました。

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルの削除**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個の一意のファイルを削除しました。

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルのダウンロード**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルをダウンロードしました。

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 での偽装**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。 
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える異なるアカウントを偽装しました。

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイル共有**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。
    
    次に、アカウント \<*account name*> は 1 つのセッションで \<*number of*> 個を超える一意のファイルを共有しました。

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリ内のランサムウェア**
    
    このアラートは、\<*account name*> によるサインイン イベントで漏洩した資格情報が使用されたことを示しています。 
    
    次に、アカウント \<*account name*> は \<*number of*> 個のファイルをアップロードし、合計 \<*number of*> 個のファイルを削除しました。 
    
    このアクティビティ パターンはランサムウェア攻撃の可能性を示します。

## <a name="next-steps"></a>次のステップ

高度なマルチステージ攻撃の検出に関する詳細を学習したので、自分のデータや潜在的な脅威を視覚化する方法を学習することができる以下のクイックスタートにも関心を持たれるかもしれません。[Azure Sentinel の概要](quickstart-get-visibility.md)

自分用として作成したインシデントを調査する準備ができたら、次のチュートリアルをご覧ください。[Azure Sentinel でインシデントを調査する](tutorial-investigate-cases.md)

