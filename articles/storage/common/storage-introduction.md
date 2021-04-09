---
title: Azure Storage の概要 - Azure のクラウド ストレージ | Microsoft Docs
description: コア Azure Storage プラットフォームは、Microsoft のクラウド ストレージ ソリューションです。 Azure Storage は、高可用性、セキュリティ、持続性、高度なスケーラビリティ、および冗長性を備えた、データ オブジェクト向けのストレージを提供します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b49e780714bd5e0662c6cb7ef208c473a8b45ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498151"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>コア Azure Storage サービスの概要

Azure Storage プラットフォームは、最新のデータ ストレージ シナリオのための Microsoft のクラウド ストレージ ソリューションです。 コア ストレージ サービスは、データ オブジェクトのための高度にスケーラブルなオブジェクト ストア、Azure 仮想マシン (VM) のためのディスク ストレージ、クラウドのためのファイル システム サービス、信頼できるメッセージングのためのメッセージング ストア、および NoSQL ストアを提供します。 これらのサービスには次の特徴があります。

- **持続性と高可用性。** 冗長性により、一時的なハードウェア障害が発生した場合でも、データが安全に保たれます。 地域的な災害または自然災害から保護するために、データセンターまたは地理的リージョンにまたがってデータをレプリケートすることもできます。 この方法でレプリケートされたデータは、予期しない停止が発生した場合でも高可用性を維持します。
- **セキュリティ保護。** Azure Storage アカウントに書き込まれたすべてのデータがサービスによって暗号化されます。 Azure Storage では、データにアクセスできるユーザーをきめ細かく制御できます。
- **スケーラブル。** Azure Storage には、今日のアプリケーションのデータ ストレージとパフォーマンスに関するニーズを満たすために、高度にスケーラブルな設計が採用されています。
- **マネージド。** Azure では、お客様に代わって、ハードウェアのメンテナンス、更新プログラム、および重大な問題への対処が行われます。
- **アクセス可能。** Azure Storage 内のデータには、世界中のどこからでも HTTP または HTTPS 経由でアクセスできます。 Microsoft では、.NET、Java、Node.js、Python、PHP、Ruby、Go などのさまざまな言語の Azure Storage 用のクライアント ライブラリと、成熟した REST API を提供しています。 Azure Storage では、Azure PowerShell または Azure CLI によるスクリプトの実行がサポートされます。 また、Azure Portal と Azure Storage Explorer により、データを操作するための使いやすい視覚的ソリューションが提供されます。  

## <a name="core-storage-services"></a>コア ストレージ サービス

Azure Storage プラットフォームには、次のデータ サービスが含まれています。

- [Azure BLOB](../blobs/storage-blobs-introduction.md):テキストおよびバイナリ データのための高度にスケーラブルなオブジェクト ストア。 また、Data Lake Storage Gen2 によるビッグ データ分析のサポートも含まれています。
- [Azure Files](../files/storage-files-introduction.md):クラウドまたはオンプレミスのデプロイ用のマネージド ファイル共有。
- [Azure キュー](../queues/storage-queues-introduction.md):アプリケーション コンポーネント間の信頼性の高いメッセージングのためのメッセージング ストア。
- [Azure テーブル](../tables/table-storage-overview.md):構造化データのスキーマレス ストレージのための NoSQL ストア。
- [Azure ディスク](../../virtual-machines/managed-disks-overview.md): Azure VM のためのブロック レベルのストレージ ボリューム。

それぞれのサービスには、ストレージ アカウントを通じてアクセスします。 作業を開始するには、「[ストレージ アカウントの作成](storage-account-create.md)」を参照してください。

## <a name="example-scenarios"></a>シナリオ例

次の表では、ファイル、BLOB、ディスク、キュー、テーブルを比較し、それぞれのシナリオの例を示しています。

