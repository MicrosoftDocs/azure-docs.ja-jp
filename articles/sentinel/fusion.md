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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724287"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel の高度なマルチステージ攻撃の検出

> [!IMPORTANT]
> 一部の Fusion 検出 (以下でそのように示されているものを参照) は、現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

機械学習を基盤とする Fusion テクノロジを利用することで、Azure Sentinel では、キルチェーンのさまざまな段階で観察される異常な動作と疑わしい行動の組み合わせを特定し、マルチステージ攻撃を自動的に検出することができます。 これらの検出を基に、Azure Sentinel では、Azure Sentinel 以外では検出が困難であろうインシデントが生成されます。 このインシデントは、2 つ以上のアラートまたはアクティビティで構成されています。 設計上、このようなインシデントでは、ボリュームが低、忠実度が高、重大度が高になります。

この検出テクノロジはご利用の環境によってカスタマイズされるため、誤検知率を減らすだけでなく、情報が制限されているか、不足している攻撃も検出できます。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>高度なマルチステージ攻撃の検出の構成

この検出は、Azure Sentinel では既定で有効になっています。 状態を確認するか、複数のアラートに基づくインシデントの作成に代替ソリューションを利用している場合に無効にするには、次の指示に従います。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[Azure Sentinel]** 、 **[構成]** 、 **[分析]** の順に移動します。

1. **[アクティブな規則]** を選択してから、規則の種類 **[Fusion]** でリストをフィルター処理し、 **[名前]** 列で **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** を見つけます。 **[状態]** 列を見て、この検出が有効になっているか、無効になっているか確認します。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. 状態を変更するには、このエントリを選択し、 **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** ブレードで **[編集]** を選択します。

1. **[Rule creation wizard]\(規則作成ウィザード\)** ブレードで、状態の変更が自動的に選択されます。 **[次へ:レビュー]** を選択し、 **[保存]** を選択してください。 

 規則の種類 **[Fusion]** には、変更できない規則が 1 つだけ含まれているため、規則テンプレートはこの規則の種類には適用できません。

