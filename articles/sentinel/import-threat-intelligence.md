---
title: Azure Sentinel への脅威インテリジェンスのインポート | Microsoft Docs
description: Azure Sentinel の脅威インテリジェンス フィードを使用すると、データの分析、脅威の検出、アラートとインシデントの生成を行うことができます。 ブックを使用して脅威インテリジェンス情報を視覚化します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574999"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Azure Sentinel への脅威インテリジェンスのインポート

## <a name="introduction-to-threat-intelligence"></a>脅威インテリジェンスの概要

サイバー脅威インテリジェンス (CTI) は、システムとユーザーに対する既存または潜在的な脅威について説明する情報です。 この種の情報には、特定の脅威アクターの動機、インフラストラクチャ、手法を詳細に説明したレポートから、サイバーの脅威に関連する IP アドレス、ドメイン、ファイル ハッシュに関する具体的な観測結果まで、さまざまな形式があります。 CTI は、通常とは異なるアクティビティに重要なコンテキストを提供するために、組織によって使用されます。これにより、セキュリティ担当者は、ユーザーと資産を保護する措置を迅速に講じることができます。 CTI は、オープンソースのデータ フィード、脅威インテリジェンス共有コミュニティ、商用インテリジェンス フィード、組織内のセキュリティ調査の過程で集められたローカル インテリジェンスなど、多くのソースから取得できます。

Azure Sentinel のようなセキュリティ情報イベント管理 (SIEM) ソリューションで最もよく使用される CTI の形式は、一般にセキュリティ侵害のインジケーター (IoC) と呼ばれる脅威インジケーターです。 脅威インジケーターは、URL、ファイル ハッシュ、IP アドレスなどの観測結果を、フィッシング、ボットネット、マルウェアなどの既知の脅威アクティビティに関連付けるデータです。 この形式の脅威インテリジェンスは、組織に対する潜在的な脅威を防止および検出するために、セキュリティ製品と自動化に大規模に適用できるため、戦術的脅威インテリジェンスと呼ばれることがよくあります。 Azure Sentinel では、脅威インジケーターを使用して、お使いの環境で観察された悪意のあるアクティビティを検出し、セキュリティ調査担当者にコンテキストを提供して、応答の決定に役立てることができます。

次のアクティビティを通して脅威インテリジェンス (TI) を Azure Sentinel に統合できます。

- さまざまな TI プラットフォームへの **データ コネクタ** を使用して、Azure Sentinel に [脅威インテリジェンスをインポート](./connect-threat-intelligence.md)します。
- インポートした脅威インテリジェンスを Azure Sentinel の **[ログ]** および新しい **[脅威インテリジェンス]** の領域に表示し、管理します。
- 組み込みの **分析** ルール テンプレートを使用して、インポートした脅威インテリジェンスを利用したセキュリティのアラートとインシデントを生成します。
- Azure Sentinel で、**脅威インテリジェンス ブック** を使用して、脅威インテリジェンスに関する重要な情報を視覚化します。

また、脅威インテリジェンスによって、 **[ハンティング]** や **[ノートブック]** などの他の Azure Sentinel エクスペリエンスにも有用なコンテキストが提供されます。これらのエクスペリエンスについては、この記事では取り上げていませんが、Notebooks 内での CTI の使用について書かれた、[Azure Sentinel での Jupyter Notebooks に関する Ian Hellen によるこちらの優れたブログ記事](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)で取り上げられています。

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>脅威インテリジェンス用の Azure Sentinel データ コネクタ

Azure Sentinel の他のすべてのイベント データと同様に、脅威インジケーターはデータ コネクタを使用してインポートされます。 Azure Sentinel には、脅威インジケーター用に特別に用意された 2 つのデータ コネクタがあります。**脅威インテリジェンス - TAXII** と **脅威インテリジェンス プラットフォーム** です。 組織の脅威インジケーターの取得元となる場所に応じて、どちらかのデータ コネクタを単独で使用することも、両方のコネクタを一緒に使用することもできます。 各データ コネクタについて説明します。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>脅威インテリジェンス プラットフォーム データ コネクタを使用して Azure Sentinel に脅威インジケーターを追加する

