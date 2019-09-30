---
title: OPC Vault 証明書管理サービスを安全に実行する方法 - Azure | Microsoft Docs
description: Azure で OPC Vault 証明書管理サービスを安全に実行する方法と、考慮すべきその他のセキュリティ ガイドラインについて説明します。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997654"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>OPC Vault 証明書管理サービスを安全に実行する方法

この記事では、Azure で OPC Vault 証明書管理サービスを安全に実行する方法と、考慮すべきその他のセキュリティ ガイドラインについて説明します。

## <a name="roles"></a>ロール

### <a name="trusted-and-authorized-roles"></a>信頼されたロールと承認済みロール

OPC Vault マイクロサービスは、個別のロールでサービスのさまざまな部分にアクセスできるように構成されています。

> [!IMPORTANT]
> デプロイ中に、スクリプトでは、すべてのロールのユーザーとしてデプロイ スクリプトを実行するユーザーのみを追加します。
> このロールの割り当ては、以下のガイドラインに従って、運用環境のデプロイについて確認し、適切に再構成する必要があります。
> このタスクでは、Azure AD エンタープライズ アプリケーション ポータルでロールとサービスを手動で割り当てる必要があります。

### <a name="certificate-management-service-roles"></a>証明書管理サービスのロール

OPC Vault マイクロサービスでは、次のロールを定義します。

- **閲覧者**:既定では、テナント内のすべての認証済みユーザーに読み取りアクセス許可があります。 
  - アプリケーションと証明書の要求への読み取りアクセス許可です。 アプリケーションと証明書の要求の一覧を表示し、クエリを実行できます。 また、デバイス検出情報とパブリック証明書には、読み取りアクセス許可でアクセスできます。
- **ライター**:ライター ロールは、特定のタスクに対する書き込みアクセス許可を追加するためにユーザーに割り当てます。 
  - アプリケーションと証明書の要求への読み取り/書き込みアクセス許可です。 アプリケーションの登録、更新、および登録解除を行うことができます。 証明書の要求を作成して、承認された秘密キーと証明書を取得できます。 秘密キーを削除することもできます。
- **承認者**:承認者ロールは、証明書の要求を承認または拒否するためにユーザーに割り当てます。 このロールに他のロールは含まれません。
  - ユーザーは、OPC Vault マイクロサービス API にアクセスするための承認者ロールに加え、証明書に署名できるように Key Vault のキー署名アクセス許可も持っている必要があります。
  - ライターと承認者のロールは、別のユーザーに割り当てる必要があります。
  - 承認者の主なロールは、証明書の要求の生成と拒否の承認です。
- **管理者**: 管理者ロールは、証明書グループを管理するためにユーザーに割り当てます。 このロールでは、承認者ロールはサポートされませんが、ライター ロールが含まれます。
  - 管理者は証明書グループを管理し、構成を変更できます。また、新しい CRL を発行して、アプリケーション証明書を失効させることができます。
  - ライター、承認者および管理者の各ロールは異なるユーザーに割り当てられていることが理想的です。 セキュリティを強化するために、承認者または管理者ロールを持つユーザーは、証明書を発行したり、発行者 CA 証明書を更新したりするために、KeyVault でのキー署名アクセス許可も必要です。
  - マイクロサービス管理ロールに加え、このロールでは、以下を始めとする処理を行います。
    - CA のセキュリティ プラクティスの実装の管理の担当。
    - 証明書の生成、失効、および中断の管理。 
    - 暗号化キーのライフサイクル管理 (発行者 CA キーの更新など)。
    - CA を運用するサービスのインストール、構成、および維持。
    - サービスの日常的な操作。 
    - CA およびデータベースのバックアップと回復。

### <a name="other-role-assignments"></a>その他のロールの割り当て

サービスを実行するときに、次のロールも考慮して割り当てる必要があります。