| 機能 | 説明 | 使用する場合 |
|--------------|-------------|-------------|
| **Azure Files** |業界標準のサーバー メッセージ ブロック (SMB) プロトコル経由でどこからでもアクセスできるフル マネージドのクラウド ファイル共有を提供します。<br><br>Azure ファイル共有は、Windows、Linux、macOS のクラウドまたはオンプレミスのデプロイからマウントできます。 | 既に、ネイティブなファイル システム API を使用して、Azure で実行されている他のアプリケーションとの間でデータを共有しているアプリケーションをクラウドに "リフト アンド シフト" したい場合。<br/><br/>オンプレミスのファイル サーバーまたは NAS デバイスを置き換えるか、またはそれを補完したい場合。<br><br> 多くの仮想マシンからアクセスする必要のある開発ツールとデバッグ ツールを格納する。 |
| **Azure BLOB** | 非構造化データを大規模にブロック BLOB に格納してアクセスできるようにします。<br/><br/>エンタープライズ ビッグ データ分析ソリューション用の [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) もサポートされています。 | アプリケーションでストリーミングとランダム アクセスのシナリオに対応する。<br/><br/>アプリケーションのデータにどこからでもアクセスできるようにする。<br/><br/>Azure 上にエンタープライズ Data Lake を構築し、ビッグ データ分析を実行する。 |
| **Azure ディスク** | データを永続的に格納し、アタッチされた仮想ハード ディスクからアクセスできるようにします。 | ネイティブなファイル システム API を使用して永続ディスクとの間のデータの読み取りと書き込みを行っているアプリケーションを "リフト アンド シフト" したい場合。<br/><br/>外部 (ディスクがアタッチされている仮想マシンの外) からのアクセスが不要なデータを格納する。 |
| **Azure キュー** | アプリケーション コンポーネント間の非同期メッセージ キューを可能にします。 | アプリケーション コンポーネントを分離し、非同期メッセージングを使用してそれらの間の通信を行いたい場合。<br><br>Queue Storage および Service Bus キューをそれぞれいつ使用するかに関するガイダンスについては、「[Storage キューと Service Bus キューの比較](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)」を参照してください。 |
| **Azure テーブル** | 構造化された NoSQL データをクラウドに格納し、スキーマなしの設計でキー/属性ストアを実現できるようにします。 | Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータのような柔軟なデータセットを格納したい場合。 <br/><br/>テーブル ストレージと Azure Cosmos DB Table API をそれぞれいつ使用するかに関するガイダンスについては、「[Azure Cosmos DB Table API と Azure Table Storage を使用した開発](../../cosmos-db/table-support.md)」を参照してください。 |

## <a name="blob-storage"></a>BLOB ストレージ

Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 BLOB ストレージは、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。

BLOB ストレージは、次の目的に最適です。

- 画像またはドキュメントをブラウザーに直接配信する。
- 分散アクセス用にファイルを格納する。
- ビデオおよびオーディオをストリーミング配信する。
- バックアップと復元、ディザスター リカバリー、アーカイブのためのデータを格納する。
- オンプレミス サービスまたは Azure ホステッド サービスで分析するデータを格納する。

