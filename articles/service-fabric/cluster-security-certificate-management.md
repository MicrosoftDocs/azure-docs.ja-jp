---
title: Service Fabric クラスターでの証明書管理
description: X.509 証明書で保護された Service Fabric クラスターでの証明書の管理について説明します。
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: a8a7e8954f3c9d5b54c2e1ed9caa330ef92d4512
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100099508"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric クラスターでの証明書管理

この記事では、Azure Service Fabric クラスターでの通信をセキュリティで保護するために使用される証明書の管理面について説明します。また、[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)の概要に関する記事と、[Service Fabric での X.509 証明書ベースの認証](cluster-security-certificates.md)に関する記事の説明を補足します。 読者が基本的なセキュリティの概念と、クラスターのセキュリティを構成するために Service Fabric で公開されているコントロールについて理解していることを前提としています。  

このタイトルで取り上げられている側面:

* "証明書管理" とは
* 証明書管理に関係するロールとエンティティ
* 証明書のプロセス
* 例の詳細
* トラブルシューティングとよく寄せられる質問

はじめに -免責事項: この記事では、理論上の例と、サービスやテクノロジなどの詳細が必要な実務上の例を組み合わせています。 対象読者の大部分は Microsoft 社内ユーザーであるため、Microsoft Azure に固有のサービス、テクノロジ、および製品について触れます。 Microsoft 固有の詳細がお客様のケースに該当しない場合のご不明な点やガイダンスについては、コメント セクションで気軽にお問い合わせください。

## <a name="defining-certificate-management"></a>証明書管理の定義
[コンパニオン記事](cluster-security-certificates.md)で見てきたように、証明書は、それが表すエンティティを説明する属性を持つ非対称キー ペアを実質的にバインドする暗号オブジェクトです。 ただし、その有効期間が有限であり、侵害される可能性があるという点で "生もの" のオブジェクトでもあります。証明書は、誤って開示したり、悪用されたりすると、セキュリティの観点から役に立たなくなります。 これは、定期的に、またはセキュリティ インシデントに応じて、証明書を変更する必要があることを意味します。 管理のもう 1 つの側面 (これがこの記事のトピックです) は、証明書の秘密キーの保護、つまり証明書の取得とプロビジョニングに関係するエンティティの ID を保護するシークレットの保護です。 証明書を取得し、必要な場所に安全に (そしてセキュアに) 転送するために使用されるプロセスと手順を "証明書管理" と呼びます。 登録、ポリシー設定、承認制御などの一部の管理操作は、この記事では取り上げません。 さらに、プロビジョニング、更新、キー更新、失効など、偶発的にしか Service Fabric に関連しないものもあります。それでも、これらの操作を理解することでクラスターを適切に保護するために役立つため、ここではある程度説明します。 

目標は、証明書管理をできるだけ自動化して、ユーザー操作が不要なプロセスの場合にクラスターの中断のない可用性を確保し、セキュリティの保証を提供することです。 現在、この目標は Azure Service Fabric クラスターで達成できます。以降、この記事では、まず証明書管理を分解し、後で自動ロールオーバーを有効にすることに焦点を当てます。

具体的には、次のトピックについて扱います。
  - 証明書管理のコンテキストにおける所有者とプラットフォーム間の属性の分離に関連する前提
  - 発行から使用までの証明書の長いパイプライン
  - 証明書ローテーション - 理由、方法、タイミング
  - 発生する可能性のある問題

ドメイン名のセキュリティ保護と管理、証明書への登録、証明書発行を実施するための承認制御の設定などの側面は、この記事では取り上げません。 お気に入りの公開キー基盤 (PKI) サービスの登録機関 (RA) に関する情報を参照してください。 Microsoft 社内ユーザー: Azure Security にお問い合わせください。