- 外部ルート証明機関との証明書調達契約のビジネス オーナー (所有者が外部 CA から証明書を購入した場合、または外部 CA の下位にある CA を運用している場合)。
- 証明機関の開発と確認。
- 監査レコードの確認。
- CA のサポートや、物理的およびクラウドの機能の管理を支援するが、CA を運用する際に直接信頼されていない担当者は、承認されたロールに属するものとして定義されます。 承認されたロールのユーザーが実行できる一連のタスクを文書化する必要もあります。

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>信頼されたロールと承認済みロールのメンバーシップは毎年確認する必要がある

各ロールの一連のユーザー (手動プロセスの場合) またはサービス ID (自動化されたプロセスの場合) を確実に最小限に抑えられるように、少なくとも四半期ごとに、信頼されたロールと承認済みロールのメンバーシップを確認する必要があります。

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>証明書の発行プロセスでは証明書の要求元と承認者のロールを分ける必要がある

証明書の発行プロセスでは、証明書の要求元ロールと証明書の承認者ロール (ユーザーまたは自動化されたシステム) の役割を分ける必要があります。 証明書の発行は、証明書の要求元が証明書の取得を承認されていることを確認する証明書の承認者ロールによって承認される必要があります。 証明書の承認者ロールを保持するユーザーは、正式に承認されたユーザーである必要があります。

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>特権ロールの管理ではアクセスを制限して四半期ごとに確認する必要がある

管理者および承認者グループのメンバーシップの承認など、特権ロールの割り当ては、権限を持つ限られた数の担当者に制限する必要があります。 特権ロールのすべての変更は、24 時間以内にアクセスを取り消す必要があります。 最後に、特権ロールの割り当ては、四半期ごとに確認する必要があり、不要なまたは有効期限切れの割り当てはすべて削除する必要があります。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特権ロールでは 2 要素認証を使用する必要がある

承認者および管理者によるサービスへの対話型ログオンでは、多要素認証 (2 要素認証、MFA、または TFA) を使用する必要があります。

## <a name="certificate-service-operation-guidelines"></a>証明書サービス操作のガイドライン

### <a name="operational-contacts"></a>操作の連絡先

証明書サービスでは、詳細な操作上のインシデント対応の連絡先を含む、最新のセキュリティ対応プランがファイルに含まれている必要があります。

### <a name="security-updates"></a>セキュリティ更新プログラム

すべてのシステムを継続的に監視し、最新のセキュリティ更新プログラム/パッチのコンプライアンスで更新する必要があります。

> [!IMPORTANT]
> OPC Vault サービスの GitHub リポジトリは、セキュリティ パッチで継続的に更新されます。 GitHub の更新プログラムを監視し、定期的に更新プログラムをサービスに適用する必要があります。

### <a name="security-monitoring"></a>セキュリティの監視

たとえば、中央監視ソリューション (Azure Security Center、O365 監視ソリューションなど) にサブスクライブして、適切なセキュリティ監視をサブスクライブまたは実装し、セキュリティ イベントが確実に監視ソリューションに送信されるように適切に構成します。