多くの組織では、脅威インテリジェンス プラットフォーム (TIP) ソリューションを利用してさまざまなソースからの脅威インジケーター フィードを集約し、プラットフォーム内のデータをキュレーションしたうえで、ネットワーク デバイス、高度な脅威保護ソリューション、Azure Sentinel のような SIEM など、さまざまなセキュリティ ソリューションに適用する脅威インジケーターを選択します。 MISP、Anomali ThreatStream、ThreatConnect、EclecticIQ Platform、ThreatQ Threat Intelligence Platform、Palo Alto Networks の MineMeld などの統合された TIP ソリューションを組織で利用している場合は、**脅威インテリジェンス プラットフォーム データ コネクタ** でその TIP を使用して、Azure Sentinel に脅威インジケーターをインポートできます。 これはコネクタと [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) の連携によって実現されるため、任意のカスタム脅威インテリジェンス プラットフォームでコネクタを使用し、tiIndicators API を利用して Azure Sentinel (および Defender ATP などの他の Microsoft セキュリティ ソリューション) にインジケーターを送信することもできます。

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="脅威インテリジェンスのインポート パス":::

統合された TIP またはカスタム脅威インテリジェンス ソリューションから Azure Sentinel に脅威インジケーターをインポートするには、次の手順に従います。

1. Azure Active Directory からアプリケーション ID とクライアント シークレットを取得する

1. この情報を TIP ソリューションまたはカスタム アプリケーションに入力する

1. Azure Sentinel で脅威インテリジェンス プラットフォーム データ コネクタを有効にする

これらの各手順の詳細は以下のとおりです。

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory からアプリケーション ID とクライアント シークレットを取得する

TIP を使用しているか、カスタム ソリューションを使用しているかにかかわらず、tiIndicators API では、脅威インジケーターの接続と送信に関する基本的な情報が必要になります。 次の 3 つの情報を取得する必要があります。
- アプリケーション (クライアント) ID
- ディレクトリ (テナント) ID
- クライアント シークレット

この情報は、常に、次の 3 つの手順を含む **アプリの登録** と呼ばれるプロセスを通じて Azure Active Directory から取得されます。
- Azure Active Directory にアプリを登録する
- アプリから Microsoft Graph tiIndicators API に接続して脅威インジケーターを送信するために必要な、アクセス許可を指定する
- これらのアクセス許可をこのアプリケーションに付与するために、組織から同意を得る  

**アプリケーションを Azure Active Directory に登録する**

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Active Directory** サービスに移動します。

1. メニューから **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。

1. アプリケーションの登録を表す名前を選択し、**シングル テナント** のラジオ ボタンを選択して、 **[登録]** を選択します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="アプリケーションを登録する":::

1. 表示された画面で、 **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** の値をコピーします。 これらは、Azure Sentinel に脅威インジケーターを送信する TIP またはカスタム ソリューションを構成するために後で必要になる情報の最初の 2 つです。

**アプリケーションに必要なアクセス許可を指定する**

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Active Directory** サービスに移動します。

1. メニューから **[アプリの登録]** を選択し、新しく登録したアプリを選択します。

1. メニューから **[API のアクセス許可]** を選択し、 **[アクセス許可の追加]** ボタンをクリックします。

1. **[API を選択します]** ページで、Microsoft Graph のアクセス許可の一覧から選択して **[Microsoft Graph]** を選びます。

1. **[アプリケーションに必要なアクセス許可の種類]** の選択を求められたら、 **[アプリケーションのアクセス許可]** を選択します。 これは、アプリ ID とアプリ シークレット (API キー) での認証を行うアプリケーションで使用されるアクセス許可の種類です。

