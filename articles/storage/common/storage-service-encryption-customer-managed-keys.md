---
title: "ユーザーが管理する Azure Key Vault キーを Azure Storage Service Encryption に使用する | Microsoft Docs"
description: "Azure Storage Service Encryption 機能を使用すると、データを格納するときにサービス側で Azure Blob Storage を暗号化し、データを取得するときに暗号化を解除することができます。この機能には、ユーザーが管理するキーを使用することができます。"
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する

Microsoft Azure はお客様がそのデータを保護し、組織のセキュリティとコンプライアンスの必達目標を満たせるよう支援するために取り組んでいます。  保存データを保護する方法の 1 つが Storage Service Encryption (SSE) で、ストレージに書き込むときに自動的にデータを暗号化し、取得するときにデータの暗号化を解除することができます。 暗号化と解読は自動的かつ完全に透過的に行われ、現在最も強力なブロック暗号の 1 つである 256 ビットの [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)が使用されます。

SSE には Microsoft が管理する暗号化キーのほか、ユーザー独自の暗号化キーを使うことができます。 この記事では、後者について説明します。 Microsoft が管理するキーまたは SSE 全般について詳しくは、「[Storage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption) ](../storage-service-encryption.md)」をご覧ください。

ユーザー独自の暗号化キーの使用に対応するため、BLOB ストレージの SSE には Azure Key Vault (AKV) が統合されています。 独自の暗号化キーを作成して AKV に格納したり、AKV の API を使って暗号化キーを生成したりすることができます。 AKV では、独自のキーの管理と制御を行えることに加え、キーの使用状況を監査することもできます。 

独自のキーを作成する利点としては、 これにより、アクセス制御を作成、回転、無効化、および定義できるようになり、柔軟性が高まります。 また、データを保護するために使用される暗号化キーを監査することもできます。

## <a name="sse-with-customer-managed-keys-preview"></a>ユーザーが管理するキーを使用した SSE (プレビュー)

現在、この機能はプレビュー段階にあります。 この機能を使用するには、新しいストレージ アカウントを作成する必要があります。 新しいキー コンテナーとキーを作成するか、既にあるキー コンテナーとキーを使用することができます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。

プレビューへの参加を希望される場合は、[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) までご連絡ください。プレビューに参加するための特殊なリンクをお送りします。

詳細については、[よく寄せられる質問](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest)のセクションを参照してください。

> [!IMPORTANT]
> この記事の手順に進む前に、プレビューにサインアップする必要があります。 プレビューにアクセスしないとポータルでこの機能を有効にできません。

