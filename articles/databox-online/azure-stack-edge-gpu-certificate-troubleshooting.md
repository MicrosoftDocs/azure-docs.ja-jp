---
title: GPU 搭載の Azure Stack Edge Pro での証明書のトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイスでの証明書エラーのトラブルシューティングについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 67dd2b35229c15ae4df5ec8acb357aa35621d67c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436636"
---
# <a name="troubleshooting-certificate-errors"></a>証明書エラーのトラブルシューティング

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイスに証明書をインストールするときに発生する一般的な証明書エラーのトラブルシューティングについて説明します。

## <a name="common-certificate-errors"></a>一般的な証明書エラー

次の表に、一般的な証明書エラーと、それらのエラーの詳細情報および有効な解決策を示します。

> [!NOTE]
> &#8220;{0}、{1}、...、{n}&#8221; は、位置指定パラメーターを示します。 位置指定パラメーターは、使用する証明書に基づき値を受け取ります。

| エラー コード | 説明 |
|---|---|
| CertificateManagement_UntrustedCertificate | サブジェクト名 {0} の証明書の証明書チェーンが破損しています。 この証明書をアップロードする前に、署名チェーン証明書をアップロードしてください。|
| CertificateManagement_DeviceNotRegistered| デバイスがアクティブ化されていません。 アクティブ化した場合のみ、サポート証明書をアップロードできます。|
| CertificateManagement_ExpiredCertificate | 種類が {0} の証明書が有効期限切れか、間もなく有効期限が切れます。 証明書の有効期限を確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_FormatMismatch | 証明書の形式がサポートされていません。 証明書の形式を確認し、必要に応じて新しい証明書を導入してください。  {0} が必要ですが、{1} が見つかりました。 |
| CertificateManagement_GenericError | 証明書の管理操作を実行できません。 数分後にこの操作を再試行してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
| CertificateManagement_HttpsBindingFailure | サブジェクト名 {0} の証明書で、セキュリティで保護された https チャネルを作成できませんでした。 アップロードした証明書を確認し、必要に応じて新しい証明書を導入してください。 このエラーは、デバイス ノードの証明書で発生します。|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | サブジェクト名 {0} の証明書のキー使用法は証明書署名であってはなりません。 証明書のキー使用法を確認し、必要に応じて新しい証明書を導入してください。 このエラーは、署名チェーン証明書で発生します。 |
| CertificateManagement_IncorrectKeyNumber | サブジェクト名 {0} の証明書のキー番号 {1} が正しくありません。 証明書のキー番号を確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_InvalidP7b | アップロードした証明書ファイルは無効です。  証明書の形式を確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_KeyAlgorithmNotRSA | この証明書では、RSA キー アルゴリズムが使用されていません。 RSA 証明書のみがサポートされています。 |
| CertificateManagement_KeySizeNotSufficient | サブジェクト名 {0} の証明書のキー サイズ {1} が不十分です。 最小キー サイズは 4096 です。|
| CertificateManagement_MissingClientOid | サブジェクト名 {0} の証明書にクライアント認証 OID がありません。 証明書のプロパティを確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_MissingDigitalSignatureKeyUsage | サブジェクト名 {0} の証明書のキー使用法にデジタル署名がありません。 証明書のプロパティを確認し、必要に応じて新しい証明書を導入してください。 |
| CertificateManagement_MissingKeyCertSignKeyUsage | サブジェクト名 {0} の証明書のキー使用法に証明書署名がありません。 証明書のプロパティを確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | サブジェクト名 {0} の証明書のキー使用法にキーの暗号化がありません。 証明書のプロパティを確認し、必要に応じて新しい証明書を導入してください。 |
| CertificateManagement_MissingServerOid | サブジェクト名 {0} の証明書にサーバー認証 OID がありません。 証明書のプロパティを確認し、必要に応じて新しい証明書を導入してください。|
| CertificateManagement_NameMismatch | 証明書の種類が一致しません。 必要なスコープ: {0}、見つかったスコープ: {1}。 適切な証明書をアップロードしてください。|
| CertificateManagement_NoPrivateKeyPresent | サブジェクト名 {0} の証明書に秘密キーがありません。 秘密キーとともに .pfx 証明書をアップロードしてください。|
| CertificateManagement_NoRSACryptoPrivateKey | サブジェクト名 {0} の証明書の秘密キーにアクセスできません。 サポートされている証明書を使用していることを確認してください。 Microsoft RSA/Schannel Cryptographic Provider のみがサポートされます。 |
| CertificateManagement_NotSelfSignedCertificate | サブジェクト名 {0} の証明書が自己署名されていません。 ルート証明書には自己署名が必要です。 |
| CertificateManagement_NotSupportedOnVirtualAppliance | この操作は、仮想デバイスではサポートされません。 このエラーは、戦術的なクラウド アプライアンスで実行されている Data Box Gateway でのみ署名が行われることを示します。 このエラーは、Windows PowerShell を使用してデバイスを管理しているときに発生します。|
| CertificateManagement_SelfSignedCertificate | サブジェクト名 {0} の証明書は自己署名されています。 正しく署名されている証明書をアップロードしてください。|
| CertificateManagement_SignatureAlgorithmSha1 | サブジェクト名 {0} の証明書に、サポートされていない署名アルゴリズムがあります。 SHA1-RSA はサポートされていません。 |
| CertificateManagement_SubjectNamesInvalid | サブジェクト名 {0} の証明書に、{1} 証明書の正しいサブジェクト名またはサブジェクトの別名がありません。 アップロードした証明書を確認し、必要に応じて新しい証明書を導入してください。 また、DNS 名が SANS 名と一致しているか確認する必要もあります。|
| CertificateManagement_UnreadableCertificate | 種類が {0} の証明書を読み取れませんでした。 このエラーは、証明書が読み取り不能であるか、破損している場合に発生します。 新しい証明書を導入してください。|
| CertificateSubjectNotFound | サブジェクト名 {0} の証明書が見つかりません。 新しい証明書を導入してください。|
| CertificateRotationGenericFailure | 1 つまたは複数の証明書のローテーションが失敗しました。 数分後に再試行してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。|
| CertificateImportFailure | 拇印 {0} 付きの証明書がノード {1} にインポートされませんでした。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
| CertificateApplyFailure | 拇印 {0} 付きの証明書がノード {1} に適用されませんでした。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。|
| NodeNotReachable | {0} の証明書を検証できませんでした。 システム ハードウェアとソフトウェアの正常性をご確認ください。|


## <a name="next-steps"></a>次のステップ

5\. コマンド プロンプトで、「[ipconfig /all](azure-stack-edge-gpu-certificate-requirements.md)」と入力して Enter キーを押します。