> [!NOTE]
> 現在、Azure Sentinel では 30 日間の履歴データを使用して機械学習システムをトレーニングしています。 このデータは、機械学習パイプラインを通過するときに、Microsoft のキーを使用して常に暗号化されます。 ただし、Azure Sentinel ワークスペースで CMK を有効にした場合、トレーニング データは[カスタマー マネージド キー (CMK)](customer-managed-keys.md) を使用して暗号化されません。 Fusion をオプトアウトするには、 **[Azure Sentinel]** \> **[Configuration]** \> **[Analytics] \> [アクティブな規則] \> [Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** に移動し、 **[状態]** 列で **[無効にする]** を選択します。

## <a name="attack-detection-scenarios"></a>攻撃の検出シナリオ

次のセクションに、Azure Sentinel が Fusion テクノロジを使用して検索する、相関関係シナリオの種類を脅威分類別にグループ化して示します。

前述のように、Fusion は、高度なマルチステージ攻撃を検出するためにさまざまな製品からの複数のセキュリティ アラートを関連付けるため、成功した Fusion 検出は、Azure Sentinel の **[インシデント]** ページに **Fusion インシデント** として表示され、 **[ログ]** の **[セキュリティ アラート]** テーブルには **アラート** として表示されません。

これらの Fusion による攻撃検出シナリオを有効にするには、一覧にあるデータ ソースを関連付けられている Azure Sentinel データ コネクタを使用して取り込む必要があります。

> [!NOTE]
> これらのシナリオの一部は **プレビュー** 段階です。 そのように明記されています。

## <a name="compute-resource-abuse"></a>Compute リソース プールの不正使用

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>疑わしい Azure Active Directory サインインの後の複数の VM 作成アクティビティ
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、リソース ハイジャック (T1496)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の VM が作成されたことを示しています。 この種類のアラートは、高い信頼度で、Fusion インシデントの説明に示されているアカウントが侵害され、暗号化マイニング操作の実行など、不正な使用を目的として新しい VM を作成するために使用されたことを示しています。 複数の VM 作成アクティビティ アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動の後の、複数の VM 作成アクティビティ**

- **知らない場所からサインインされた後の、複数の VM 作成アクティビティ**

- **感染したデバイスからサインインされた後の、複数の VM 作成アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、複数の VM 作成アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、複数の VM 作成アクティビティ**

## <a name="credential-harvesting-new-threat-classification"></a>資格情報の収集 (新しい脅威の分類)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>疑わしいサインインの後の悪意のある資格情報盗難ツールの実行

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、OS 資格情報のダンプ (T1003)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender

**説明:** この種類の Fusion インシデントは、疑わしい Azure AD サインインの後に既知の資格情報盗難ツールが実行されたことを示します。 これにより、アラートの説明に記載されているユーザー アカウントが侵害され、システムから資格情報 (キー、プレーンテキスト パスワード、パスワード ハッシュなど) を取得するために、**Mimikatz** などのツールが首尾よく使用された可能性があることが、高い信頼度で示されます。 収集した資格情報を使用して、攻撃者は機密データにアクセスしたり、特権を昇格させたり、ネットワークを横方向に移動したりする可能性があります。 悪意のある資格情報盗難ツール アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動が、悪意のある資格情報盗難ツールの実行につながる**

- **未知の場所からのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **感染したデバイスからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **匿名 IP アドレスからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、悪意のある資格情報盗難ツールの実行につながる**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>疑わしいサインインの後の資格情報盗難アクティビティの疑い

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、パスワード ストアの資格情報 (T1555)、OS 資格情報のダンプ (T1003)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender

**説明:** この種類の Fusion インシデントは、資格情報盗難のパターンに関連付けられたアクティビティが、疑わしい Azure AD サインインの後に発生したことを示します。 これにより、アラートの説明に記載されているユーザー アカウントが侵害され、資格情報 (キー、プレーンテキスト パスワード、パスワード ハッシュなど) を盗むために使用されたことが、高い信頼度で示されます。 盗んだ資格情報を使用して、攻撃者は機密データにアクセスしたり、特権を昇格させたり、ネットワークを横方向に移動したりする可能性があります。 資格情報盗難アクティビティ アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動の後、資格情報盗難アクティビティが疑われる**

- **未知の場所からのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **感染したデバイスからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **匿名 IP アドレスからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

- **資格情報が漏洩したユーザーからのサインイン イベントの後、資格情報盗難アクティビティが疑われる**

## <a name="crypto-mining-new-threat-classification"></a>暗号化マイニング (新しい脅威の分類)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>疑わしいサインインの後の暗号化マイニング アクティビティ

**MITRE ATT&CK の方針:** 初期アクセス、資格情報アクセス

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、リソース ハイジャック (T1496)

**データ コネクタのソース:** Azure Active Directory Identity Protection、Azure Defender (Azure Security Center)

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインに関連付けられた暗号化マイニング アクティビティを示します。 これにより、アラートの説明に記載されているユーザー アカウントが侵害され、環境内のリソースをハイジャックして暗号通貨をマイニングするために使用されたことが、高い信頼度で示されます。 これにより、コンピューティング能力が損なわれたり、クラウドの使用料が予測よりも大幅に高くなるおそれがあります。 暗号化マイニング アクティビティ アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。  

- **特殊な場所へのあり得ない移動が、暗号化マイニング アクティビティにつながる**

- **未知の場所からのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **感染したデバイスからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **匿名 IP アドレスからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、暗号化マイニング アクティビティにつながる**

## <a name="data-exfiltration"></a>データ窃盗

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の Office 365 メールボックスからの情報の流出

**MITRE ATT&CK の方針:** 初期アクセス、流出、収集

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、電子メール収集 (T1114)、自動化された流出 (T1020)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで疑わしい受信トレイの転送ルールが設定されたことを示しています。 この情報は、(Fusion インシデントの説明に記載された) ユーザーのアカウントが侵害されたこと、および実際のユーザーが知らないうちにメールボックス転送ルールを有効にして組織のネットワークからデータを流出させるために使用されたことを示しています。 Office 365 メールボックスからの情報の流出アラートが組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスからの情報の流出**

- **知らない場所からサインインされた後の、Office 365 メールボックスからの情報の流出**

- **感染したデバイスからサインインされた後の、Office 365 メールボックスからの情報の流出**

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 メールボックスからの情報の流出**

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 メールボックスからの情報の流出**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイルのダウンロード

**MITRE ATT&CK の方針:** 初期アクセス、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーによって異常な数のファイルがダウンロードされたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、組織のネットワークからデータを流出させるために使用されたことを示しています。 大量のファイルのダウンロード アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイルのダウンロード**

- **知らない場所からサインインされた後の、大量のファイルのダウンロード**

- **感染したデバイスからサインインされた後の、大量のファイルのダウンロード**

- **送信元のわからない IP からサインインされた後の、大量のファイルのダウンロード**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルのダウンロード**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイル共有

**MITRE ATT&CK の方針:** 初期アクセス、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、特定のしきい値を超える数のファイルが他者と共有されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、ドキュメント、スプレッドシートなどのファイルを悪意のある目的で無許可のユーザーと共有して組織のネットワークからデータを流出させるために使用されたことを示しています。 大量のファイル共有アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイル共有**

- **知らない場所からサインインされた後の、大量のファイル共有**

- **感染したデバイスからサインインされた後の、大量のファイル共有**

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイル共有**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイル共有**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定
このシナリオは、このリストで 2 つの脅威の分類 (**データ流出** と **侵入拡大**) に含まれています。 わかりやすくするために、両方のセクションに記載されています。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで異常な受信トレイ ルールが設定されたことを示しています。 これは、高い信頼度で、Fusion インシデントの説明に示されているアカウントが侵害され、悪意のある目的でユーザーの電子メール受信トレイ ルールを操作するために使用されたことを示しています。 攻撃者が組織のネットワークからデータを流出させようとした可能性があります。 または、攻撃者は、その他のユーザーや特権のあるアカウントへのアクセスを取得し、(外部ソースからのメールを対象とするフィッシング検出メカニズムを迂回して) 組織内から侵入拡大目的でフィッシング メールを生成しようとしたと考えられます。 疑わしい受信トレイ操作ルール アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、疑わしい受信トレイ操作ルール**

- **知らない場所からサインインされた後の、疑わしい受信トレイ操作ルール**

- **感染したデバイスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい受信トレイ操作ルール**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の複数の Power BI レポート共有アクティビティ 
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、流出 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の Power BI レポートが共有されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、Power BI レポートを悪意のある目的で無許可のユーザーと共有して組織のネットワークからデータを流出させるために使用されたことを示しています。 複数の Power BI レポート共有アクティビティと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動の後の、複数の Power BI レポート共有アクティビティ**

- **知らない場所からサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **感染したデバイスからサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、複数の Power BI レポート共有アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、複数の Power BI レポート共有アクティビティ**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい Power BI レポート共有
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、流出 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、Web サービス経由の流出 (T1567)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、疑わしい Power BI レポート共有アクティビティが発生したことを示しています。 Power BI レポートには自然言語処理を使用して識別された機密情報が含まれていたため、および外部の電子メール アドレスで共有された、Web に公開された、または外部で登録されている電子メール アドレスにスナップショットとして配信されたため、共有アクティビティは疑わしいものとして識別されました。 このアラートは、Fusion インシデントの説明に記載されたアカウントが侵害され、Power BI レポートを悪意のある目的で無許可のユーザーと共有して組織から機密データを流出させるために使用されたことを示しています。 疑わしい Power BI レポート共有と組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動の後の、疑わしい Power BI レポート共有**

- **知らない場所からサインインされた後の、疑わしい Power BI レポート共有**

- **感染したデバイスからサインインされた後の、疑わしい Power BI レポート共有**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい Power BI レポート共有**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい Power BI レポート共有**

## <a name="data-destruction"></a>データの破壊

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の大量のファイル削除

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、データの破棄 (T1485)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、異常な数の固有なファイルが削除されたことを示しています。 これは、Fusion インシデントの説明に示されているアカウントが侵害され、悪意のある目的でデータを破壊するために使用されたことを示しています。 大量のファイル削除アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、大量のファイルの削除**

- **知らない場所からサインインされた後の、大量のファイルの削除された**

- **感染したデバイスからサインインされた後の、大量のファイルの削除**

- **送信元のわからない IP アドレスからサインインされた後の、大量のファイルの削除**

- **資格情報が漏洩したユーザーからサインインされた後の、大量のファイルの削除**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい電子メール削除アクティビティ
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響 

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、データの破棄 (T1485)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の電子メールが削除されたことを示しています。 これは、Fusion インシデントの説明に示されているアカウントが侵害され、組織に被害をもたらす、スパム関連の電子メール アクティビティを隠すなど、悪意のある目的でデータを破壊するために使用されたことを示しています。 疑わしい電子メール削除アクティビティ アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。   

- **特殊な場所へのあり得ない移動の後の、疑わしい電子メール削除アクティビティ**

- **知らない場所からサインインされた後の、疑わしい電子メール削除アクティビティ**

- **感染したデバイスからサインインされた後の、疑わしい電子メール削除アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい電子メール削除アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい電子メール削除アクティビティ**

## <a name="denial-of-service"></a>サービス拒否

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の複数の VM 削除アクティビティ
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、エンドポイントのサービス拒否 (T1499)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、1 つのセッションで異常な数の VM が削除されたことを示しています。 この情報は、Fusion インシデントの説明に記載されたアカウントが侵害され、組織のクラウド環境を中断または破壊しようとするために使用されたことを示しています。 複数の VM 削除アクティビティ アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動の後の、複数の VM 削除アクティビティ**

- **知らない場所からサインインされた後の、複数の VM 削除アクティビティ**

- **感染したデバイスからサインインされた後の、複数の VM 削除アクティビティ**

- **送信元のわからない IP アドレスからサインインされた後の、複数の VM 削除アクティビティ**

- **資格情報が漏洩したユーザーからサインインされた後の、複数の VM 削除アクティビティ**

## <a name="lateral-movement"></a>侵入拡大

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の Office 365 偽装

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントからの疑わしいサインインの後、異常な数の偽装アクションが発生したことを示しています。 一部のソフトウェアでは、ユーザーが他のユーザーを偽装できるオプションがあります。 たとえば、メール サービスを使用すると、ユーザーは自分に代わって他のユーザーがメールを送信することを承認できます。 このアラートは、Fusion インシデントの説明に記載されているアカウントが侵害され、マルウェア配布や侵入拡大のためにフィッシング メールを送信するなど、悪意のある目的で偽装アクティビティを実行するために使用されたことを、高い信頼度で示しています。 Office 365 での偽装アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、Office 365 メールボックスの偽装**

- **知らない場所からサインインされた後の、Office 365 での偽装**

- **感染したデバイスからサインインされた後の、Office 365 での偽装**

- **送信元のわからない IP アドレスからサインインされた後の、Office 365 での偽装**

- **資格情報が漏洩したユーザーからサインインされた後の、Office 365 での偽装**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後の疑わしい受信トレイ操作ルールの設定
このシナリオは、このリストで 2 つの脅威の分類 (**侵入拡大** と **データ流出**) に含まれています。 わかりやすくするために、両方のセクションに記載されています。

現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、侵入拡大、流出

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、内部のスピアフィッシング (T1534)、自動化された流出 (T1020)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ユーザーの受信トレイで異常な受信トレイ ルールが設定されたことを示しています。 この情報は、高い信頼度で、Fusion インシデントの説明に示されているアカウントが侵害され、悪意のある目的でユーザーの電子メール受信トレイ ルールを操作するために使用されたことを示しています。 攻撃者が組織のネットワークからデータを流出させようとした可能性があります。 または、攻撃者は、その他のユーザーや特権のあるアカウントへのアクセスを取得し、(外部ソースからのメールを対象とするフィッシング検出メカニズムを迂回して) 組織内から侵入拡大目的でフィッシング メールを生成しようとしたと考えられます。 疑わしい受信トレイ操作ルール アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。

- **特殊な場所へのあり得ない移動後の、疑わしい受信トレイ操作ルール**

- **知らない場所からサインインされた後の、疑わしい受信トレイ操作ルール**

- **感染したデバイスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **送信元のわからない IP アドレスからサインインされた後の、疑わしい受信トレイ操作ルール**

- **資格情報が漏洩したユーザーからサインインされた後の、疑わしい受信トレイ操作ルール**

## <a name="malicious-administrative-activity"></a>悪意のある管理アクティビティ

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後のクラウド アプリでの疑わしい管理行為

**MITRE ATT&CK の方針:** 初期アクセス、永続性、防御侵入、侵入拡大、収集、流出、影響

**MITRE ATT&CK の手法:** 該当なし

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、同じアカウントからの疑わしい Azure AD サインインの後、1 つのセッションで異常な数の管理行為が実行されたことを示しています。 これは、Fusion インシデントの説明に示されているアカウントが侵害され、任意の数の未許可の管理行為を悪意を持って行うために使用されたことを示しています。 これは、管理特権を持つアカウントが侵害された可能性があることも示しています。 クラウド アプリでの疑わしい管理行為アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、クラウド アプリでの疑わしい管理行為**

- **知らない場所からサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **感染したデバイスからサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリでの疑わしい管理行為**

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリでの疑わしい管理行為**

## <a name="malicious-execution-with-legitimate-process"></a>正当なプロセスによる悪意のある実行

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell で疑わしいネットワーク接続が行われた後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 実行

**MITRE ATT&CK の手法:** コマンドおよびスクリプト インタープリター (T1059)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 Microsoft Defender Advanced Threat Protection または MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、PowerShell コマンドを使用して送信接続要求が行われ、その後、Palo Alto Networks ファイアウォールによって異常な受信アクティビティが検出されたことを示しています。 これは、攻撃者がネットワークへのアクセスを取得した可能性があり、悪意のあるアクションを実行しようとしていることを示しています。 このパターンに従う PowerShell による接続の試行は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 すべての "現地調達型" 攻撃と同様、このアクティビティは PowerShell を正当に使用している可能性があります。 ただし、PowerShell コマンドの実行後に疑わしい受信ファイアウォール アクティビティが続く場合、より高い信頼度で PowerShell が悪意のある方法で使用されていることを示しており、さらに詳しく調査する必要があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>疑わしいリモート WMI 実行後の、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 実行、検出

**MITRE ATT&CK の手法:** Windows Management Instrumentation (T1047)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、Windows Management Interface (WMI) コマンドがシステムでリモート実行され、その後、疑わしい受信アクティビティが Palo Alto Networks ファイアウォールによって検出されたことを示しています。 これは、攻撃者がネットワークへのアクセスを取得した可能性があり、侵入拡大、特権のエスカレーション、または悪意のあるペイロードの実行を試みていることを示しています。 すべての "現地調達型" 攻撃と同様、このアクティビティは WMI を正当に使用している可能性があります。 ただし、リモート WMI コマンドの実行後に疑わしい受信ファイアウォール アクティビティが続く場合、より高い信頼度で WMI が悪意のある方法で使用されていることを示しており、さらに詳しく調査する必要があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>疑わしいサインインの後の疑わしい PowerShell コマンド ライン

**MITRE ATT&CK の方針:** 初期アクセス、実行

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、コマンドおよびスクリプト インタープリター (T1059)

**データ コネクタのソース:** Azure Active Directory Identity Protection、エンドポイント用 Microsoft Defender (旧称 MDATP)

**説明:** この種類の Fusion インシデントは、ユーザーが Azure AD アカウントへの疑わしいサインインの後に悪意のある可能性のある PowerShell コマンドを実行したことを示します。 これにより、アラートの説明に記載されているアカウントが侵害され、さらに悪意のあるアクションが行われたことが、高い信頼度で示されます。 多くの場合、攻撃者は PowerShell を利用して、ウイルス検索プログラムなどのディスクベースのセキュリティ メカニズムによる検出を避けるために、ディスク上にアーティファクトを残さずに、メモリ内で悪意のあるペイロードを実行します。 疑わしい PowerShell コマンド アラートと組み合わされた、疑わしい Azure AD サインイン アラートの順列は次のとおりです。

- **特殊な場所へのあり得ない移動が、疑わしい PowerShell コマンド ラインにつながる**

- **未知の場所からのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **感染したデバイスからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **匿名 IP アドレスからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

- **資格情報が漏洩したユーザーからのサインイン イベントが、疑わしい PowerShell コマンド ラインにつながる**

## <a name="malware-c2-or-download"></a>マルウェア C2 またはダウンロード

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名化サービスに対するネットワーク要求の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続きます。
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** コマンドと制御

**MITRE ATT&CK の手法:** 暗号化されたチャネル (T1573)、プロキシ (T1090)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、TOR 匿名化サービスへの送信接続要求が行われ、その後、Palo Alto Networks ファイアウォールによって異常な受信アクティビティが検出されたことを示しています。 これは、攻撃者がネットワークへのアクセスを取得した可能性があり、アクションと意図を隠そうとしていることを示しています。 このパターンに従う TOR ネットワークへの接続は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>承認されていないアクセス試行の履歴がある IP への送信接続の後に、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィックが続く
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** コマンドと制御

**MITRE ATT&CK の手法:** 適用なし

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、未許可のアクセス試行履歴がある IP アドレスへの送信接続が確立され、その後、Palo Alto Networks ファイアウォールによって異常なアクティビティが検出されたことを示しています。 これは、攻撃者がネットワークへのアクセスを取得した可能性があることを示しています。 このパターンに従う接続の試行は、マルウェアのコマンド アンド コントロール アクティビティ、追加のマルウェアのダウンロード要求、またはリモートの対話型アクセスを確立する攻撃者を示している可能性があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

## <a name="ransomware"></a>ランサムウェア

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>疑わしい Azure AD サインインの後のランサムウェア実行

**MITRE ATT&CK の方針:** 初期アクセス、影響

**MITRE ATT&CK の手法:** 有効なアカウント (T1078)、影響を及ぼすためのデータの暗号化 (T1486)

**データ コネクタのソース:** Microsoft Cloud App Security、Azure Active Directory Identity Protection

**説明:** この種類の Fusion インシデントは、Azure AD アカウントへの疑わしいサインインの後、ランサムウェア攻撃を示す異常なユーザー ビヘイビアーが検出されたことを示しています。 この情報は、Fusion インシデントの説明に示されているアカウントが侵害され、データ所有者を脅迫する、またはデータ所有者によるデータへのアクセスを拒否する目的でデータを暗号化するために使用されたことを示しています。 ランサムウェア実行アラートと組み合わされた疑わしい Azure AD サインイン アラートは次のとおりです。  

- **特殊な場所へのあり得ない移動後の、クラウド アプリ内のランサムウェア**

- **知らない場所からサインインされた後の、クラウド アプリ内のランサムウェア**

- **感染したデバイスからサインインされた後の、クラウド アプリ内のランサムウェア**

- **送信元のわからない IP アドレスからサインインされた後の、クラウド アプリ内のランサムウェア**

- **資格情報が漏洩したユーザーからサインインされた後の、クラウド アプリ内のランサムウェア**

## <a name="remote-exploitation"></a>リモートの悪用

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>疑わしい攻撃フレームワーク使用後の、Palo Alto Networks ファイアウォールによってフラグが設定された異常なトラフィック
現在、このシナリオは **プレビュー** 段階です。

**MITRE ATT&CK の方針:** 初期アクセス、実行、侵入拡大、特権エスカレーション

**MITRE ATT&CK の手法:** 公開アプリケーションの悪用 (T1190)、クライアント実行の悪用 (T1203)、リモート サービスの悪用 (T1210)、特権エスカレーションの悪用 (T1068)

**データ コネクタのソース:** Microsoft Defender for Endpoint (旧称 MDATP)、Palo Alto Networks 

**説明:** この種類の Fusion インシデントは、Metasploit などの攻撃フレームワークの使用に似た、プロトコルの非標準の使用が検出され、その後、疑わしい受信アクティビティが Palo Alto Networks ファイアウォールによって検出されたことを示しています。 これは、攻撃者がネットワーク リソースへのアクセスを取得するためにサービスを悪用したこと、または攻撃者が既にアクセスを取得していて、利用可能なシステム/サービスをさらに悪用して侵入拡大や特権エスカレーションを行おうとしていることを示す最初の兆候の場合があります。 Palo Alto ログでは、Azure Sentinel は[脅威ログ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)に焦点を当てており、脅威 (疑わしいデータ、ファイル、フラッド、パケット、スキャン、スパイウェア、URL、ウイルス、脆弱性、wildfire ウイルス、wildfire) が許可されている場合、トラフィックは疑わしいと見なされます。 アラートのその他の詳細について、Fusion インシデントの説明に記載されている[脅威/コンテンツ タイプ](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html)に対応する Palo Alto 脅威ログも参照してください。

## <a name="next-steps"></a>次のステップ

高度なマルチステージ攻撃の検出に関する詳細を学習したので、自分のデータや潜在的な脅威を視覚化する方法を学習することができる以下のクイックスタートにも関心を持たれるかもしれません。[Azure Sentinel の概要](quickstart-get-visibility.md)

自分用として作成したインシデントを調査する準備ができたら、次のチュートリアルをご覧ください。[Azure Sentinel でインシデントを調査する](tutorial-investigate-cases.md)