## <a name="getting-started"></a>Getting Started (概要)
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>手順 1: [新しいストレージ アカウントを作成する](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>手順 2: 暗号化を有効にする
ストレージ アカウントの SSE は、[Azure Portal](https://portal.azure.com) を使用して有効にすることができます。 ストレージ アカウントの [設定] ブレードで [Blob service] セクション (下図参照) を探し、[暗号化] をクリックします。

![暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Blob service の SSE を有効にする*

ストレージ アカウントの Storage Service Encryption をプログラムを使用して有効または無効にする場合は、[Azure Storage Resource Provider REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN)、[.NET 用 Storage Resource Provider クライアント ライブラリ](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN)、[Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0)、または [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli) を使用します。

この画面で、[独自のキーを使用する] チェック ボックスが表示されない場合、プレビューの使用がまだ承認されていません。 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 宛てに電子メールで承認依頼を送信してください。

![プレビューの暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

既定では、Microsoft によって管理されたキーが SSE に使用されます。 独自のキーを使用するには、このチェック ボックスをオンにしてください。 そのうえで独自のキーの URI を指定するか、選択画面からキーとキー コンテナーを選びます。

## <a name="step-3-select-your-key"></a>手順 3: キーを選択する

![[独自のキーを使用する] 暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![[キー URI を入力] 暗号化オプションが表示されたポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

キー コンテナーへのアクセス権がストレージ アカウントにない場合は、Azure PowerShell で以下のコマンドを実行することによって、必要なキー コンテナーへのアクセス権をストレージ アカウントに付与できます。

![キー コンテナーへのアクセスが拒否されたことを示すポータルのスクリーンショット](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

アクセス権の付与は、Azure Portal で行うこともできます。Azure Portal の [Azure Key Vault] に移動して、ストレージ アカウントにアクセス権を付与します。

## <a name="step-4-copy-data-to-storage-account"></a>手順 4: ストレージ アカウントにデータをコピーする
新しいストレージ アカウントにデータを暗号化した状態で転送する方法については、[「torage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption)」の「Getting Started (概要)」の手順 3](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account) をご覧ください。

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>手順 5: 暗号化データの状態を照会する
暗号化データの状態を照会する方法については、[「torage Service Encryption for Data at Rest (保存データに対する Storage Service Encryption)」の「Getting Started (概要)」の手順 4](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data) をご覧ください。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Storage Service Encryption for Data at Rest に関してよく寄せられる質問
**Q: Premium Storage を使用しています。ユーザーが管理するキーで SSE を使うことはできますか。**

A: はい。Standard Storage と Premium Storage のどちらでも、Microsoft が管理するキーとユーザーが管理するキーを SSE にご利用いただけます。 

**Q: ユーザーが管理するキーによる SSE を有効にした新しいストレージ アカウントを Azure PowerShell と Azure CLI で作成できますか。**

A: はい。

**Q: ユーザーが管理するキーによる SSE を有効にすると、Azure Storage の料金はどれくらい増えますか。**

A: Azure Key Vault の使用に関連した料金が発生します。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/en-us/pricing/details/key-vault/)」を参照してください。 SSE の使用に関して、それ以上の料金は発生しません。

**Q: 暗号化キーへのアクセスを取り消すことはできますか?**

A: はい。いつでもアクセスを取り消すことができます。 キーへのアクセスは、いくつかの方法で取り消すことができます。 詳細については、[Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) に関するページと [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault) に関するページを参照してください。 アクセスを取り消すと、Azure Storage がアカウント暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全 BLOB へのアクセスが事実上ブロックされます。

**Q: ストレージ アカウントとキーを別々のリージョンに作成してもかまいませんか。**

A: いいえ。ストレージ アカウントとキー コンテナー/キーは、同じリージョンに存在する必要があります。 

**Q: ユーザーが管理するキーによる SSE をストレージ アカウントの作成時に有効にすることはできますか。**

A: いいえ。 ストレージ アカウントの作成時に SSE を有効にする場合、使用できるのは Microsoft が管理するキーのみとなります。 ユーザーが管理するキーの使用を希望される場合は、ストレージ アカウントのプロパティを更新する必要があります。 REST またはいずれかのストレージ クライアント ライブラリを使用してプログラムでストレージ アカウントを更新するか、ストレージ アカウントの作成後にそのプロパティを Azure Portal で更新してください。

**Q: ユーザーが管理するキーによる SSE を使用している状態で暗号化を無効にすることはできますか。**

A: いいえ。ユーザーが管理するキーによる SSE を使用している状態で暗号化を無効にすることはできません。 暗号化を無効にするには、Microsoft が管理するキーを使用するように切り替える必要があります。 この設定は、Azure Portal または PowerShell で行うことができます。

**Q: 新しいストレージ アカウントを作成すると、SSE は既定で有効になりますか?**

A: SSE は既定では有効になりません。Azure Portal を使用して有効にできます。 Storage Resource Provider REST API を使用してプログラムでこの機能を有効にすることもできます。 

**Q: ストレージ アカウントで暗号化を有効にできません。**

A: それは Resource Manager ストレージ アカウントですか。 クラシック ストレージ アカウントはサポートされていません。 また、ユーザーが管理するキーによる SSE は、新しく作成された Resource Manager ストレージ アカウントでのみ有効にすることができます。

**Q: ユーザーが管理するキーによる SSE は特定のリージョンでしか使用できないのでしょうか。**

A: このプレビューでは、SSE の利用は一部のリージョンの BLOB ストレージに限られます。 このプレビュー機能に関する利用の可否と詳細については、[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 宛てにメールでお問い合わせください。 

**Q: 問題やフィードバックの連絡先はどこですか?**

A: Storage Service Encryption に関する問題は、 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。 

## <a name="next-steps"></a>次のステップ

*   安全なアプリケーション開発を支援する包括的なセキュリティ機能の詳細については、[Storage セキュリティ ガイド](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)に関するページをご覧ください。
*   Azure Key Vault の概要については、「[Azure Key Vault とは](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)」を参照してください。
*   Azure Key Vault の概要については、[Azure Key Vault の概要](../../key-vault/key-vault-get-started.md)に関するページを参照してください。

