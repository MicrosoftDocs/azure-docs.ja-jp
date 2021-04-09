---
title: Microsoft Azure Data Box Disk のセキュリティ機能 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるクラウド ソリューションの Azure Data Box Disk で実装されているセキュリティ機能について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 59cce6d89bc863ad489a8ed37c7f284d3085c775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "79117277"
---
# <a name="azure-data-box-disk-security-and-data-protection"></a>Azure Data Box Disk のセキュリティとデータ保護

この記事では、Azure Data Box Disk のセキュリティに注目し、Azure Data Box ソリューションの各コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。 

## <a name="data-flow-through-components"></a>各コンポーネントを通るデータのフロー

Microsoft Azure Data Box ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Azure Data Box サービス** - ディスクを発注して構成し、その注文を最後まで追跡するための管理サービス。
- **Data Box Disk** - オンプレミスのデータを Azure にインポートするためにお客様に発送される物理ディスク。 
- **ディスクに接続されるクライアント/ホスト** – お客様のインフラストラクチャにおいて保護の対象となるデータを格納しているクライアント。USB 経由で Data Box Disk に接続されます。
- **クラウド ストレージ** – Azure クラウド内のデータの保存先となる場所。 通常は、お客様が作成した Azure Data Box リソースにリンクされたストレージ アカウントです。

次の図は、Azure Data Box Disk ソリューションを通じてオンプレミスから Azure に向かうデータのフローを示します。

![Data Box Disk のセキュリティ](media/data-box-disk-security/data-box-disk-security-1.png)

## <a name="security-features"></a>セキュリティ機能

Data Box Disk は、承認済みのエンティティ以外データの閲覧、変更、削除ができないよう徹底することによって、データ保護のための安全なソリューションを実現しています。 このソリューションのセキュリティ機能は、ディスクおよび関連するサービスを対象としたものであり、そこに格納されるデータのセキュリティを確保するものです。

### <a name="data-box-disk-protection"></a>Data Box Disk の保護

Data Box Disk は、次の機能によって保護されます。

- ディスクは、BitLocker の AES-128 ビット方式によって常時暗号化されます。
- 安全なディスク更新機能が使用されます。
- ディスクはロックされた状態で出荷され、ロックの解除は Data Box Disk のロック解除ツールでしか行えません。 ロック解除ツールは Data Box Disk のサービス ポータルで利用できます。

### <a name="data-box-disk-data-protection"></a>Data Box Disk のデータ保護

Data Box Disk に対する入出力データは、次の機能によって保護されます。

- データは常時 BitLocker で暗号化されます。
- Azure へのアップロードが完了したデータは、ディスクから確実に消去されます。 データの消去は、NIST 800-88r1 標準に準拠しています。

### <a name="data-box-service-protection"></a>Data Box サービスの保護

Data Box サービスは、次の機能によって保護されます。

- Data Box Disk サービスにアクセスするには、Data Box Disk を含む Azure サブスクリプションを会社が保有している必要があります。 Azure ポータルから利用できる機能は、保有するサブスクリプションによって決まります。
- Data Box サービスは Azure でホストされるため、Azure のセキュリティ機能によって保護されます。 Microsoft Azure のセキュリティ機能の詳細については、「 [Microsoft Azure トラスト センター](https://www.microsoft.com/TrustCenter/Security/default.aspx)」をご覧ください。
- このサービスでは、ディスクのロック解除にパスキーが使用されます。ディスクのパスキーは、Data Box Disk に格納されます。 
- Data Box Disk サービスは、その注文の詳細と状態を格納します。 この情報は、注文が削除されると削除されます。

## <a name="managing-personal-data"></a>個人データの管理

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Azure Data Box Disk は、同サービスにおける次の主要なインスタンスに個人情報を収集して表示します。

- **通知設定** - お客様は、発注の際、ユーザーのメール アドレスを通知設定で構成することになります。 この情報は管理者が閲覧できます。 この情報は、ジョブが最終状態に達するか、お客様が注文を削除すると、サービスによって削除されます。

- **注文の詳細** - 発注後、ユーザーの配送先住所、メール、連絡先情報が Azure portal に格納されます。 たとえば、次の情報が保存の対象となります。

  - 連絡先の名前
  - 電話番号
  - Email
  - 番地
  - City
  - 郵便番号
  - State
  - 国/地方/地域
  - ドライブ ID
  - 運送業者のアカウント番号
  - 配送追跡番号

    注文の詳細は、ジョブが完了するか、お客様が注文を削除すると、Data Box サービスによって削除されます。

- **配送先住所** - Data Box サービスは、注文を受けると、サード パーティの運送業者 (UPS、DHL など) に配送先住所を提供します。 

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。


## <a name="next-steps"></a>次のステップ

- [Data Box Disk の要件](data-box-disk-system-requirements.md)を確認する。
- [Data Box Disk の制限事項](data-box-disk-limits.md)を理解する。
- Azure portal で [Azure Data Box Disk](data-box-disk-quickstart-portal.md) をすばやく展開する。