## <a name="roles-and-entities-involved-in-certificate-management"></a>証明書管理に関係するロールとエンティティ
Service Fabric クラスターのセキュリティ アプローチは、"クラスター所有者が宣言し、Service Fabric ランタイムによってそれが実施される" というケースです。 つまり、クラスターの機能に参加している証明書、キー、またはその他の ID の資格情報のほとんどは、サービス自体に由来するものではありません。これらはすべてクラスターの所有者によって宣言されます。 さらに、クラスター所有者は、証明書をクラスターにプロビジョニングし、必要に応じて更新し、常に証明書のセキュリティを確保する役割も担っています。 具体的には、クラスター所有者は次のことを確保する必要があります。
  - クラスター マニフェストの NodeType セクションで宣言された証明書を、[プレゼンテーション規則](cluster-security-certificates.md#presentation-rules)に従って、その種類の各ノードで見つけることができる
  - 上記の宣言された証明書は、対応する秘密キーを含めてインストールされる
  - プレゼンテーション規則で宣言された証明書は、[検証規則](cluster-security-certificates.md#validation-rules)に合格する必要がある 

Service Fabric は、次の役割を担います。
  - クラスター定義の宣言と一致する証明書を特定および検索する  
  - "必要" に応じて、Service Fabric で制御されるエンティティに対応する秘密キーへのアクセス権を付与する
  - 確立されたセキュリティのベスト プラクティスとクラスター定義に厳密に従って証明書を検証する
  - 証明書の有効期限が迫っている場合、または証明書の検証の基本手順を実行できなかった場合にアラートを生成する
  - クラスター定義の証明書関連の側面がホストの基となる構成によって満たされていることを (ある程度) 検証する 

そのため、(アクティブな操作としての) 証明書管理の負担は、クラスター所有者にのみ課せられます。 以下のセクションでは、使用できるメカニズムとそのクラスターに与える影響を含め、各管理操作を詳しく見ていきます。

## <a name="the-journey-of-a-certificate"></a>証明書のプロセス
Service Fabric クラスターのコンテキストで、証明書の発行から使用までの過程を簡単におさらいしましょう。

  1. ドメイン所有者は、PKI の RA に、以降の証明書に関連付けたいドメインまたはサブジェクトを登録します。その結果、証明書は、このドメインまたはサブジェクトの所有権の証明になります
  2. また、ドメイン所有者は、RA で、承認された要求者の ID (ドメインまたはサブジェクトが指定された証明書の登録を要求する資格を持つエンティティ) を指定します。Microsoft Azure では、既定の ID プロバイダーは Azure Active Directory であり、承認された要求者は、対応する AAD ID によって (またはセキュリティ グループを介して) 指定されます
  3. 承認された要求者は、シークレット管理サービスを使用して証明書に登録します。Microsoft Azure では、SMS として Azure Key Vault (AKV) が選択されています。これにより、承認されたエンティティはシークレットと証明書を安全に保存し、取得できます。 また、AKV では、関連する証明書ポリシーの構成に従って、証明書の更新とキー更新が行われます (AKV では ID プロバイダーとして AAD が使用されています)。
  4. 承認された取得コンポーネント ("プロビジョニング エージェント" と呼びます) では、その秘密キーを含む証明書がコンテナーから取得され、クラスターをホストしているマシンにインストールされます
  5. (各ノードで管理者特権で実行されている) Service Fabric サービスによって、許可された Service Fabric エンティティに証明書へのアクセス権を付与されます。これらはローカル グループによって指定され、ServiceFabricAdministrators と ServiceFabricAllowedUsers に分割されます
  6. Service Fabric ランタイムからは、フェデレーションを確立したり、承認されたクライアントからの受信要求を認証したりする目的で証明書にアクセスし、使用します
  7. プロビジョニング エージェントでは、コンテナー証明書が監視され、更新が検出されるとプロビジョニング フローがトリガーされます。その後、クラスター所有者はクラスター定義を更新し、必要に応じて、証明書をロールオーバーする意図を示します。
  8. プロビジョニング エージェントまたはクラスター所有者は、使用されていない証明書のクリーンアップと削除も担当します
  
この記事の目的という観点からは、上記のシーケンスの最初の 2 つの手順はほとんど関係ありません。唯一の関係は、証明書のサブジェクトの共通名が、クラスター定義で宣言された DNS 名であることです。

これらの手順を以下に示します。拇印と共通名でそれぞれ宣言された証明書間のプロビジョニングの違いに注意してください。

*図 1.* 拇印によって宣言された証明書の発行とプロビジョニングのフロー。
![拇印によって宣言された証明書のプロビジョニング][Image1]

*図 2.* サブジェクトの共通名によって宣言された証明書の発行とプロビジョニングのフロー。
![サブジェクトの共通名によって宣言された証明書のプロビジョニング][Image2]

### <a name="certificate-enrollment"></a>証明書の登録
このトピックの詳細については、Key Vault の[ドキュメント](../key-vault/certificates/create-certificate.md)を参照してください。継続性と参照を容易にするために、この記事には概要を含めています。 引き続き Azure をコンテキストとして使用し、Azure Key Vault をシークレット管理サービスとして使用する場合、承認された証明書要求者には、コンテナーに対して、少なくともコンテナー所有者から付与された証明書管理アクセス許可が必要です。次に、要求者は次のように証明書に登録します。
    - Azure Key Vault (AKV) で証明書ポリシーを作成します。これにより、証明書のドメインとサブジェクト、目的の発行者、キーの種類と長さ、目的のキー使用法などが指定されます。詳細については、[Azure Key Vault の証明書](../key-vault/certificates/certificate-scenarios.md)に関するページを参照してください。 
    - 上記で指定したポリシーを使用して、同じコンテナーに証明書を作成します。これにより、コンテナー オブジェクトとしてキー ペア、秘密キーで署名された証明書署名要求が生成され、署名のために指定された発行者に転送されます
    - 発行者 (証明機関) が署名入り証明書を使用して応答すると、結果はコンテナーにマージされ、証明書を次の操作に使用できるようになります。
      - {vaultUri}/certificates/{name} 以下: 公開キーとメタデータを含む証明書
      - {vaultUri}/keys/{name} 以下: 暗号化操作 (ラップとラップ解除、署名と検証) に使用できる証明書の秘密キー
      - {vaultUri}/secrets/{name} 以下: 秘密キーを含む証明書。保護されていない pfx または pem ファイルとしてダウンロードできます。  
    コンテナー証明書は、実際には、ポリシーを共有している証明書インスタンスの時系列の行であることを思い出してください。 証明書のバージョンは、ポリシーの有効期間と更新の属性に従って作成されます。 コンテナー証明書は、サブジェクト、ドメイン、または DNS 名を共有しないことを強くお勧めします。サブジェクトが同じで、発行者、キーの使用法などのその他の属性が大幅に異なる別のコンテナー証明書から証明書インスタンスをプロビジョニングすると、クラスター内で混乱を招く可能性があります。

この時点で、コンテナーに証明書が存在し、使用できるようになります。 次に進みましょう。

### <a name="certificate-provisioning"></a>証明書のプロビジョニング
"プロビジョニング エージェント" について触れました。これは、秘密キーを含む証明書をコンテナーから取得し、それをクラスターの各ホストにインストールするエンティティです (Service Fabric によって証明書がプロビジョニングされないことを思い出してください)。このコンテキストでは、クラスターは Azure VM や仮想マシン スケール セットのコレクションでホストされます。 Azure では、以下のメカニズムを使用してコンテナーから VM または VMSS に証明書をプロビジョニングできます。ただし、前述のように、コンテナーの所有者からプロビジョニング エージェントに対して、コンテナーの "取得" アクセス許可が以前に付与されていることを前提とします。 
  - アドホック: オペレーターは (pfx/PKCS#12 または pem として) コンテナーから証明書を取得し、それを各ノードにインストールします
  - デプロイ中に仮想マシン スケール セットの "シークレット" として: Compute Service によって、オペレーターの代理でファースト パーティ ID が使用され、テンプレートのデプロイが有効なコンテナーから証明書が取得され、仮想マシン スケール セットの各ノードに ([このように](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)) インストールされます。これにより、バージョン管理されたシークレットのプロビジョニングのみが可能になることに注意してください
  - [Key Vault VM 拡張機能](../virtual-machines/extensions/key-vault-windows.md)の使用: これにより、監視対象の証明書を定期的に更新しながら、バージョンなしの宣言を使用して証明書をプロビジョニングできます。 この場合、VM または VMSS には、監視対象の証明書を含むコンテナーへのアクセス権が付与された ID である[マネージド ID](../virtual-machines/security-policy.md#managed-identities-for-azure-resources) が必要です。

アドホック メカニズムは、セキュリティから可用性までさまざまな理由からお勧めできません。また、ここではこれ以上詳しく説明しません。詳細については、[仮想マシン スケール セットの証明書](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)に関するページを参照してください。

VMSS ベースまたはコンピューティングベースのプロビジョニングには、セキュリティと可用性の利点がありますが、制限もあります。 設計上、証明書をバージョン管理されたシークレットとして宣言する必要があるため、拇印で宣言された証明書で保護されたクラスターにのみ適しています。 対照的に、Key Vault VM 拡張機能ベースのプロビジョニングでは、監視対象の各証明書の最新バージョンが常にインストールされるため、サブジェクトの共通名で宣言された証明書で保護されたクラスターにのみ適しています。 特に注意が必要な点として、インスタンスによって (つまり、拇印によって) 宣言された証明書には、自動更新プロビジョニング メカニズム (KVVM 拡張など) を使用しないでください。可用性を失うリスクがかなり大きくなります。

その他のプロビジョニングメカニズムが存在する可能性がありますが、現時点では、前述のものを Azure Service Fabric クラスターに使用できます。

### <a name="certificate-consumption-and-monitoring"></a>証明書の使用と監視
前述のように、Service Fabric ランタイムは、クラスター定義で宣言された証明書を特定して使用する役割を担っています。 [証明書ベースの認証](cluster-security-certificates.md)に関する記事で、Service Fabric でプレゼンテーション規則と検証規則をそれぞれ実装する方法が詳細に説明されているため、ここでは説明しません。 ここでは、アクセス権とアクセス許可の付与と、監視について見ていきます。

Service Fabric の証明書は、フェデレーション レイヤーでの相互認証から管理エンドポイントの TLS 認証まで、さまざまな目的で使用されることを思い出してください。これには、証明書の秘密キーにアクセスするためにさまざまなコンポーネントまたはシステム サービスが必要です。 Service Fabric ランタイムによって定期的に証明書ストアがスキャンされ、既知の各プレゼンテーション規則の一致が検索されます。一致する証明書ごとに、対応する秘密キーが特定され、その随意アクセス制御リストは、それらを必要とする各 ID に付与されたアクセス許可 (通常は読み取りと実行) を含むように更新されます (このプロセスは、非公式に "ACL 処理 (ACLing)" と呼ばれます)。このプロセスは 1 分間隔で実行され、設定の暗号化やエンドポイント証明書などに使用される "アプリケーション" 証明書も対象となります。 ACL 処理はプレゼンテーション規則に従っているため、拇印によって宣言され、その後のクラスター構成の更新なしで自動更新される証明書にはアクセスできないことに注意する必要があります。    

### <a name="certificate-rotation"></a>証明書ローテーション
補足: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) には、[更新](https://tools.ietf.org/html/rfc3647#section-4.4.6)とは、置き換えられる証明書と同じ属性を持つ証明書の発行と正式に定義されています。発行者、サブジェクトの公開キーおよび情報は保持されます。また、[キー更新](https://tools.ietf.org/html/rfc3647#section-4.4.7)とは、新しいキー ペアを持つ証明書の発行と定義されており、発行者が変更できるかどうかの制限はありません。 区別が重要になる場合があるため (発行者が固定された、サブジェクトの共通名で宣言された証明書の場合を考えてみてください)、ここでは、両方のシナリオに対応するために中立的な用語である "ローテーション" を選択します ("更新" は、非公式に使用される場合、実際にはキー更新を指すことに注意してください)。 

既に説明したように、Azure Key Vault で証明書の自動ローテーションがサポートされています。関連付けられた証明書ポリシーには、証明書がコンテナーでローテーションされる時点が、有効期限までの日数または全有効期間に占める割合として定義されています。 この新しい証明書をクラスターのすべてのノードに配布するには、この時点の後、以前の証明書の有効期限が切れる前に、プロビジョニング エージェントを呼び出す必要があります。 Service Fabric を使用すると、(クラスターで現在使用されている) 証明書の有効期限が事前に指定した間隔よりも早く発生したときに、正常性に関する警告を受け取ることができます。 コンテナー証明書を監視するように構成された自動プロビジョニング エージェント (つまり KeyVault VM 拡張機能) を使用すると、コンテナーを定期的にポーリングし、ローテーションを検出し、新しい証明書を取得してインストールすることができます。 VM または VMSS の "シークレット" 機能を介して行われるプロビジョニングでは、承認されたオペレーターが、新しい証明書に対応するバージョン管理された KeyVault URI を使用して VM または VMSS を更新する必要があります。

いずれの場合も、ローテーションされた証明書がすべてのノードにプロビジョニングされるようになります。ローテーションの検出に対して Service Fabric で採用されるメカニズムの説明は以上です。次に何が起こるかを見てみましょう。サブジェクトの共通名で宣言されたクラスター証明書にローテーションが適用されていると仮定します
  - 内部およびクラスターへの新しい接続については、Service Fabric ランタイムによって、最後に発行された一致する証明書 ('NotBefore' プロパティの最大値) が検索され選択されます。 これは、以前のバージョンの Service Fabric ランタイムからの変更点になります。
  - 既存の接続はそのまま維持するか、自然に期限切れになるか、または終了することができます。内部ハンドラーには、新しい一致が存在することが通知されます

> [!NOTE] 
> バージョン 7.2.445 (7.2 CU4) より前では、Service Fabric によって、最も有効期限が遅い証明書 ('NotAfter' プロパティが最も遅い証明書) が選択されています

これは、次の重要な観察に変わります。
  - 有効期限が現在使用中の証明書の有効期限よりも早い場合、更新証明書は無視される場合があります。
  - クラスターまたはホストされているアプリケーションの可用性は、証明書をローテーションするディレクティブよりも優先されます。クラスターは最終的に新しい証明書に収束しますが、タイミングは保証されません。 その結果、次のようになります。
  - オブザーバーから見て、ローテーションされた証明書によって以前の証明書が完全に置き換えられたことはすぐにわからない可能性があります。(クラスター証明書の場合) これを保証する唯一の方法は、ホスト マシンを再起動することです。 Service Fabric ノードを再起動するだけでは不十分です。クラスター内でリース接続を形成するカーネル モード コンポーネントは影響を受けないためです。 また、VM または VMSS を再起動すると、一時的に可用性が失われる可能性があります (アプリケーション証明書の場合は、それぞれのアプリケーション インスタンスのみを再起動するだけで十分です)。
  - キーが更新され、検証規則を満たさない証明書を導入すると、クラスターは実質的に中断されます。 この最も一般的な例は、予期しない発行者の場合です。クラスター証明書は、発行者が固定されたサブジェクトの共通名を使用して宣言されますが、ローテーションされた証明書は、新しい、宣言されていない発行者によって発行されたものです。     

### <a name="certificate-cleanup"></a>証明書のクリーンアップ
現時点では、証明書を明示的に削除するための Azure のプロビジョニングはありません。 多くの場合、特定の証明書が特定の時間に使用されているかどうかを判断することは容易ではありません。 これはクラスター証明書よりもアプリケーション証明書の方が困難です。 Service Fabric プロビジョニング エージェントではないため、どのような状況でもそれ自体によってユーザーが宣言した証明書が削除されることはありません。 標準的なプロビジョニング メカニズムの場合は次のようになります。
  - VM または VMSS シークレットとして宣言された証明書は、VM または VMSS 定義で参照されている限り、プロビジョニングされます。また、コンテナーから取得できます (コンテナー シークレットまたは証明書を削除すると、以降の VM または VMSS のデプロイに失敗します。同様に、コンテナーでシークレット バージョンを無効にすると、そのシークレット バージョンを参照する VM または VMSS のデプロイも失敗します)
  - KeyVault VM 拡張機能を使用してプロビジョニングされた以前のバージョンの証明書は、VM または VMSS ノードに存在する場合と存在しない場合があります。 このエージェントでは、現在のバージョンが取得され、インストールされるだけで、証明書は削除されません。 ノードを再イメージ化すると (通常は毎月実行されます)、証明書ストアが OS イメージのコンテンツにリセットされるため、以前のバージョンは暗黙的に削除されます。 ただし、仮想マシン スケール セットをスケールアップすると、監視対象の証明書の最新バージョンのみがインストールされることを考慮してください。インストールされた証明書に関して、ノードの均一性は想定しないでください。   

## <a name="simplifying-management---an-autorollover-example"></a>管理の簡略化 - 自動ロールオーバーの例
これまでに、メカニズム、制限について説明し、複雑な規則と定義の概要を説明し、停止の恐ろしい予測を行いました。 そろそろ、こうしたすべての懸念を回避するように自動証明書管理を設定する方法を説明するタイミングではないでしょうか。 ここでは、PaaSv2 仮想マシン スケール セットで実行されている Azure Service Fabric クラスターのコンテキストでそれを行い、次のようにシークレット管理に Azure Key Vault を使用し、マネージド ID を利用します。
  - 証明書の検証は、拇印の固定からサブジェクトと発行者の固定に変更されます。特定の発行者からの特定のサブジェクトを持つ証明書はすべて同等に信頼されます
    - 証明書は、信頼されるストア (Key Vault) に登録され、そこから取得され、エージェント (この場合は KeyVault VM 拡張機能) によって更新されます
    - 証明書のプロビジョニングは、(ComputeRP によって行われる) デプロイ時およびバージョンベースから、デプロイ後に、バージョンのない KeyVault URI を使用するように変更されます
    - KeyVault へのアクセス権は、ユーザーが割り当てたマネージド ID を介して付与されます。UA ID が作成され、デプロイ時に仮想マシン スケール セットに割り当てられます
    - デプロイ後、エージェント (KV VM 拡張機能) によって、仮想マシン スケール セットの各ノード上で監視対象の証明書がポーリングされ、更新されます。証明書のローテーションはこのように完全に自動化され、SF によって最も有効期限が遅い有効な証明書が自動的に取得されるようになります

このシーケンスは完全にスクリプト化または自動化されており、証明書の自動ロールオーバー用に構成されたクラスターの初回のデプロイは、ユーザーが操作することなく実行できます。 詳細な手順については後述します。 ここでは、PowerShell コマンドレットと json テンプレートのフラグメントを組み合わせて使用します。 Azure との対話をサポートするすべての方法を使用して、同じ機能を実現できます。

> [!NOTE]
> この例では、証明書がコンテナーに既に存在していることを前提としています。KeyVault で管理される証明書の登録と更新には、この記事で前述したように、前提条件となる手動の手順が必要です。 運用環境の場合は、KeyVault で管理された証明書を使用します。Microsoft の内部 PKI に固有のサンプル スクリプトが以下に含まれています。

> [!NOTE]
> 証明書の自動ロールオーバーは、CA から発行された証明書に対してのみ意味があります。自己署名証明書 (Azure portal で Service Fabric クラスターをデプロイするときに生成されるものなど) を使用することは無意味ですが、発行者の拇印がリーフ証明書と同じであると宣言することで、ローカルのデプロイまたは開発者がホストするデプロイには利用できます。

### <a name="starting-point"></a>開始ポイント
簡潔にするために、次のような開始状態を想定します。
  - Service Fabric クラスターが存在し、拇印によって宣言された CA 発行の証明書で保護されています。
  - 証明書はコンテナーに格納され、仮想マシン スケール セットシークレットとしてプロビジョニングされています
  - クラスターを共通名ベースの証明書の宣言に変換するために同じ証明書が使用されるので、サブジェクトと発行者によって検証できます。そうでない場合は、この目的のために CA から発行された証明書を取得し、[こちら](service-fabric-cluster-security-update-certs-azure.md)で説明されているように、拇印によるクラスター定義に追加します。

このような状態に対応するテンプレートからの json の抜粋を次に示します。多くの必要な設定が省略され、証明書関連の側面のみを示していることに注意してください。
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

上の例は、基本的に、拇印 ```json [parameters('primaryClusterCertificateTP')] ``` があり、KeyVault URI ```json [parameters('clusterCertificateUrlValue')] ``` で見つかった証明書が、拇印によってクラスターの唯一の証明書として宣言されていることを示しています。 次に、証明書の自動ロールオーバーを確実にするために必要な追加のリソースを設定します。

### <a name="setting-up-prerequisite-resources"></a>前提条件リソースの設定
前述のように、仮想マシン スケール セットのシークレットとしてプロビジョニングされた証明書は、Microsoft.Compute Resource Provider サービスによって、ファーストパーティ ID を使用して、デプロイ オペレーターに代わって、コンテナーから取得されます。 自動ロールオーバーの場合は状況が変わります。ここでは、仮想マシン スケール セットに割り当てられ、コンテナーのシークレットへのアクセス許可が付与されているマネージド ID を使用するように切り替えます。

以降のすべての抜粋も付随してデプロイする必要があります。これらは、実行ごとの分析と説明のために個別に示されます。

まず、ユーザー割り当て ID を定義します (既定値が例として含まれています)。最新の情報については、[公式ドキュメント](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)を参照してください。
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

次に、この ID にコンテナー シークレットへのアクセス権を付与します。最新の情報については、[公式ドキュメント](/rest/api/keyvault/vaults/updateaccesspolicy)を参照してください。
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

次の手順では、以下を実行します。
  - ユーザーが割り当てた ID を仮想マシン スケール セットに割り当てる
  - マネージド ID の作成と、コンテナーへのアクセス権を付与した結果に対する仮想マシン スケール セットの依存関係を宣言する
  - KeyVault VM 拡張機能を宣言し、起動時に監視対象の証明書を取得することを要求する ([公式ドキュメント](../virtual-machines/extensions/key-vault-windows.md))
  - KVVM 拡張機能を利用し、クラスター証明書を共通名に変換するように Service Fabric VM 拡張機能の定義を更新します (これらの変更は同じリソースのスコープに含まれるため、1 つの手順で行っています)。

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
上記では明記されていませんが、仮想マシン スケール セットの "OsProfile" セクションからコンテナー証明書の URL が削除されていることに注意してください。
最後の手順は、証明書の宣言を拇印から共通名に変更するするようにクラスター定義を更新することです。ここでは、発行者の拇印も固定しています。

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

この時点で、上記の更新は 1 つのデプロイで実行できます。[クラスター証明書の拇印から共通名への変換](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)に関するセグメントで説明されているように、Service Fabric リソース プロバイダー サービスによってクラスターのアップグレードはいくつかの手順に分割されます。

### <a name="analysis-and-observations"></a>分析と監視
このセクションは、上記の詳細な手順を説明し、重要な側面に注意を引くための包括的な説明です。

#### <a name="certificate-provisioning-explained"></a>証明書のプロビジョニング、説明
KVVM 拡張機能は、プロビジョニング エージェントとして、事前に指定された頻度で継続的に実行されます。 監視対象の証明書の取得に失敗すると、次の行に進み、次のサイクルまで休止状態になります。 SFVM 拡張機能は、クラスター ブートストラップ エージェントとして、クラスターを形成する前に宣言された証明書が必要です。 つまり、SFVM 拡張機能は、ここで ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` 句と KeyVaultVM 拡張機能の ```json "requireInitialSync": true``` 設定で示されているクラスター証明書の取得に成功した後にのみ実行できます。 これは、KVVM 拡張機能に対して、最初の実行時 (デプロイ後または再起動後) に、すべてが正常にダウンロードされるまで、監視対象の証明書を循環させる必要があることを示しています。 このパラメーターを false に設定し、さらにクラスター証明書の取得に失敗すると、クラスターのデプロイに失敗します。 逆に、監視対象の証明書の正しくない、または無効なリストとの初期同期が必要になると、KVVM 拡張機能の障害が発生し、その結果、クラスターのデプロイが失敗します。  

#### <a name="certificate-linking-explained"></a>証明書のリンク、説明
KVVM 拡張機能の "linkOnRenewal" フラグと、それが false に設定されていることに気づいたかもしれません。 ここでは、このフラグによって制御される動作と、クラスターの機能への影響について詳しく説明します。 この動作は Windows に固有のものです。

その[定義](../virtual-machines/extensions/key-vault-windows.md#extension-schema)に従って次のようにします。

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

TLS 接続の確立に使用される証明書は、通常、S チャネル セキュリティ サポート プロバイダーを介して [ハンドルとして取得](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea)されます。つまり、クライアントからは証明書自体の秘密キーに直接アクセスしません。 S チャネルは、証明書拡張機能 ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) の形式で資格情報のリダイレクト (リンク) をサポートします。このプロパティが設定されている場合、その値は "更新" 証明書の拇印を表すため、S チャネルでは、代わりにリンクされた証明書を読み込もうと試行されます。 実際、このリンクされた (そしてできれば非循環的な) リストは、"最終的な" 証明書 (更新マークのないもの) になるまで走査されます。 この機能を慎重に使用すると、(たとえば) 証明書の期限切れなどによる可用性の損失を軽減できます。 その他の例として、診断や軽減が困難な停止の原因となる場合があります。 S チャネルでは、サブジェクト、発行者、またはクライアントによる結果の証明書の検証に参加するその他の特定の属性に関係なく、更新プロパティに対して無条件に証明書の走査を実行されます。 実際には、生成された証明書に秘密キーが関連付けられていないか、またはキーが想定されるコンシューマーに ACL 処理されていない可能性があります。 
 
リンクが有効な場合、KeyVault VM 拡張機能では、監視対象の証明書をコンテナーから取得すると、更新の拡張機能プロパティを介してそれらをリンクするために、一致する既存の証明書を見つけようとします。 一致は、(排他的に) サブジェクトの別名 (SAN) に基づいており、次の例のように機能します。
次の 2 つの既存の証明書を想定します。A:CN = “Alice's accessories”, SAN = {“alice.universalexports.com”}, renewal = ‘’ B:CN = “Bob's bits”, SAN = {“bob.universalexports.com”, “bob.universalexports.net”}, renewal = ‘’
 
証明書 C が KVVM ext によって取得されるとします。CN = “Mallory's malware”, SAN = {“alice.universalexports.com”, “bob.universalexports.com”, “mallory.universalexports.com”}
 
A の SAN リストは C に完全に含まれているため、A.renewal = C.thumbprint です。B の SAN リストには C と共通の共通部分がありますが、完全には含まれていないため、B.renewal は空のままです。
 
証明書 A でこの状態の AcquireCredentialsHandle (S チャネル) を呼び出そうとすると、実際には C がリモート パーティに送信されます。 Service Fabric の場合、クラスターの [Transport サブシステム](service-fabric-architecture.md#transport-subsystem)によって相互認証に S チャネルが使用されるため、上記の動作はクラスターの基本的な通信に直接影響します。 上記の例を続行し、A がクラスター証明書であると仮定すると、次に起こることは、次の条件によって変わります。
  - C の秘密キーが Fabric を実行しているアカウントに ACL 処理されていない場合、秘密キーの取得に失敗します (SEC_E_UNKNOWN_CREDENTIALS など)
  - C の秘密キーにアクセスできる場合、他のノードから返された承認エラーが表示されます (CertificateNotMatched、unauthorized など)。 
 
いずれの場合も転送は失敗し、クラスターが停止する可能性があります。この現象はさまざまです。 さらに悪いことに、リンクは更新の順序によって変わります。KVVM 拡張機能の監視対象の証明書リストの順序、コンテナーの更新スケジュール、さらには取得の順序が変わる一時的なエラーによって決まります。

このようなインシデントを軽減するには、次の推奨事項があります。
  - 異なるコンテナー証明書の SAN を混在させないでください。各コンテナー証明書で個別の目的を果たし、サブジェクトと SAN はそれを具体的に反映するようにします
  - SAN リストにサブジェクトの共通名を含めます (文字どおり "CN=<subject common name>")  
  - 不明な場合は、KVVM 拡張機能でプロビジョニングされた証明書の更新時にリンクを無効にします 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>ユーザー割り当てマネージド ID を使用する理由と 使用する意味
上記の json スニペットから明らかになったように、変換を確実に成功させ、クラスターの可用性を維持するには、特定のシーケンスの操作と更新が必要です。 具体的には、仮想マシン スケール セット リソースでその ID を宣言して使用し、(ユーザーの観点から) 1 回の更新でシークレットを取得します。 (クラスターをブートストラップする) Service Fabric VM 拡張機能は KeyVault VM 拡張機能の完了に依存し、これはさらに監視対象の証明書の正常な取得に依存します。 KVVM 拡張機能では、コンテナーへのアクセスに仮想マシン スケール セットの ID が使用されます。つまり、仮想マシン スケール セットをデプロイする前に、コンテナーのアクセス ポリシーを更新しておく必要があります。 

マネージド ID の作成を破棄する場合や、別のリソースに割り当てる場合、デプロイ オペレーターは、テンプレートで参照される他のリソースを管理するために必要なロールに加えて、サブスクリプションまたはリソース グループに必要なロール (ManagedIdentityOperator) を持っている必要があります。 

セキュリティの観点から、仮想マシン (スケール セット) は、Azure ID に関してセキュリティ境界と見なされることを思い出してください。 つまり、VM でホストされているアプリケーションを使用すると、原則として、VM を表すアクセス トークンを取得できます。マネージド ID アクセス トークンは、認証されていない IMDS エンドポイントから取得されます。 VM を共有環境またはマルチテナント環境と考えている場合、おそらくこの方法ではクラスター証明書を取得できません。 ただし、証明書の自動ロールオーバーに適した唯一のプロビジョニング メカニズムです。

## <a name="troubleshooting-and-frequently-asked-questions"></a>トラブルシューティングとよく寄せられる質問

*Q*: KeyVault で管理される証明書にプログラムで登録するにはどうすればよいですか?
*A*: KeyVault のドキュメントから発行者の名前を見つけて、以下のスクリプトで置き換えてください。  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*Q*: 宣言されていない、または指定されていない発行者が証明書を発行するとどうなりますか? 特定の PKI のアクティブな発行者の完全な一覧はどこで入手できますか?
*A*: 証明書の宣言で発行者の拇印が指定されていて、証明書の直接の発行者が固定された発行者リストに含まれていない場合、ルートがクライアントによって信頼されているかどうかに関係なく、証明書は無効と見なされます。 そのため、発行者リストが最新であることを確認することが重要です。 新しい発行者の導入はごくまれにしか起こらないため、証明書の発行を開始する前に広く公表しておく必要があります。 

一般に、PKI は、IETF [RFC 7382](https://tools.ietf.org/html/rfc7382) に従って、認証局運用規定を発行および維持します。 他にも情報はありますが、すべてのアクティブな発行者が含まれます。 プログラムでこのリストを取得する方法は、PKI によって異なる場合があります。   

Microsoft 内部の PKI については、承認された発行者を取得するために使用されるエンドポイントおよび SDK に関する内部ドキュメントを参照してください。このリストを定期的に調査し、クラスターの定義に "*すべて*" の発行者が含まれていることを確認するのは、クラスター所有者の役割です。

*Q*: 複数の PKI はサポートされていますか? 
*A*: はい。同じ値を持つクラスター マニフェストで複数の CN エントリを宣言することはできませんが、同じ CN に対応する複数の PKI からの発行者を列挙することはできます。 これは推奨される方法ではなく、証明書の透明性を確保するために、このような証明書は発行されない可能性があります。 ただし、これは、ある PKI から別の PKI に移行する手段として許容されているメカニズムです。

*Q*: 現在のクラスター証明書が CA によって発行されていない場合、または目的のサブジェクトがない場合はどうなりますか? 
*A*: 目的のサブジェクトを含む証明書を取得し、セカンダリとして拇印によるクラスターの定義に追加します。 アップグレードが正常に完了したら、別のクラスター構成のアップグレードを開始して、証明書の宣言を共通名に変換します。 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