> [!IMPORTANT]
> 既定では、OPC Vault サービスは、監視ソリューションとして [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) を使用してデプロイされます。 [Azure Security Center](https://azure.microsoft.com/services/security-center/) のようなセキュリティ ソリューションを追加することを強くお勧めします。

### <a name="assess-security-of-open-source-software-components"></a>オープン ソース ソフトウェア コンポーネントのセキュリティを評価する

製品またはサービス内で使用されるすべてのオープンソース コンポーネントは、中程度以上のセキュリティ脆弱性がない状態でなければなりません。

> [!IMPORTANT]
> OPC Vault サービスの GitHub リポジトリでは、継続的インテグレーション ビルド中に脆弱性について、すべてのコンポーネンがスキャンされます。 GitHub の更新プログラムを監視し、定期的に更新プログラムをサービスに適用する必要があります。

### <a name="maintain-an-inventory"></a>インベントリを維持する

資産インベントリは、すべての運用ホスト (永続仮想マシンを含む)、デバイス、すべての内部 IP アドレス範囲、VIP、およびパブリック DNS ドメイン名について維持する必要があります。 このインベントリは、30 日以内にシステム、デバイスの IP アドレス、VIP、またはパブリック DNS ドメインの追加または削除によって更新する必要があります。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>既定の Azure OPC Vault マイクロサービス運用環境のデプロイのインベントリ: 

**Azure** の場合:
- **[App Service プラン]** :サービス ホストの App Service プラン。 既定の S1。
- マイクロサービスの **App Service**:OPC Vault サービス ホスト。
- サンプル アプリケーションの **App Service**:OPC Vault サンプル アプリケーション ホスト。
- **KeyVault Standard**:Web サービスのシークレットと Cosmos DB キーを格納します。
- **KeyVault Premium**:署名サービス、Vault の構成およびアプリケーションの秘密キーの格納のために発行者 CA キーをホストします。
- **Cosmos DB**:アプリケーションと証明書の要求のデータベース。 
- **Application Insights**: (省略可能) Web サービスおよびアプリケーションの監視ソリューション。
- **AzureAD アプリケーションの登録**:サンプル アプリケーション、サービス、およびエッジ モジュールの登録。

クラウド サービスの場合、サービスのデプロイに使用されるすべてのホスト名、リソース グループ、リソース名、サブスクリプション ID、TenantId を文書化する必要があります。 

**IoT Edge** またはローカルの **IoT Edge サーバー**の場合:
- **OPC Vault IoT Edge モジュール**:ファクトリ ネットワーク OPC UA Global Discovery Server をサポートします。 

IoT Edge デバイスの場合は、ホスト名と IP アドレスを文書化する必要があります。 

### <a name="document-the-certification-authorities-cas"></a>証明機関 (CA) を文書化する

CA 階層ドキュメントには、サービスによって管理されていない場合でも、関連するすべての下位 CA、親 CA、およびルート CA を含む、運用されている CA がすべて含まれている必要があります。 正式なドキュメントではなく、有効期限切れでないすべての CA 証明書の完全なセットが提供される場合があります。

> [!NOTE]
> OPC Vault サンプル アプリケーションでは、ドキュメント用にサービスで使用され、生成されるすべての証明書のダウンロードがサポートされます。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>すべての証明機関 (CA) によって発行された証明書を文書化する

過去 12 か月間に発行されたすべての証明書の完全なセットを、ドキュメント用に提供する必要があります。

> [!NOTE]
> OPC Vault サンプル アプリケーションでは、ドキュメント用にサービスで使用され、生成されるすべての証明書のダウンロードがサポートされます。

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>暗号化キーを安全に削除するための SOP を文書化する

キーの削除は、CA の有効期間中にまれに発生する可能性があります。これが、KeyVault 証明書の削除権限が割り当てられているユーザーがいないことと、発行者 CA 証明書を削除するために API が公開されていない理由です。 証明機関の暗号化キーを安全に削除するための手動による標準的な操作手順は、Azure portal で KeyVault に直接アクセスし、KeyVault の証明書グループを削除することによってのみ利用できます。 確実に即時削除を行うには、[KeyVault の論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を無効にする必要があります。

## <a name="certificates"></a>証明書

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>証明書は最低限の証明書プロファイルに準拠している必要がある

OPC Vault サービスは、エンド エンティティ証明書をサブスクライバーに発行するオンライン証明機関 (CA) です。
OPC Vault マイクロサービスは、既定の実装でこれらのガイドラインに従います。

- すべての証明書には、以下のように次の x.509 フィールドを含める必要があります。
  - バージョン フィールドの内容は、v3 である必要があります。 
  - serialNumber フィールドの内容には、FIPS (Federal Information Processing Standards) 140 承認済みの乱数ジェネレーターから取得される、8 バイト以上のエントロピを含める必要があります。<br>
    > [!IMPORTANT]
    > OPC Vault のシリアル番号は、既定では 20 バイトで、OS 暗号化乱数ジェネレーターから取得されます。 乱数ジェネレーターは、Linux フレーバーではなく、Windows デバイスで FIPS 140 承認されます。 この事実は、基盤テクノロジの OpenSSL が FIPS 140 承認されていない、Linux VM または Linux Docker コンテナーを使用する、サービス デプロイを選択するときに考慮する必要があります。
  - issuerUniqueID および subjectUniqueID フィールドが存在してはなりません。
  - IETF RFC 5280 に従い、基本制約拡張機能でエンドエンティティ証明書を識別する必要があります。
  - Issuing CA 証明書の場合は、pathLenConstraint フィールドを 0 に設定する必要があります。 
  - 拡張キー使用法拡張機能が存在し、拡張キー使用法オブジェクト識別子 (OID) の最小セットが含まれている必要があります。 anyExtendedKeyUsage OID (2.5.29.37.0) を指定することはできません。 
  - CRL 配布ポイント (CDP) 拡張機能は、発行者 CA 証明書に存在する必要があります。<br>
    > [!IMPORTANT]
    > CRL 配布ポイント (CDP) 拡張機能は、OPC Vault CA 証明書に存在しますが、OPC UA デバイスではカスタム メソッドを使用して CRL が配布されます。
  - 機関情報アクセス拡張機能がサブスクライバー証明書に存在する必要があります。<br>
    > [!IMPORTANT]
    > 機関情報アクセス拡張機能は、OPC Vault サブスクライバー証明書に存在しますが、OPC UA デバイスではカスタム メソッドを使用して発行者 CA 情報が配布されます。
- 承認された非対称アルゴリズム、キーの長さ、ハッシュ関数およびパディング モードを使用する必要があります。
  - **RSA** と **SHA-2** は、唯一サポートされているアルゴリズム (*) です。
  - RSA は、暗号化、キー交換、署名に使用できる場合があります。
  - RSA 暗号化では、OAEP、RSA-KEM、または RSA-PSS パディング モードのみを使用する必要があります。
  - キーの長さは 2048 ビット以上必要です。
  - SHA-2 ファミリのハッシュ アルゴリズム (SHA256、SHA384、SHA512) を使用します。
  - 一般的な有効期間が 20 年以上の RSA ルート CA キーは、4096 ビット以上である必要があります。
  - RSA の発行者 CA キーは 2048 ビット以上である必要があります。CA 証明書の有効期限が 2030 年より後の場合、CA キーは 4096 ビット以上である必要があります。
- 証明書の有効期間
  - ルート CA 証明書:ルート CA の証明書の最大有効期間が 25 年を超えないようにしてください。
  - サブ CA またはオンライン発行者 CA 証明書:オンラインであり、サブスクライバー証明書のみを発行する CA の証明書の最大有効期間が、6 年を超えないようにしてください。 これらの CA の場合、新しい証明書を発行するために、関連する秘密署名キーを 3 年を超えて使用することはできません。<br>
    > [!IMPORTANT]
    > 外部ルート CA を利用せずに既定の OPC Vault マイクロサービスで生成される発行者証明書は、それぞれの要件と有効期間があるオンラインのサブ CA と同様に扱われます。 既定の有効期間は 5 年に設定され、キーの長さは 2048 以上です。
  - すべての非対称キーには最大 5 年の有効期間が必要であり、有効期間を 1 年にすることをお勧めします。<br>
    > [!IMPORTANT]
    > 既定では、OPC Vault で発行されたアプリケーション証明書の有効期間は 2 年であり、毎年置き換える必要があります。 
  - 証明書が更新されるたびに、新しいキーで更新されます。
- アプリケーション インスタンス証明書の OPC UA 固有の拡張機能
  - subjectAltName 拡張機能には、アプリケーション URI とホスト名が含まれており、これに FQDN、IPv4 および IPv6 アドレスが含まれる場合もあります。
  - keyUsage には、digitalSignature、nonRepudiation、keyEncipherment、および dataEncipherment が含まれます。
  - extendedKeyUsage には、serverAuth または clientAuth、あるいはその両方が含まれます。
  - authorityKeyIdentifier は、署名済み証明書で指定されます。

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>証明機関 (CA) のキーと証明書が最小要件を満たしている必要がある

- **秘密キー**:**RSA** キーは 2048 ビット以上である必要があります。CA 証明書の有効期限が 2030 年より後の場合、CA キーは 4096 ビット以上である必要があります。
- **有効期間**:オンラインであり、サブスクライバー証明書のみを発行する CA の証明書の最大有効期間が、6 年を超えないようにしてください。 これらの CA の場合、新しい証明書を発行するために、関連する秘密署名キーを 3 年を超えて使用することはできません。

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>CA キーはハードウェア セキュリティ モジュール (HSM) を使用して保護される

- OpcVault では Azure KeyVault Premium を使用し、キーは FIPS 140-2 レベル 2 ハードウェア セキュリティ モジュール (HSM) で保護されます。 

Key Vault が使う暗号化モジュールは、HSM でもソフトウェアでも、FIPS (Federal Information Processing Standards) で検証されます。<br>
HSM で保護されて作成またはインポートされたキーは、HSM 内で処理され、FIPS 140-2 レベル 2 について検証されます。<br>
ソフトウェアで保護されて作成またはインポートされたキーは、暗号化モジュール内で処理され、FIPS 140-2 レベル 1 について検証されます。

## <a name="operational-practices"></a>運用プラクティス

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>証明書の登録に関する標準的な運用 PKI プラクティスを文書化して維持する

以下を含む、CA での証明書の発行方法に関する標準的な運用手順 (SOP) を文書化し、維持します。 
- サブスクライバーを識別および認証する方法 
- 証明書の要求を処理および検証する方法 (該当する場合は、証明書の更新とキー更新の要求を処理する方法も含まれます) 
- 発行された証明書をサブスクライバーに配布する方法 

OPC Vault マイクロサービス SOP については、「[Overview](overview-opc-vault-architecture.md)」 (概要) と「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」のドキュメントで説明されています。 このプラクティスでは、OPC Unified Architecture 仕様の「Part 12:Discovery and Global Services」 (パート 12: 探索とグローバル サービス) に従います。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>証明書の失効に関する標準的な運用 PKI プラクティスを文書化して維持する

証明書の失効プロセスについては、「[Overview](overview-opc-vault-architecture.md)」 (概要) と「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」のドキュメントで説明されています。
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>証明機関 (CA) のキー生成処理を文書化する 

Azure KeyVault でストレージがセキュリティで保護されているため、OPC Vault マイクロサービスでの発行者 CA キーの生成については簡略化されており、「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」のドキュメントで説明されています。

しかし、外部ルート証明機関が使用されている場合、証明機関 (CA) のキー生成処理では、次の要件に従う必要があります。

CA キーの生成処理は、少なくとも次の項目を含む、文書化されたスクリプトに対して実行する必要があります。 
1. ロールと参加者の責任の定義
2. CA キーの生成処理の実行承認
3. 処理に必要な暗号化ハードウェアおよびアクティブ化のマテリアル
4. ハードウェアの準備 (資産/構成情報の更新とサインオフを含む)
5. オペレーティング システムのインストール
6. CA キーの生成処理中に行われる具体的な手順は次のとおりです。 
7. CA アプリケーションのインストールと構成
8. CA キーの生成
9. CA キーのバックアップ
10. CA 証明書の署名
9. サービスの保護された HSM への署名されたキーのインポート。
11. CA システムのシャットダウン
12. ストレージのマテリアルの準備


## <a name="next-steps"></a>次の手順

ここでは、OPC Vault を安全に管理する方法を学習しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [OPC UA デバイスを OPC Vault で保護する](howto-opc-vault-secure.md)