BLOB ストレージ内のオブジェクトには、世界中のどこからでも HTTP または HTTPS 経由でアクセスできます。 ユーザーまたはクライアント アプリケーションは、URL、[Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](/powershell/module/azure.storage)、[Azure CLI](/cli/azure/storage)、または Azure Storage クライアント ライブラリを介して BLOB にアクセスできます。 ストレージ クライアント ライブラリは、[.NET](/dotnet/api/overview/azure/storage)、[Java](/java/api/overview/azure/storage)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/)、[PHP](https://azure.github.io/azure-storage-php/)、[Ruby](https://azure.github.io/azure-storage-ruby) など、さまざまな言語で利用できます。

Blob Storage の詳細については、「[Blob Storage の概要](../blobs/storage-blobs-introduction.md)」をご覧ください。

## <a name="azure-files"></a>Azure Files

[Azure Files](../files/storage-files-introduction.md) では、標準的なサーバー メッセージ ブロック (SMB) プロトコルを使用してアクセスできる可用性の高いネットワーク ファイル共有を設定することができます。 つまり、複数の VM が、読み取りアクセス権と書き込みアクセス権の両方を使用して同じファイルを共有できます。 また、REST インターフェイスまたはストレージ クライアント ライブラリを使用してファイルを読み取ることもできます。

Azure Files と企業のファイル共有上のファイルとの違いの 1 つは、世界中のどこからでも、ファイルを指し、共有アクセス署名 (SAS) トークンを含む URL を使用して、ファイルにアクセスできることです。 SAS トークンは生成することができます。SAS トークンを使用することで、特定の期間にわたってプライベートな資産への特定のアクセスを許可できます。

ファイル共有は、多くの一般的なシナリオで使用できます。

- 多くのオンプレミスのアプリケーションでファイル共有が使用されます。 この機能により、データを共有するそれらのアプリケーションを Azure に簡単に移行できます。 オンプレミスのアプリケーションで使用されているのと同じドライブ文字にファイル共有をマウントすることにより、ファイル共有にアクセスするアプリケーションの部分は、(変更があっても) 最小限の変更で動作します。

- 構成ファイルをファイル共有に格納して、複数の VM からアクセスできます。 グループ内の複数の開発者によって使用されるツールやユーティリティをファイル共有に格納できるため、だれでもそれらを見つけ、同じバージョンを使用することができます。

- ファイル共有に書き込んだ後で処理または分析できるデータの例として、リソース ログ、メトリック、およびクラッシュ ダンプの 3 つが挙げられます。

Azure Files の詳細については、「[Azure Files の概要](../files/storage-files-introduction.md)」を参照してください。

一部の SMB 機能はクラウドでは利用できません。 詳細については、「[Features not supported by the Azure File service (Azure File Service でサポートされていない機能)](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)」を参照してください。

## <a name="queue-storage"></a>ストレージ

Azure Queue サービスは、メッセージの保管と取得に使用します。 キューに許容されるメッセージ サイズは最大 64 KB で、1 つのキューに数百万件のメッセージを格納することができます。 一般に、非同期的に処理するメッセージのリストを格納するのがキューの用途となります。

たとえば、画像をアップロードしてそれぞれにサムネイルを作成する機能をユーザーに提供するとしましょう。 もちろん画像のアップロードの際、サムネイルを作成するまでユーザーに待ってもらうことはできます。 しかし、その代わりの方法として、キューを使うこともできます。 ユーザーからのアップロードが完了したら、メッセージをキューに書き込みます。 その後、Azure Functions でキューからメッセージを取得して、サムネイルを作成するのです。 この処理では、各段階を個別にスケーリングすることができるので、用途に応じたチューニングの自由度が高まります。

Azure キューの詳細については、「[キューの概要](../queues/storage-queues-introduction.md)」を参照してください。

## <a name="table-storage"></a>テーブル ストレージ

Azure Cosmos DB に、新たに Azure Table Storage が加わりました。 Azure Table Storage のドキュメントについては、「[Azure Table Storage の概要](../tables/table-storage-overview.md)」を参照してください。 既存の Azure Table Storage サービスに加えて、スループットに最適化されたテーブル、グローバルな分散、自動セカンダリ インデックスを利用できる新しい Azure Cosmos DB Table API プランが追加されています。 新しい Premium エクスペリエンスの詳細とその試行方法については、[Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md)に関するページを参照してください。

テーブル ストレージの詳細については、「[Azure Table Storage の概要](../tables/table-storage-overview.md)」を参照してください。

## <a name="disk-storage"></a>Disk Storage

Azure マネージド ディスクは仮想ハード ディスク (VHD) です。 オンプレミス サーバーの物理ディスクと似ていますが、仮想化されたディスクと考えることができます。 Azure マネージド ディスクは、Azure 内のランダムな IO ストレージ オブジェクトであるページ BLOB として格納されます。 マネージド ディスクを "マネージド" と呼ぶ理由は、ページ BLOB、BLOB コンテナー、および Azure ストレージ アカウントを抽象化したものであるためです。 マネージド ディスクでは、ディスクをプロビジョニングするだけで、後の管理は Azure が実行します。

マネージド ディスクの詳細については、「[Azure マネージド ディスクの概要](../../virtual-machines/managed-disks-overview.md)」をご覧ください。

## <a name="types-of-storage-accounts"></a>ストレージ アカウントの種類

Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 ストレージ アカウントの種類の詳細については、[Azure Storage アカウントの概要](storage-account-overview.md)に関するページを参照してください。

## <a name="secure-access-to-storage-accounts"></a>ストレージ アカウントへのアクセスをセキュリティで保護する

Azure Storage に対するすべての要求が承認される必要があります。 Azure Storage では、次の認証方法がサポートされています。

- **BLOB とキュー データ用の Azure Active Directory (Azure AD) 統合。** Azure Storage では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用した BLOB サービスと Queue サービスに対する Azure AD の認証と承認をサポートされています。 優れたセキュリティと使いやすさのため、Azure AD による要求の承認をお勧めします。 詳細については、[Azure Active Directory を使用した Azure BLOB およびキューへのアクセスの承認](storage-auth-aad.md)に関するページを参照してください。
- **SMB を使用した Azure Files の Azure Active Directory 認証。** Azure Files では、Azure Active Directory Domain Services (Azure AD DS) またはオンプレミスの Active Directory Domain Services (プレビュー) のどちらかを使用した SMB (サーバー メッセージ ブロック) 経由の ID ベースの承認がサポートされます。 ドメインに参加している Windows VM は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。 詳細については、「[SMB アクセスに対する Azure Files ID ベース認証サポートの概要](../files/storage-files-active-directory-overview.md)」および「[Azure Files のデプロイの計画](../files/storage-files-planning.md#identity)」を参照してください。
- **共有キーによる承認。** Azure Storage の BLOB、ファイル、キュー、テーブルの各サービスでは、共有キーによる承認がサポートされます。 共有キーによる承認を使用するクライアントは、ストレージ アカウントのアクセス キーを使用して署名されたすべての要求を含むヘッダーを渡します。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key)に関するページを参照してください。
- **Shared Access Signature (SAS) を使用した承認。** Shared Access Signature (SAS) は、ストレージ リソースの URI に追加できるセキュリティ トークンを含む文字列です。 セキュリティ トークンには、アクセスの許可や間隔などの制約をカプセル化します。 詳細については、[共有アクセス署名 (SAS) の使用](storage-sas-overview.md)に関するページを参照してください。
- **コンテナーおよび BLOB への匿名アクセス。** コンテナーとその BLOB は一般公開されることがあります。 コンテナーまたは BLOB をパブリックとして指定すると、認証が不要になり、すべてのユーザーが匿名でリソースを読み取ることができます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/anonymous-read-access-configure.md)」を参照してください。