1. **[ThreatIndicators.ReadWrite.OwnedBy]** を選択し、 **[アクセス許可の追加]** を選択して、このアクセス許可をアプリのアクセス許可の一覧に追加します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="アクセス許可を指定する":::

**これらのアクセス許可を付与するために組織から同意を得る**

1. アプリの [API のアクセス許可] ページで同意を与えるために、Azure Active Directory のグローバル管理者が **[<テナント名> に管理者の同意を与えます]** ボタンを選択する必要があります。 お使いのアカウントでグローバル管理者の役割をお持ちでない場合、このボタンは使用できず、この手順を実行するには、組織の全体管理者に依頼する必要があります。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="同意する":::

1. アプリに同意が与えられると、 **[状態]** に緑色のチェックマークが表示されます。
 
アプリが登録され、アクセス許可が付与されたので、リストの最後のもの (アプリのクライアント シークレット) を取得できます。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Active Directory** サービスに移動します。

1. メニューから **[アプリの登録]** を選択し、新しく登録したアプリを選択します。

1. メニューから **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** ボタンをクリックして、アプリのシークレット (API キー) を取得します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="クライアント シークレットの取得":::

1. **[追加]** ボタンをクリックし、**必ずクライアント シークレットをコピー** してください。このページから移動すると、このシークレットを再度取得することができないためです。 TIP またはカスタム ソリューションを構成するときに、この値が必要になります。

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>この情報を TIP ソリューションまたはカスタム アプリケーションに入力する

以上で、Azure Sentinel に脅威インジケーターを送信するための TIP またはカスタム ソリューションの構成に必要な 3 つの情報がすべて揃いました。 
- アプリケーション (クライアント) ID
- ディレクトリ (テナント) ID
- クライアント シークレット

統合された TIP またはカスタム ソリューションの構成に必要に応じてこれらの値を入力すると、Azure Sentinel を対象にした Microsoft Graph tiIndicators API を介して脅威インジケーターが送信されます。

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Azure Sentinel で脅威インテリジェンス プラットフォーム データ コネクタを有効にする

統合プロセスの最後の手順は、Azure Sentinel で **脅威インテリジェンス プラットフォーム** データ コネクタを有効にすることです。 これは、TIP またはカスタム ソリューションから Microsoft Graph tiIndicators API を使用して送信された脅威インジケーターを Azure Sentinel にインポートする手順です。 これらのインジケーターは、組織のすべての Azure Sentinel ワークスペースで使用できます。 各ワークスペースについて、次の手順に従って脅威インテリジェンス プラットフォーム データ コネクタを有効にします。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. TIP またはカスタム ソリューションから送信された脅威インジケーターのインポート先となる **ワークスペース** を選択します。

1. メニューから **[データ コネクタ]** を選択し、コネクタ ギャラリーから **[脅威インテリジェンス プラットフォーム]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. アプリの登録を既に完了し、脅威インジケーターを送信するように TIP またはカスタム ソリューションを構成したので、残りの手順は、 **[接続]** ボタンをクリックすることだけです。

数分以内に、脅威インジケーターが Azure Sentinel ワークスペースに送られるようになります。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>脅威インテリジェンス - TAXII データ コネクタを使用して Azure Sentinel に脅威インジケーターを追加する

