---
title: 暗号化 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool で公開されている脅威に対する暗号化の軽減策について説明します。 軽減策に関する情報とコード例をご覧ください。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587181"
---
# <a name="security-frame-cryptography--mitigations"></a>セキュリティ フレーム:暗号化 | 軽減策 

| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Web アプリケーション** | <ul><li>[承認済みの対称ブロック暗号とキー長のみを使用する](#cipher-length)</li><li>[対称暗号に承認済みのブロック暗号モードと初期化ベクトルを使用する](#vector-ciphers)</li><li>[承認済みの非対称アルゴリズム、キー長、パディングを使用する](#padding)</li><li>[承認済みの乱数ジェネレーターを使用する](#numgen)</li><li>[対称ストリーム暗号は使用しない](#stream-ciphers)</li><li>[承認済みの MAC/HMAC/キー付きハッシュ アルゴリズムを使用する](#mac-hash)</li><li>[承認済みの暗号化ハッシュ関数のみを使用する](#hash-functions)</li></ul> |
| **[データベース]** | <ul><li>[強力な暗号化アルゴリズムを使用してデータベース内のデータを暗号化する](#strong-db)</li><li>[SSIS パッケージを暗号化し、デジタル署名する必要がある](#ssis-signed)</li><li>[重要なセキュリティ保護可能なデータベース リソースにデジタル署名を追加する](#securables-db)</li><li>[SQL Server EKM を使用して暗号化キーを保護する](#ekm-keys)</li><li>[暗号化キーがデータベース エンジンに公開されないようにする必要がある場合は AlwaysEncrypted 機能を使用する](#keys-engine)</li></ul> |
| **IoT デバイス** | <ul><li>[IoT デバイスで暗号化キーを安全に保存する](#keys-iot)</li></ul> | 
| **IoT クラウド ゲートウェイ** | <ul><li>[IoT Hub に対する認証用に十分な長さのランダムな対称キーを生成する](#random-hub)</li></ul> | 
| **Dynamics CRM モバイル クライアント** | <ul><li>[PIN の使用を要求し、リモート ワイプを許可するデバイス管理ポリシーが設定されていることを確認する](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook クライアント** | <ul><li>[PIN/パスワード/自動ロックを要求し、すべてのデータを暗号化する (BitLocker など) デバイス管理ポリシーが設定されていることを確認する](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Identity Server を使用するときは、署名キーがロールオーバーされていることを確認する](#rolled-server)</li><li>[Identity Server で暗号強度が高いクライアント ID とクライアント シークレットが使用されていることを確認する](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>承認済みの対称ブロック暗号とキー長のみを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>製品では、組織の暗号アドバイザーによって明示的に承認された対称ブロック暗号および関連付けられたキー長のみを使用する必要があります。 Microsoft での承認済みの対称アルゴリズムには、次のブロック暗号が含まれます。</p><ul><li>新しいコードでは、AES-128、AES-192、AES-256 が許容されます。</li><li>既存のコードとの下位互換性を確保するために、three-key 3DES が許容されます。</li><li>対称ブロック暗号を使用する製品では、次の点に注意してください。<ul><li>新しいコードでは、Advanced Encryption Standard (AES) が必須となります。</li><li>three-key Triple Data Encryption Standard (3DES) は、下位互換性を確保するために既存のコードで許容されます。</li><li>RC2、DES、2-key 3DES、DESX、Skipjack など、他のすべてのブロック暗号は、古いデータの復号化にのみ使用できます。暗号化に使用する場合は置き換える必要があります。</li></ul></li><li>対称ブロック暗号化アルゴリズムでは、128 ビットの最小キー長が必須となります。 新しいコードに推奨されるブロック暗号化アルゴリズムは AES だけです (AES-128、AES-192、AES-256 はすべて許容されます)。</li><li>現在、three-key 3DES は、既存のコードで既に使用されている場合には許容されますが、AES に移行することをお勧めします。 DES、DESX、RC2、Skipjack は安全と見なされなくなりました。 これらのアルゴリズムは、下位互換性を確保するために既存のデータの復号化にのみ使用できます。データは、推奨されるブロック暗号を使用して再暗号化する必要があります。</li></ul><p>対称ブロック暗号は、いずれも承認済みの暗号モードで使用する必要があることに注意してください。承認済みの暗号モードでは、適切な初期化ベクトル (IV) を使用する必要があります。 通常、適切な IV は乱数であり、定数値ではありません。</p><p>組織の暗号委員会によるレビュー後に、従来の暗号化アルゴリズムまたは未承認の暗号化アルゴリズムと短いキー長を、(新しいデータの書き込みではなく) 既存のデータの読み取りに使用することが認められる場合があります。 ただし、この要件の例外を申請する必要があります。 さらに、エンタープライズ デプロイでは、データの読み取りに脆弱な暗号が使用されているときに、製品で管理者に警告することを検討する必要があります。 このような警告は、説明的ですぐに実行できる内容にします。 グループ ポリシーによって、脆弱な暗号の使用を制御するのが適している場合もあります。</p><p>管理された暗号化方式の指定で許容される .NET のアルゴリズム (優先順)</p><ul><li>AesCng (FIPS に準拠している)</li><li>AuthenticatedAesCng (FIPS に準拠している)</li><li>AESCryptoServiceProvider (FIPS に準拠している)</li><li>AESManaged (FIPS に準拠していない)</li></ul><p>これらのアルゴリズムはいずれも、machine.config ファイルに変更を加えずに `SymmetricAlgorithm.Create` メソッドまたは `CryptoConfig.CreateFromName` メソッドを使用して指定することはできないことに注意してください。 また、.NET 3.5 より前のバージョンの .NET での AES の名前は `RijndaelManaged` であることにも注意してください。`AesCng` と `AuthenticatedAesCng` は CodePlex から入手できますが、基になる OS に CNG が必要です。</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>対称暗号に承認済みのブロック暗号モードと初期化ベクトルを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 対称ブロック暗号はいずれも承認済みの対称暗号モードで使用する必要があります。 承認済みのモードは CBC と CTS だけです。 具体的には、電子コード ブック (ECB) 動作モードは避ける必要があります。ECB を使用する場合は、組織の暗号委員会のレビューが必要となります。 OFB、CFB、CTR、CCM、GCM、またはその他の暗号化モードを使用する場合は、常に組織の暗号委員会によるレビューを受ける必要があります。 CTR などの "ストリーム暗号モード" のブロック暗号で同じ初期化ベクトル (IV) を再利用すると、暗号化されたデータが露呈する可能性があります。 また、対称ブロック暗号はいずれも適切な初期化ベクトル (IV) で使用する必要があります。 通常、適切な IV は暗号強度が高い乱数であり、定数値ではありません。 |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>承認済みの非対称アルゴリズム、キー長、パディングを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>禁止された暗号化アルゴリズムの使用は、製品のセキュリティに重大なリスクをもたらすので避ける必要があります。 製品では、組織の暗号委員会によって明示的に承認されている暗号化アルゴリズム、関連付けられたキー長、パディングのみを使用する必要があります。</p><ul><li>**RSA -** 暗号化、キー交換、署名に使用できます。 RSA 暗号化では、OAEP または RSA-KEM パディング モードのみを使用する必要があります。 既存のコードでは、互換性の確保のみを目的として、PKCS #1 v1.5 パディング モードを使用できます。 null パディングの使用は明示的に禁止されています。 新しいコードでは、2048 ビット以上のキーが必要です。 既存のコードは、組織の暗号委員会によるレビュー後に、下位互換性の確保のみを目的として、2048 ビット未満のキーをサポートできます。 1024 ビット未満のキーは、古いデータの復号化または検証にのみ使用できます。暗号化または署名操作に使用する場合は置き換える必要があります。</li><li>**ECDSA -** 署名にのみ使用できます。 新しいコードでは、256 ビット以上のキーを使用する ECDSA が必要です。 ECDSA ベースの署名では、NIST が承認した 3 つの曲線 (P-256、P-384、P521) のいずれかを使用する必要があります。 徹底的に分析された曲線は、組織の暗号委員会によるレビュー後にのみ使用できます。</li><li>**ECDH -** キー交換にのみ使用できます。 新しいコードでは、256 ビット以上のキーを使用する ECDH が必要です。 ECDH ベースのキー交換では、NIST が承認した 3 つの曲線 (P-256、P-384、P521) のいずれかを使用する必要があります。 徹底的に分析された曲線は、組織の暗号委員会によるレビュー後にのみ使用できます。</li><li>**DSA -** 組織の暗号委員会によるレビューおよび承認後に許容される場合があります。 セキュリティ アドバイザーに連絡して、組織の暗号委員会によるレビューのスケジュールを設定してください。 DSA の使用が承認された場合、長さが 2048 ビット未満のキーの使用を禁止する必要があることに注意してください。 CNG は、Windows 8 以降で 2048 ビット以上のキー長をサポートします。</li><li>**Diffie-Hellman -** セッション キーの管理にのみ使用できます。 新しいコードでは、2048 ビット以上のキー長が必要です。 既存のコードは、組織の暗号委員会によるレビュー後に、下位互換性の確保のみを目的として、2048 ビット未満のキー長をサポートできます。 1024 ビット未満のキーは使用できません。</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>承認済みの乱数ジェネレーターを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>製品では、承認済みの乱数ジェネレーターを使用する必要があります。 そのため、C ランタイム関数の rand などの擬似ランダム関数、.NET Framework の System.Random クラス、GetTickCount などのシステム関数をコードで使用することはできません。 双対楕円曲線乱数ジェネレーター (DUAL_EC_DRBG) アルゴリズムの使用は禁止されています。</p><ul><li>**CNG -** BCryptGenRandom (呼び出し元が 0 より大きい IRQL (つまり、PASSIVE_LEVEL) で実行されている場合を除き、BCRYPT_USE_SYSTEM_PREFERRED_RNG フラグを使用することをお勧めします)。</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64 -** RtlGenRandom (新しい実装では、BCryptGenRandom または CryptGenRandom を使用する必要があります) * rand_s * SystemPrng (カーネル モードの場合)</li><li>**.NET -** RNGCryptoServiceProvider または RNGCng</li><li>**Windows ストア アプリ -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom または .GenerateRandomNumber</li><li>**Apple OS X (10.7 以降)/iOS(2.0 以降) -** int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t \*bytes )</li><li>**Apple OS X (10.7 より前)-** /dev/random を使って乱数を取得します</li><li>**Java(Google Android Java コードを含む) -** java.security.SecureRandom クラス。 Android 4.3 (Jelly Bean) の場合、開発者は Android の推奨回避策に従い、アプリケーションを更新して、/dev/urandom または /dev/random のエントロピーで PRNG を明示的に初期化する必要があります。</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>対称ストリーム暗号は使用しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 対称ストリーム暗号 (RC4 など) は使用しないでください。 製品では、対称ストリーム暗号ではなく、ブロック暗号を使用する必要があります。具体的には、キー長が 128 ビット以上の AES を使用します。 |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>承認済みの MAC/HMAC/キー付きハッシュ アルゴリズムを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>製品では、承認済みのメッセージ認証コード (MAC) アルゴリズムまたはハッシュ ベース メッセージ認証コード (HMAC) アルゴリズムのみを使用する必要があります。</p><p>メッセージ認証コード (MAC) はメッセージに添付される情報の一部です。MAC により、メッセージの受信者は、秘密キーを使用して送信元の信頼性とメッセージの整合性を確認することが可能になります。 基になるすべてのハッシュ アルゴリズムまたは対称暗号化アルゴリズムも使用が承認されていれば、ハッシュ ベースの MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) または[ブロック暗号ベースの MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) の使用が許容されます。現時点では、これには HMAC-SHA2 関数 (HMAC-SHA256、HMAC-SHA384、HMAC-SHA512) と、ブロック暗号ベースの MAC である CMAC/OMAC1 および OMAC2 (これらは AES に基づいています) が含まれます。</p><p>プラットフォームの互換性を確保するために、HMAC-SHA1 の使用が許容される場合もありますが、この手順の例外を申請し、組織の暗号委員会のレビューを受ける必要があります。 HMAC を 128 ビット未満に切り捨てることは許可されていません。 顧客独自の方法を使用したキーとデータのハッシュは承認されておらず、使用前に組織の暗号委員会によるレビューを受ける必要があります。</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>承認済みの暗号化ハッシュ関数のみを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>製品では、SHA-2 ファミリのハッシュ アルゴリズム (SHA256、SHA384、SHA512) を使用する必要があります。 短い MD5 ハッシュを念頭に置いて設計されたデータ構造に合わせるために 128 ビット出力長が必要な場合など、短いハッシュが必要な場合、製品チームは SHA2 ハッシュのいずれか (通常は SHA256) を切り捨てることができます。 SHA384 は SHA512 の切り捨てられたバージョンです。 セキュリティ上の目的で暗号化ハッシュを切り捨てる場合、128 ビット未満に切り捨てることは許可されていません。 新しいコードでは、MD2、MD4、MD5、SHA-0、SHA-1、RIPEMD の各ハッシュ アルゴリズムは使用しないでください。 これらのアルゴリズムでは、ハッシュの競合が計算的に可能であるため、実質的にアルゴリズムを破ることになります。</p><p>管理された暗号化方式の指定で許容される .NET のハッシュ アルゴリズムは次のとおりです (優先順)。</p><ul><li>SHA512Cng (FIPS に準拠している)</li><li>SHA384Cng (FIPS に準拠している)</li><li>SHA256Cng (FIPS に準拠している)</li><li>SHA512Managed (FIPS に準拠していない) (HashAlgorithm.Create または CryptoConfig.CreateFromName の呼び出しで、アルゴリズム名として SHA512 を使用する)</li><li>SHA384Managed (FIPS に準拠していない) (HashAlgorithm.Create または CryptoConfig.CreateFromName の呼び出しで、アルゴリズム名として SHA384 を使用する)</li><li>SHA256Managed (FIPS に準拠していない) (HashAlgorithm.Create または CryptoConfig.CreateFromName の呼び出しで、アルゴリズム名として SHA256 を使用する)</li><li>SHA512CryptoServiceProvider (FIPS に準拠している)</li><li>SHA256CryptoServiceProvider (FIPS に準拠している)</li><li>SHA384CryptoServiceProvider (FIPS に準拠している)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>強力な暗号化アルゴリズムを使用してデータベース内のデータを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [暗号化アルゴリズムの選択](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **手順** | 暗号化アルゴリズムによって定義されるデータ変換は、未承認ユーザーが容易に復元できないものです。 SQL Server では、管理者と開発者は、DES、Triple DES、TRIPLE_DES_3KEY、RC2、RC4、128 ビット RC4、DESX、128 ビット AES、192 ビット AES、256 ビット AES など、多数のアルゴリズムの中から選択できます。 |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS パッケージを暗号化し、デジタル署名する必要がある

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [デジタル署名を使用してパッケージのソースを特定する](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures)、[脅威と脆弱性の対策 (Integration Services)](/sql/integration-services/security/security-overview-integration-services) |
| **手順** | パッケージのソースは、パッケージを作成した個人または組織です。 不明なソースや信頼されていないソースのパッケージを実行することは危険な場合があります。 SSIS パッケージの不正な改ざんを防ぐには、デジタル署名を使用する必要があります。 また、保存中や転送中にパッケージの機密性を確保するために、SSIS パッケージを暗号化する必要があります。 |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>重要なセキュリティ保護可能なデータベース リソースにデジタル署名を追加する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ADD SIGNATURE (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **手順** | 重要なセキュリティ保護可能なデータベース リソースの整合性を検証する必要がある場合、デジタル署名を使用する必要があります。 ストアド プロシージャ、関数、アセンブリ、トリガーなど、セキュリティ保護可能なデータベース リソースにデジタル署名することができます。 これが役立つ状況の例を紹介します。ISV (独立系ソフトウェア ベンダー) が、顧客に提供されたソフトウェアのサポートを提供するとします。 ISV はサポートを提供する前に、そのソフトウェアのセキュリティ保護可能なデータベース リソースが誤って改ざんされたり、悪意のある試みによって改ざんされたりしていないことを確認する必要があります。 セキュリティ保護可能なリソースがデジタル署名されていれば、ISV はデジタル署名を確認し、そのリソースの整合性を検証できます。| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>SQL Server EKM を使用して暗号化キーを保護する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [SQL Server 拡張キー管理 (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm)、[Azure Key Vault を使用する拡張キー管理 (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **手順** | SQL Server 拡張キー管理を使用すると、データベース ファイルを保護する暗号化キーを、スマート カード、USB デバイス、EKM/HSM モジュールなどの外部デバイスに保存できます。 これにより、データベース管理者 (sysadmin グループのメンバーを除く) からのデータの保護も実現され、 そのデータベース ユーザー以外はアクセスできない外部 EKM/HSM モジュール上の暗号化キーを使用してデータを暗号化できます。 |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>暗号化キーがデータベース エンジンに公開されないようにする必要がある場合は AlwaysEncrypted 機能を使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure、OnPrem |
| **属性**              | SQL バージョン - V12、MsSQL2016 |
| **参照**              | [Always Encrypted (データベース エンジン)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **手順** | Always Encrypted は、Azure SQL Database や SQL Server データベースに格納された、クレジット カード番号や国民識別番号 (米国の社会保障番号など) などの機密データを保護することを目的とした機能です。 Always Encrypted を使用すると、クライアントはクライアント アプリケーション内の機密データを暗号化することができます。暗号化キーがデータベース エンジン (SQL Database や SQL Server) に公開されることはありません。 これにより、Always Encrypted では、データの所有者 (データを表示できるユーザー) とデータの管理者 (アクセス権は付与しないユーザー) を分離できます。 |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>IoT デバイスで暗号化キーを安全に保存する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | デバイスの OS - Windows IoT Core、デバイスの接続 - Azure IoT device SDK |
| **参照**              | [Windows IoT Core 上の TPM](/windows/iot-core/secure-your-device/TPM)、[Windows IoT Core 上での TPM のセットアップ](/windows/iot-core/secure-your-device/setuptpm)、[Azure IoT device SDK の TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **手順** | 対称秘密キーまたは証明書の秘密キーは、TPM やスマート カード チップのようなハードウェアで保護された記憶域に安全に保存します。 Windows 10 IoT Core では TPM の使用をサポートしています。いくつかの互換性のある TPM を使用できます (https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm )。 ファームウェア TPM またはディスクリート TPM を使用することをお勧めします。 ソフトウェア TPM は、開発およびテストにのみ使用します。 TPM が使用可能であり、キーがプロビジョニングされたら、機密情報をハードコーディングせずに、トークンを生成するコードを作成する必要があります。 | 

### <a name="example"></a>例
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
上記のように、デバイスの主キーはコード内にはありません。 代わりに、スロット 0 の TPM に格納されます。 TPM デバイスは、有効期間の短い SAS トークンを生成します。このトークンは、IoT Hub への接続に使用されます。 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>IoT Hub に対する認証用に十分な長さのランダムな対称キーを生成する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | ゲートウェイの選択 - Azure IoT Hub |
| **参照**              | 該当なし  |
| **手順** | IoT Hub にはデバイス ID レジストリがあり、デバイスのプロビジョニング時に、ランダムな対称キーが自動的に生成されます。 Azure IoT Hub ID レジストリのこの機能を使用して、認証に使用するキーを生成することをお勧めします。 また、IoT Hub では、デバイスの作成時にキーを指定することもできます。 デバイスのプロビジョニング時に IoT Hub の外部でキーを生成する場合は、ランダムな対称キーまたは 256 ビット以上のキーを作成することをお勧めします。 |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>PIN の使用を要求し、リモート ワイプを許可するデバイス管理ポリシーが設定されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM モバイル クライアント | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | PIN の使用を要求し、リモート ワイプを許可するデバイス管理ポリシーが設定されていることを確認します。 |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>PIN/パスワード/自動ロックを要求し、すべてのデータを暗号化する (BitLocker など) デバイス管理ポリシーが設定されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM Outlook クライアント | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | PIN/パスワード/自動ロックを要求し、すべてのデータを暗号化する (BitLocker など) デバイス管理ポリシーが設定されていることを確認する |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Identity Server を使用するときは、署名キーがロールオーバーされていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Identity Server - キー、署名、暗号化](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **手順** | Identity Server を使用するときは、署名キーがロールオーバーされていることを確認します。 「参照」のリンク先ドキュメントでは、Identity Server を利用するアプリケーションを停止させずにこれを計画する方法を説明しています。 |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Identity Server で暗号強度が高いクライアント ID とクライアント シークレットが使用されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>Identity Server で暗号強度が高いクライアント ID とクライアント シークレットが使用されていることを確認します。 クライアント ID とクライアント シークレットを生成するときは、次のガイドラインに従ってください。</p><ul><li>クライアント ID として、ランダムな GUID を生成します。</li><li>シークレットとして、暗号化されたランダムな 256 ビット キーを生成します。</li></ul>|