## <a name="encryption"></a>暗号化

コア ストレージ サービスで使用できる暗号化には、基本的に次の 2 種類があります。 セキュリティと暗号化の詳細については、「[Azure Storage セキュリティ ガイド](../blobs/security-recommendations.md)」を参照してください。

### <a name="encryption-at-rest"></a>保存時の暗号化

Azure Storage の暗号化では、組織のセキュリティとコンプライアンスの責務を果たせるように、データの保護とセーフガードが行われます。 Azure Storage では、データをストレージ アカウントに保存する前に自動的に暗号化し、取得する前に復号化します。 暗号化、復号化、キー管理の各プロセスは、ユーザーに対して透過的です。 お客様は、Azure Key Vault を使用して自分のキーを管理することを選択することもできます。 詳細については、「[保存データ向け Azure ストレージの暗号化](storage-service-encryption.md)」をご覧ください。

### <a name="client-side-encryption"></a>クライアント側暗号化

Azure Storage のクライアント ライブラリには、クライアント ライブラリのデータをネット上で送信する前に暗号化し、応答を復号化する方法が提供されています。 クライアント側の暗号化によって暗号化されたデータは、保存時も Azure Storage によって暗号化されます。 クライアント側の暗号化の詳細については、[Azure Storage での .NET によるクライアント側の暗号化](storage-client-side-encryption.md)に関するページを参照してください。

## <a name="redundancy"></a>冗長性

データの持続性を確保するために、Azure Storage はデータの複数のコピーを格納します。 冗長性オプションは、ストレージ アカウントを設定するときに選択します。 詳細については、「[Azure Storage の冗長性](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。

## <a name="transfer-data-to-and-from-azure-storage"></a>Azure Storage との間でデータを転送する

Azure Storage へのデータの移動または Azure Storage からのデータの移動には、いくつかのオプションがあります。 どのオプションを選択するかは、データセットとネットワーク帯域幅のサイズによって異なります。 詳細については、「[データ転送用の Azure ソリューションを選択する](storage-choose-data-transfer-solution.md)」を参照してください。

## <a name="pricing"></a>価格

データの格納方法とアクセス方法を決めるときには、それに伴うコストも考慮する必要があります。 詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するページを参照してください。

## <a name="storage-apis-libraries-and-tools"></a>ストレージ API、ライブラリ、ツール

ストレージ アカウント内のリソースには、HTTP/HTTPS 要求を作成できる任意の言語でアクセスできます。 さらに、コア Azure Storage サービスには、いくつかの一般的な言語のためのプログラミング ライブラリが用意されています。 これらのライブラリを使用すると、同期呼び出しと非同期呼び出し、操作のバッチ処理、例外管理、自動再試行、実行動作などの詳細が処理され、Azure Storage の使用に関するさまざまな側面が簡略化されます。 ライブラリは、次の言語およびプラットフォーム用が現在提供されており、その他についても準備中です。

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage データ API およびライブラリのリファレンス

- [Azure Storage REST API](/rest/api/storageservices/)
- [.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)
- [Java/Android 用 Azure Storage クライアント ライブラリ](/java/api/overview/azure/storage)
- [Node.js 用 Azure Storage クライアント ライブラリ](/javascript/api/overview/azure/storage-overview)
- [Python 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-python)
- [PHP 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-php)
- [Ruby 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-ruby)
- [C++ 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage 管理 API およびライブラリのリファレンス

- [ストレージ リソース プロバイダー REST API](/rest/api/storagerp/)
- [.NET 用 Storage Resource Provider クライアント ライブラリ](/dotnet/api/overview/azure/storage/management)
- [Storage Service Management REST API (Classic)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage データ移動 API およびライブラリのリファレンス

- [Storage Import/Export Service REST API](/rest/api/storageimportexport/)
- [.NET 用 Storage Data Movement クライアント ライブラリ](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>ツールとユーティリティ

- [ストレージ用 Azure PowerShell コマンドレット](/powershell/module/az.storage)
- [ストレージ用 Azure CLI コマンドレット](/cli/azure/storage)
- [AzCopy コマンド ライン ユーティリティ](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
- [Azure Storage 用 Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>次のステップ

コア Azure Storage サービスを稼働させるには、[ストレージ アカウントの作成](storage-account-create.md)に関するページを参照してください。