脅威インテリジェンスの送信用に最も広く採用されている業界標準は、[STIX データ形式と TAXII プロトコルの組み合わせ](https://oasis-open.github.io/cti-documentation/)です。 組織が現在の STIX/TAXII バージョン (2.0 または 2.1) をサポートするソリューションから脅威インジケーターを取得している場合、**脅威インテリジェンス - TAXII** データ コネクタを使用して、脅威インジケーターを Azure Sentinel に取り込むことができます。 脅威インテリジェンス - TAXII データ コネクタを使用すると、Azure Sentinel に組み込みの TAXII クライアントで、TAXII 2.x サーバーから脅威インテリジェンスをインポートできます。

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII のインポート パス":::
 
TAXII サーバーから Azure Sentinel に STIX 形式の脅威インジケーターをインポートするには、次の手順に従います。

1. TAXII サーバー API ルートとコレクション ID を取得する

1. Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする

次に、これらの各手順について詳しく説明します。

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>TAXII サーバー API ルートとコレクション ID を取得する

TAXII 2.x サーバーによって、脅威インテリジェンスのコレクションをホストする URL である API ルートが公開されています。 ほとんどの場合、API ルートは、TAXII サーバーをホストしている脅威インテリジェンス プロバイダーのドキュメント ページ経由で取得できます。 ただし、公開される情報が、探索エンドポイントと呼ばれる URL だけである場合もあります。 その場合、探索エンドポイントを使用して API ルートを簡単に見つけることができます。 使用する TAXII サーバーの API ルートとコレクション ID が既にわかっている場合は、次のセクション「**Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする**」に進んでください。

[cURL](https://en.wikipedia.org/wiki/CURL) と呼ばれるシンプルなコマンド ライン ユーティリティの使用方法を実際の例で見てみましょう。これは Windows およびほとんどの Linux ディストリビューションで提供されており、API ルートを検出し、探索エンドポイントからのみ開始される TAXII サーバーのコレクションを参照します。 この例では、[Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 サーバーの探索エンドポイントを使用します。

1.  ブラウザーで [ThreatStream TAXII 2.0 サーバー探索エンドポイント](https://limo.anomali.com/taxii)に移動し、API ルートを取得します。 `guest` というユーザーとパスワードを使用して認証します。

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  cURL ユーティリティと前の手順で取得した API ルート (https://limo.anomali.com/api/v1/taxii2/feeds/) を使用して、API ルートでホストされているコレクション ID の一覧を参照します。

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

これで、Anomali Limo によって提供される 1 つ以上の TAXII サーバー コレクションに Azure Sentinel を接続するために必要なすべての情報が得られました。

| **API ルート** (https://limo.anomali.com/api/v1/taxii2/feeds/) | コレクション ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする

TAXII サーバーから Azure Sentinel に脅威インジケーターをインポートするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. TAXII サーバーからの脅威インジケーターのインポート先となる **ワークスペース** を選択します。

1. メニューから **[データ コネクタ]** を選択し、コネクタ ギャラリーから **[脅威インテリジェンス - TAXII]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. この TAXII サーバー コレクションの **名前** を入力し、 **[API ルート URL]** 、 **[コレクション ID]** 、 **[ユーザー名]** (必要な場合)、 **[パスワード]** (必要な場合) に入力して、 **[追加]** ボタンをクリックします。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXII サーバーを構成する":::
 
TAXII サーバーへの接続が正常に確立されたことを示す確認メッセージが表示され、同じまたは異なる TAXII サーバーから複数のコレクションに接続するために必要な回数だけ上記の手順 (4) を繰り返すことができます。

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Azure Sentinel で脅威インジケーターを表示する

**脅威インテリジェンス プラットフォーム** または **脅威インテリジェンス - TAXII** データ コネクタを使用して Azure Sentinel に脅威インジケーターをインポートしたので、すべての Azure Sentinel イベント データが格納されている、 **[ログ]** の **ThreatIntelligenceIndicator** テーブルに、それらを表示できます。 このテーブルは、他の Azure Sentinel 機能 (分析やブックなど) によって実行されるクエリの基礎となります。 **ThreatIntelligenceIndicator** テーブルで脅威インジケーターを検索して表示する方法を次に示します。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して脅威インジケーターをインポートした **ワークスペース** を選択します。

1. Azure Sentinel メニューの **[全般]** セクションで **[ログ]** を選択します。

1. **ThreatIntelligenceIndicator** テーブルは、**Azure Sentinel** グループの下にあります。

1. テーブル名の横にある **[データのプレビュー]** アイコン (目のアイコン) を選択し、 **[クエリ エディターで表示]** ボタンを選択して、このテーブルのレコードを表示するクエリを実行します。

結果は、次に示す脅威インジケーターの例のようになります。

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="サンプル クエリ データ":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Azure Sentinel の新しい脅威インテリジェンス領域で脅威インジケーターを管理する

Azure Sentinel メニューからアクセスできる新しい **[脅威インテリジェンス]** 領域を使用すると、**ログ** クエリを記述することなく、インポートした脅威インジケーターの表示、並べ替え、フィルター処理、検索を行うこともできます。 また、この新しい領域では、Azure Sentinel インターフェイス上で直接脅威インジケーターを作成することや、インジケーターのタグ付けやセキュリティ調査に関連した新しいインジケーターの作成などの一般的な脅威インテリジェンス管理タスクを実行することもできます。
最も一般的なタスクのうちの 2 つを見てみましょう。新しい脅威インジケーターの作成と、グループ化と参照を簡単にするためのインジケーターのタグ付けです。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して脅威インジケーターをインポートした **ワークスペース** を選択します。

1. Azure Sentinel メニューの [脅威管理] セクションで **[脅威インテリジェンス]** を選択します。

1. ページの上部のメニューから **[新規追加]** ボタンを選択します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="新しい脅威インジケーターの追加" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. インジケーターの種類を選択し、 **[新しいインジケーター]** パネルで、赤いアスタリスク (*) が付いている必須フィールドに入力します。

1. **[適用]** を選択します。 インジケーターがインジケーター グリッドに追加され、 **[ログ]** の ThreatIntelligenceIndicator テーブルにも送信されます。

タグ付けは、脅威インジケーターをグループ化して見つけやすくする簡単な方法です。 通常は、特定のインシデントに関連するインジケーター、または特定の既知のアクターや既知の攻撃キャンペーンからの脅威を表すインジケーターにタグを適用することができます。 脅威インジケーターは個別にタグ付けすることも、インジケーターを複数選択してすべて一度にタグ付けすることもできます。 複数のインジケーターにインシデント ID をタグ付けする例を次に示します。 タグ付けは自由形式であるため、脅威インジケーター タグの標準的な名前付け規則を作成することをお勧めします。 各インジケーターに複数のタグを適用できます。

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="脅威インジケーターにタグを適用する" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>分析によって脅威インジケーターでの潜在的な脅威の検出を機能させる

脅威インジケーターが Azure Sentinel に供給されるようになりました。それらを表示して管理する方法を確認しました。次に、それらで何ができるかを確認します。 Azure Sentinel のような SIEM ソリューションでの脅威インジケーターの最も重要なユース ケースは、分析ルールの強化です。  このようなインジケーターベースのルールでは、データ ソースで発生した未加工のイベントが脅威インジケーターと比較され、組織内のセキュリティ上の脅威が検出されます。 Azure Sentinel の **[分析]** で、スケジュールに従って実行されてセキュリティ アラートを生成する分析ルールを作成します。 ルールは、クエリと、ルールを実行する頻度やセキュリティ アラートを生成するクエリ結果の種類、アラートが生成されたときにトリガーされる自動応答を決定する構成によって駆動されます。

新しい分析ルールを最初から作成することは常に可能ですが、Azure Sentinel には、Microsoft セキュリティ エンジニアが作成した組み込みのルール テンプレートのセットが用意されていて、それらをそのまま使用することも、ニーズに合わせて変更することもできます。 脅威インジケーターを使用するルール テンプレートは、すべて "**TI map**..." (TI で ... にマップする) で始まるタイトルが付いているため、簡単に特定できます。 これらのルール テンプレートはすべて同じように動作しますが、使用される脅威インジケーターの種類 (ドメイン、電子メール、ファイル ハッシュ、IP アドレス、または URL) と照合するイベントの種類が異なります。 各テンプレートには、ルールが機能するために必要なデータ ソースが一覧表示されます。そのため、必要なイベントが Azure Sentinel に既にインポートされているかどうかを一目で確認できます。

これらのルール テンプレートの 1 つを示して、Azure Sentinel にインポートした脅威インジケーターを使用したセキュリティ アラートの生成ルールを有効にして構成する方法を説明します。 この例では、 **[TI map IP entity to AzureActivity]\(TI で IP エンティティを AzureActivity にマップする\)** というルール テンプレートを使用します。 このルールで、あらゆる IP アドレスの種類の脅威インジケーターとすべての Azure アクティビティ イベントを照合します。 一致が見つかると、**アラート** と、対応する **インシデント** が生成され、セキュリティ運用チームによる調査を行えるようになります。 この分析ルールは、**脅威インテリジェンス** データ コネクタ (脅威インジケーターをインポートする) の一方または両方と、**Azure アクティビティ** データ コネクタ (Azure サブスクリプション レベルのイベントをインポートする) を有効にした場合にのみ、正常に機能します。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. **脅威インテリジェンス** データ コネクタを使用して脅威インジケーターをインポートした先、および **Azure アクティビティ** データ コネクタを使用して Azure アクティビティ データをインポートした先の **ワークスペース** を選択します。

1. Azure Sentinel メニューの **[構成]** セクションで **[分析]** を選択します。

1. **[規則のテンプレート]** タブを選択して、使用可能な分析ルール テンプレートの一覧を表示します。

1. 次に示すように、 **[TI map IP entity to AzureActivity]\(TI で IP エンティティを AzureActivity にマップする\)** というタイトルの付いたルールに移動し、必要なすべてのデータ ソースを接続済みであることを確認します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="必要なデータ ソース":::

1. このルールを選択し、 **[ルールの作成]** ボタンを選択します。 これにより、ルールを構成するためのウィザードが開きます。 ここで設定を完了し、 **[次: ルール ロジックを設定]** ボタンを選択します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="分析ルールの設定":::

1. ウィザードのルール ロジック部分には次のものが含まれます。
    - ルールで使用されるクエリ。

    - エンティティ マッピング。これはアカウント、IP アドレス、URL などのエンティティの認識方法を Azure Sentinel に指示するもので、これにより、**インシデント** および **調査** において、このルールによって生成されたセキュリティ アラートでのデータ処理方法が理解されるようになります。

    - このルールを実行するスケジュール。

    - セキュリティ アラートが生成されるまでに必要なクエリ結果の数。

    テンプレートの既定の設定は次のとおりです。
    - 1 時間に 1 回実行します。

    - **ThreatIntelligenceIndicator** テーブルのすべての IP アドレス脅威インジケーターを、**AzureActivity** テーブルにある過去 1 時間のイベントで見つかったすべての IP アドレスと照合します。

    - クエリ結果が 0 件より大きい (一致が見つかったことを意味する) 場合に、セキュリティ アラートを生成します。

既定の設定をそのまま使用することも、要件に合わせて変更することもできます。 完了したら、 **[次: 自動応答 >]** を選択します。

1. ウィザードのこの手順では、この分析ルールからセキュリティ アラートが生成されたときにトリガーする自動化を構成できます。 Azure Sentinel の自動化は、Azure Logic Apps によって提供される **プレイブック** を使用して行われます。 詳細については、こちらの「[チュートリアル: Azure Sentinel で脅威への自動対応を設定する](./tutorial-respond-threats-playbook.md)」を参照してください。 この例では、 **[次: レビュー]** ボタンを選択して続行します。

1. この最後の手順でルールの設定を検証します。 ルールを有効にする準備ができたら、 **[作成]** ボタンを選択して完了します。

分析ルールを有効にしたので、Azure Sentinel の **[分析]** セクションにある **[アクティブな規則]** タブで、有効になっているルールを確認できます。 そこからアクティブなルールの編集、有効化、無効化、複製、または削除を行うことができます。 新しいルールはアクティブ化すると直ちに実行され、それ以降は定義したスケジュールで実行されます。

既定の設定に従うと、ルールがスケジュールに従って実行されるたびに、結果が見つかった場合はセキュリティ アラートが生成されます。 Azure Sentinel のセキュリティ アラートは、Azure Sentinel の **[ログ]** セクションで、**Azure Sentinel** グループの下にある **SecurityAlert** テーブルで確認できます。

Azure Sentinel では、分析ルールから生成されたアラートでもセキュリティ インシデントが生成され、Azure Sentinel メニューの **[脅威管理]** にある **[インシデント]** で確認できます。 インシデントは、セキュリティ運用チームが、適切な応答アクションを判断するためにトリアージして調査する対象です。 詳細については、こちらの「[チュートリアル: Azure Sentinel でインシデントを調査する](./tutorial-investigate-cases.md)」を参照してください。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>ブックによって脅威インテリジェンスに関する分析情報を提供する

最後に、Azure Sentinel ブックを使用して、Azure Sentinel の脅威インテリジェンスに関する重要な情報を視覚化したり、ビジネス ニーズに応じて簡単にブックをカスタマイズしたりすることができます。
Azure Sentinel で提供される脅威インテリジェンス ブックを見つける方法について説明します。また、ブックを編集してカスタマイズする方法についても説明します。

1. [Azure portal](https://portal.azure.com/) を開き、**Azure Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して脅威インジケーターをインポートした **ワークスペース** を選択します。

1. Azure Sentinel メニューの **[脅威管理]** セクションで、 **[ブック]** を選択します。

1. **[脅威インテリジェンス]** というタイトルの付いたブックに移動し、次に示すように、**ThreatIntelligenceIndicator** テーブルにデータがあることを確認します。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="データの確認":::
 
1. **[保存]** ボタンを選択し、ブックを格納する Azure の場所を選択します。 この手順は、ブックを任意の方法で変更し、変更内容を保存する場合に必要です。

1. 次に、 **[保存されたブックの表示]** ボタンを選択して、表示および編集用にブックを開きます。

1. これで、テンプレートによって提供される既定のグラフが表示されます。 次に、いずれかのグラフに変更を加えてみましょう。 ページの上部にある **[編集]** ボタンを選択して、ブックの編集モードに入ります。

1. 脅威の種類別に脅威インジケーターを表す新しいグラフを追加しましょう。 ページの下部までスクロールし、[クエリの追加] を選択します。

1. **Log Analytics ワークスペースのログ クエリ** のテキスト ボックスに次のテキストを追加します。
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **[視覚化]** ドロップダウンで、 **[横棒グラフ]** を選択します。

1. **[編集が完了しました]** ボタンを選択します。 ブックの新しいグラフが作成されました。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="横棒グラフ":::

ブックには、Azure Sentinel のあらゆる側面に関する分析情報を提供する、強力な対話型ダッシュボードが用意されています。 ブックでは多くのことができますが、提供されるテンプレートを最適な出発点として、使い慣れてきたらテンプレートをカスタマイズしたり、さまざまなデータ ソースを組み合わせた新しいダッシュボードを作成したりできます。これにより、独自の方法でデータを視覚化できるようになります。 Azure Sentinel ブックは Azure Monitor ブックに基づいているため、広範なドキュメントが既にあり、さらに多くのテンプレートを利用できます。 [Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)の方法に関するこの記事から読み始めることをお勧めします。 

また、[GitHub 上に Azure Monitor ブック](https://github.com/microsoft/Application-Insights-Workbooks)の活発なコミュニティがあり、追加のテンプレートをダウンロードしたり、独自のテンプレートを投稿したりすることができます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel の脅威インテリジェンス機能と新しい脅威インテリジェンス ブレードについて説明しました。 Azure Sentinel の脅威インテリジェンス機能の使用に関する実用的なガイダンスについては、次の記事を参照してください。

- Azure Sentinel に[脅威インテリジェンス データを接続する](./connect-threat-intelligence.md)。
- Azure Sentinel で[組み込み](./tutorial-detect-threats-built-in.md)または[カスタム](./tutorial-detect-threats-custom.md)のアラートを作成し、インシデントを[調査](./tutorial-investigate-cases.md)する。