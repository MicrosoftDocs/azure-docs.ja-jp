---
title: Azure の一般的な VM エラー コード | Microsoft Docs
description: Azure で仮想マシンをプロビジョニングし、管理するときに発生する一般的なエラー コードのいくつかについて説明します
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 3b8c9c80c93430d8dc5a888742e4e4e96ba99400
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695321"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Azure で仮想マシンを管理するときに発生する一般的なエラー メッセージについて説明します

この記事では、Azure で仮想マシン (VM) を作成または管理するときに発生する可能性がある最も一般的なエラー コードとメッセージについて説明します。

>[!NOTE]
> ご意見、ご感想については、こちらのページにコメントを投稿していただくか、または [Azure フィードバック](https://feedback.azure.com/forums/216843-virtual-machines)から #azerrormessage タグを使用してコメントをお寄せください。

## <a name="error-response-format"></a>エラー応答の形式 
Azure VM では、エラー応答に次の JSON 形式を使用します。

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

エラー応答には、常に状態コードとエラー オブジェクトが含まれます。 各エラー オブジェクトには、常にエラー コードとメッセージが含まれます。 テンプレートを使用して VM を作成した場合は、内部レベルのエラー コードとメッセージを含んだ詳細セクションもエラー オブジェクトに含まれます。 通常、エラー メッセージの最も内側のレベルは、ルートの障害です。


## <a name="common-virtual-machine-management-errors"></a>仮想マシンの管理の一般的なエラー

このセクションでは、VM の管理時に発生する可能性がある一般的なエラー メッセージの一覧を示します。

|  エラー コード  |  エラー メッセージ  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  URI {1} の BLOB を使用してディスク '{0}' を作成中にリースを取得できませんでした。 BLOB は既に使用中です。  |  
|  AllocationFailed  |  割り当てに失敗しました。 VM サイズまたは VM の台数を減らしてみるか、後で再試行するか、別の可用性セットまたは別の Azure の場所にデプロイしてみてください。  |  
|  AllocationFailed  |  内部エラーが発生したため、VM を割り当てることができませんでした。 後で再試行するか、別の場所にデプロイしてみてください。  |
|  ArtifactNotFound  |  発行元 '{0}' および種類 '{1}' の VM 拡張機能が場所 '{2}' で見つかりませんでした。  |
|  ArtifactNotFound  |  発行元 '{0}'、種類 '{1}'、種類ハンドラー バージョン '{2}' の拡張機能が拡張機能リポジトリに見つかりません。  |
|  ArtifactVersionNotFound  |  要求されたバージョン '{0}' を満たすバージョンがアーティファクト リポジトリに見つかりません。  |
|  ArtifactVersionNotFound  |  発行元 '{1}' と種類 '{2}' の VM 拡張機能に要求されたバージョン '{0}' を満たすバージョンがアーティファクト リポジトリに見つかりません。  |
|  AttachDiskWhileBeingDetached  |  データ ディスク '{0}' は、現在デタッチ中であるため、VM '{1}' にアタッチできません。 ディスクが完全に切断されるまで待ってから、もう一度お試しください。  |
|  BadRequest  |  'Aligned' 可用性セットは、このリージョンではまだサポートされていません。  |
|  BadRequest  |  管理されていない可用性セットへの管理ディスク付き VM の追加や、管理された可用性セットへの BLOB ベースのディスク付き VM の追加はサポートされていません。 管理ディスク付き VM を追加するには、'managed' プロパティを設定した可用性セットを作成してください。  |
|  BadRequest  |  Managed Disks は、このリージョンではサポートされていません。  |
|  BadRequest  |  OS の種類 '{0}' では、1 つのハンドラーに対して複数の VMExtensions はサポートされていません。 ハンドラー '{2}' を使用して VMExtension '{1}' が既に追加されているか、入力で指定されています。  |
|  BadRequest  |  操作 '{0}' はマネージド ディスクを使用するリソース '{1}' ではサポートされていません。  |
|  CertificateImproperlyFormatted  |  {0} から取得したシークレットの JSON 表現には、適切にフォーマットされた PFX ファイルではないデータ フィールドが含まれているか、指定されたパスワードでは PFX ファイルを正しくデコードできません。  |
|  CertificateImproperlyFormatted  |  {0} から取得したデータは JSON に逆シリアル化できません。  |
|  競合  |  ディスクのサイズ変更が許可されているのは、VM の作成時と VM の割り当て解除時のみです。  |
|  ConflictingUserInput  |  ディスク '{0}' は VM '{1}' に既に所有されているのでアタッチできません。  |
|  ConflictingUserInput  |  ソースとターゲットのリソース グループが同じです。  |
|  ConflictingUserInput  |  ディスク {0} のソースとターゲットのストレージ アカウントが異なります。  |
|  ContainerAlreadyOnLease  |  URI {0} の BLOB を保持するストレージ コンテナーにリースが既に存在します。  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  リソースの移動要求に、要求内で 1 つ以上の {0} によって参照される KeyVault リソースが含まれています。 これは現在、クロス サブスクリプションの移動ではサポートされていません。 KeyVault リソースの ID について、エラーの詳細を参照してください。  |
|  DiagnosticsOperationInternalError  |  VM {0} の診断プロファイルの処理中に内部エラーが発生しました。  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} は VM '{1}' に属する別のディスクで既に使用されています。 BLOB のメタデータでディスクの参照情報を調べることができます。  |
|  DiskBlobNotFound  |  ディスク '{1}' の URI {0} の VHD BLOB が見つかりません。  |
|  DiskBlobNotFound  |  URI {0} の VHD BLOB が見つかりません。  |
|  DiskEncryptionKeySecretMissingTags  |  {0} シークレットに、{1} タグがありません。 シークレット バージョンを更新し、必要なタグを追加して再試行してください。  |
|  DiskEncryptionKeySecretUnwrapFailed  |  キー {1} を使用したシークレット {0} 値のラップ解除に失敗しました。  |
|  DiskImageNotReady  |  ディスク イメージ {0} は {1} 状態です。 イメージの準備が完了したら再試行してください。  |
|  DiskPreparationError  |  VM ディスクを準備しているときに、1 つ以上のエラーが発生しました。 詳しくは、ディスクのインスタンス ビューをご覧ください。  |
|  DiskProcessingError  |  VM には、障害が発生したディスク内に他のディスクがあるため、ディスクの処理が停止しました。  |
|  ImageBlobNotFound  |  ディスク '{1}' の URI {0} の VHD BLOB が見つかりません。  |
|  ImageBlobNotFound  |  URI {0} の VHD BLOB が見つかりません。  |
|  IncorrectDiskBlobType  |  ディスクの BLOB はページ BLOB 型にしかできません。 ディスク '{1}' の BLOB {0} はブロック BLOB 型です。  |
|  IncorrectDiskBlobType  |  ディスクの BLOB はページ BLOB 型にしかできません。 BLOB {0} の種類は '{1}' です。  |
|  IncorrectImageBlobType  |  ディスクの BLOB はページ BLOB 型にしかできません。 ディスク '{1}' の BLOB {0} はブロック BLOB 型です。  |
|  IncorrectImageBlobType  |  ディスクの BLOB はページ BLOB 型にしかできません。 BLOB {0} の種類は '{1}' です。  |
|  InternalOperationError  |  ストレージ アカウント {0} を解決できません。 ストレージ アカウントがコンピューティング リソースと同じ場所にストレージ リソース プロバイダーによって作成されたことを確認してください。  |
|  InternalOperationError  |  {0} 件のゴール シーク タスクが失敗しました。  |
|  InternalOperationError  |  VM '{0}' のネットワーク プロファイルを検証中にエラーが発生しました。  |
|  InvalidAccountType  |  AccountType {0} は無効です。  |
|  InvalidParameter  |  パラメーター {0} の値が無効です。  |
|  InvalidParameter  |  指定した管理者のパスワードは許可されません。  |
|  InvalidParameter  |  指定したパスワードは、長さが {0} から {1} 文字であり、次のパスワードの複雑さの要件のうちの少なくとも {2} 個を満たしている必要があります: <ol><li> 大文字が含まれている</li><li>小文字が含まれている</li><li>数字が含まれている</li><li>特殊文字が含まれている</li></ol>  |
|  InvalidParameter  |  指定した管理者のユーザー名は許可されません。  |
|  InvalidParameter  |  VM がプラットフォーム イメージまたはユーザー イメージから作成されている場合は、既存の OS ディスクを接続することはできません。  |
|  InvalidParameter  |  コンテナー名 {0} は無効です。 コンテナー名は 3 から 63 文字の長さでなければならず、使用できるのは小文字の英数字とハイフンのみです。 ハイフンの前後には英数字を指定する必要があります。  |
|  InvalidParameter  |  URL {1} のコンテナー名 {0} は無効です。 コンテナー名は 3 から 63 文字の長さでなければならず、使用できるのは小文字の英数字とハイフンのみです。 ハイフンの前後には英数字を指定する必要があります。  |
|  InvalidParameter  |  URL {0} の BLOB 名にスラッシュが含まれています。 現時点では、ディスクに含めることはできません。  |
|  InvalidParameter  |  URI {0} は正しい BLOB URI ではないようです。  |
|  InvalidParameter  |  '{0}' という名前のディスクで既に同じ LUN: {1} を使用しています。  |
|  InvalidParameter  |  '{0}' という名前のディスクは既に存在します。  |
|  InvalidParameter  |  指定したイメージ参照で既に定義されているディスクに対してユーザー イメージの上書きを指定することはできません。  |
|  InvalidParameter  |  '{0}' という名前のディスクで既に同じ VHD URL: {1} を使用しています。  |
|  InvalidParameter  |  指定した障害ドメインの数 {0} は、{1} から {2} の範囲でなければなりません。  |
|  InvalidParameter  |  ライセンスの種類 {0} が無効です。 有効なライセンスの種類は Windows_Client または Windows_Server (大文字小文字は区別されます) です。  |
|  InvalidParameter  |  Linux のホスト名は {0} 文字を超えることはできません。また、次の文字を使用することはできません: {1}。  |
|  InvalidParameter  |  Linux のプロビジョニング エージェントの既知の問題が原因で、SSH 公開キーのターゲット パスは、現在、既定値 {0} に制限されています。  |
|  InvalidParameter  |  LUN {0} には既にディスクが存在します。  |
|  InvalidParameter  |  要求のサブスクリプション {0} は、マネージド ディスクの ID に含まれるサブスクリプション {1} と一致している必要があります。  |
|  InvalidParameter  |  OSProfile のカスタム データは、Base64 でエンコードし、最大長が {0} 文字である必要があります。  |
|  InvalidParameter  |  URL {0} の BLOB 名は '{1}' という拡張子で終わる必要があります。  |
|  InvalidParameter  |  {0} は、キャプチャされた VHD BLOB 名の有効なプレフィックスではありません。 有効なプレフィックスは、正規表現 '{1}' と一致します。  |
|  InvalidParameter  |  VM エージェントがプロビジョニングされていない場合、証明書を VM に追加することはできません。  |
|  InvalidParameter  |  LUN {0} には既にディスクが存在します。  |
|  InvalidParameter  |  要求されたサイズ {0} は、可用性セットが現在割り当てられているクラスターで利用できないため、VM を作成することができません。 利用可能なサイズは、{1} です。 VM のサイズ変更の方針について詳しくは、 https://aka.ms/azure-resizevm をご覧ください。  |
|  InvalidParameter  |  要求された VM のサイズ {0} は、現在のリージョンでは利用できません。 現在のリージョンで利用可能なサイズは、{1} です。 各リージョンで利用可能な VM のサイズについて詳しくは、 https://aka.ms/azure-regions をご覧ください。  |
|  InvalidParameter  |  要求された VM のサイズ {0} は、現在のリージョンでは利用できません。 各リージョンで利用可能な VM のサイズについて詳しくは、 https://aka.ms/azure-regions をご覧ください。  |
|  InvalidParameter  |  Windows の管理者のユーザー名は、{0} 文字を超えること、ピリオド (.) で終わること、次の文字を含めることはできません: {1}。  |
|  InvalidParameter  |  Windows のコンピューター名は、{0} 文字を超えること、すべて数字にすること、次の文字を含めることはできません: {1}。  |
|  MissingMoveDependentResources  |  リソースの移動要求に、すべての依存リソースが含まれていません。 不足しているリソース ID についてはエラーの詳細を確認してください。  |
|  MoveResourcesHaveInvalidState  |  リソースの移動要求に、無効なストレージ アカウントに関連付けられた VM が含まれています。 これらのリソース ID と参照されるストレージ アカウント名の詳細をご確認ください。  |
|  MoveResourcesHavePendingOperations  |  リソースの移動要求に、操作が保留中のリソースが含まれています。 これらのリソース ID については詳細を確認してください。 保留中の操作の完了後に操作を再試行してください。  |
|  MoveResourcesNotFound  |  リソースの移動要求に、見つけることのできないリソースが含まれています。 これらのリソース ID については詳細を確認してください。  |
|  NetworkingInternalOperationError  |  不明なネットワーク割り当てエラー。  |
|  NetworkingInternalOperationError  |  不明なネットワーク割り当てエラー  |
|  NetworkingInternalOperationError  |  VM のネットワーク プロファイルを処理中に内部エラーが発生しました。  |
|  NotFound  |  可用性セット {0} が見つかりません。  |
|  NotFound  |  要求で指定されたソース仮想マシン '{0}' は、この Azure の場所には存在しません。  |
|  NotFound  |  ID {0} のテナントが見つかりません。  |
|  NotFound  |  イメージ {0} が見つかりません。  |
|  NotSupported  |  ライセンスの種類は {0} ですが、イメージ BLOB {1} はオンプレミスのものではありません。  |
|  OperationNotAllowed  |  可用性セット {0} を削除できません。 可用性セットを削除する前に、VM が含まれていないことを確認してください。  |
|  OperationNotAllowed  |  可用性セット SKU を 'Aligned' から 'Classic' に変更することは許可されていません。  |
|  OperationNotAllowed  |  VM が実行されていないときに VM の拡張機能を変更することはできません。  |
|  OperationNotAllowed  |  キャプチャ操作は、BLOB ベースのディスクを使用する仮想マシンでのみサポートされています。 管理された仮想マシンからイメージを作成する場合は 'イメージ' リソース API をお使いください。  |
|  OperationNotAllowed  |  イメージが正常に作成されるまでは、イメージ {1} からリソース {0} を作成することはできません。  |
|  OperationNotAllowed  |  VM が割り当てられている場合は encryptionSettings を更新できません。VM の割り当ての解除後に再試行してください  |
|  OperationNotAllowed  |  BLOB ベースのディスク付きの VM への管理ディスクの追加はサポートされていません。  |
|  OperationNotAllowed  |  このサイズの VM に添付できるデータ ディスクの最大数は {0} です。  |
|  OperationNotAllowed  |  管理ディスク付き VM への BLOB ベースのディスクの追加はサポートされていません。  |
|  OperationNotAllowed  |  イメージ '{1}' は削除対象にマークされているため、このイメージに対して操作 '{0}' を実行することは許可されていません。 再試行できるのは削除操作のみです (または、進行中の作業が完了するまで待機してください)。  |
|  OperationNotAllowed  |  VM '{1}' が汎用化されているため、操作 '{0}' は許可されません。  |
|  OperationNotAllowed  |  復元ポイント コレクション '{1}' は削除対象としてマークされているので、操作 '{0}' は許可されません。  |
|  OperationNotAllowed  |  VM 拡張機能 '{1}' が削除対象としてマークされているため、操作 '{0}' は許可されません。 再試行できるのは削除操作のみです (または、進行中の作業が完了するまで待機してください)。  |
|  OperationNotAllowed  |  イメージ '{2}' を使って仮想マシン '{1}' をプロビジョニング中であるため、操作 '{0}' を実行することは許可されていません。  |
|  OperationNotAllowed  |  仮想マシン ScaleSet '{1}' で現在イメージ '{2}' を使用しているため、操作 '{0}' を実行できません。  |
|  OperationNotAllowed  |  VM '{1}' が削除対象としてマークされているため、操作 '{0}' は許可されません。 再試行できるのは削除操作のみです (または、進行中の作業が完了するまで待機してください)。  |
|  OperationNotAllowed  |  VM '{1}' が割り当て解除されているか、割り当て解除対象としてマークされているため、操作 '{0}' は許可されません。  |
|  OperationNotAllowed  |  VM '{1}' が実行中であるため、操作 '{0}' は許可されません。 ゲスト オペレーティング システム内から VM をシャットダウンする場合は明示的に電源をオフにしてください。  |
|  OperationNotAllowed  |  VM '{1}' が割り当て解除されていないので、この VM に対する操作 '{0}' は許可されません。  |
|  OperationNotAllowed  |  VM に失敗した状態の拡張機能 '{2}' があるため、操作 '{0}' は VM '{1}' では許可されていません。  |
|  OperationNotAllowed  |  VM '{1}' では、別の操作が実行中であるため、操作 '{0}' は許可されません。  |
|  OperationNotAllowed  |  '{0}' の操作には、仮想マシン '{1}' を一般化する必要があります。  |
|  OperationNotAllowed  |  この操作では、VM が実行されている (または実行するよう設定されている) 必要があります。  |
|  OperationNotAllowed  |  サイズが {0} GB のディスクは、イメージ内の対応するディスクのサイズ {1} GB より小さいため、許可されません。  |
|  OperationNotAllowed  |  ハンドラー '{0}' の VM スケール セット拡張を追加できるのは、VM スケール セットの作成時のみです。  |
|  OperationNotAllowed  |  ハンドラー '{0}' の VM スケール セット拡張を削除できるのは、VM スケール セットの削除時のみです。  |
|  OperationNotAllowed  |  VM '{0}' は既にマネージド ディスクを使用しています。  |
|  OperationNotAllowed  |  VM '{0}' は 'Classic' の可用性セット '{1}' に属しています。 'Aligned' SKU を使うように可用性セットを更新してから、変換をもう一度お試しください。  |
|  OperationNotAllowed  |  イメージから作成された VM では、BLOB ベースのディスクを持つことはできません。 すべてのディスクが管理ディスクである必要があります。  |
|  OperationNotAllowed  |  VM が一般化されていないため、キャプチャ操作を完了できません。  |
|  OperationNotAllowed  |  VM '{0}' のディスクがマネージド ディスクに変換されているので、この VM に対する管理操作は許可されません。  |
|  OperationNotAllowed  |  実行中の操作によって、仮想マシン {0} の電源状態を {1} に変更しています。 操作 {2} はしばらくしてから実行してください。  |
|  OperationNotAllowed  |  VM の追加または更新ができません。 要求された VM のサイズ {0} は、既存のアロケーション ユニットでは利用できない可能性があります。 VM のサイズ変更の方針について詳しくは、 https://aka.ms/azure-resizevm をご覧ください。  |
|  OperationNotAllowed  |  要求されたサイズ {0} は、可用性セットが現在割り当てられているクラスターで利用できないため、VM のサイズ変更ができません。 利用可能なサイズは、{1} です。 VM のサイズ変更の方針について詳しくは、 https://aka.ms/azure-resizevm をご覧ください。  |
|  OperationNotAllowed  |  要求されたサイズ {0} は、VM が現在割り当てられているクラスターで利用できないため、VM のサイズ変更ができません。 VM を {1} へサイズ変更するには、割り当ての解除 (Azure Portal での停止操作) を実行し、もう一度サイズ変更の操作をしてみてください。 VM のサイズ変更の方針について詳しくは、 https://aka.ms/azure-resizevm をご覧ください。  |
|  OSProvisioningClientError  |  VM '{0}' に OS をプロビジョニングできませんでした。現在、ゲスト OS をプロビジョニングしています。  |
|  OSProvisioningClientError  |  VM '{0}' の OS をプロビジョニングできませんでした。 エラーの詳細:{1}。イメージが適切に準備 (一般化) されていることも確認してください。 <ul><li>Windows 向けの手順: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH ホスト キーの生成に失敗しました。 エラーの詳細: {0}。 この問題を解決するには、Linux エージェントが適切にセットアップされていることを確かめてください。 <ul><li>[https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ ) で手順を確認することができます </li></ul> |
|  OSProvisioningClientError  |  VM に指定されたユーザー名は、この Linux ディストリビューションに関しては無効です。 エラーの詳細: {0}。  |
|  OSProvisioningInternalError  |  内部エラーが発生したため、VM '{0}' の OS をプロビジョニングできませんでした。  |
|  OSProvisioningTimedOut  |  割り当てられた時間内に VM '{0}' の OS のプロビジョニングが終了しませんでした。 まだプロビジョニングが正常に完了する可能性があります。 後でプロビジョニングの状態を確認してください。  |
|  OSProvisioningTimedOut  |  割り当てられた時間内に VM '{0}' の OS のプロビジョニングが終了しませんでした。 まだプロビジョニングが正常に完了する可能性があります。 後でプロビジョニングの状態を確認してください。 また、イメージが適切に準備 (一般化) されていることも確認してください。   <ul><li>Windows 向けの手順: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux 向けの手順: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  割り当てられた時間内に VM '{0}' の OS のプロビジョニングが終了しませんでした。 ただし、VM ゲスト エージェントが実行中であると検出されました。 これは、ゲスト OS を VM イメージとして使用する準備が正しく行われていないことを示しています (CreateOption=FromImage を使用)。 この問題を解決するには、CreateOption=Attach を指定して VHD をそのまま使用するか、イメージとして使用できるように正しく準備してください:   <ul><li>Windows 向けの手順: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux 向けの手順: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  選択した場所では、現在、必要な VM サイズを利用できません。  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  プラットフォームの更新が実行中であるため、現在はリソースを更新できません。 後でもう一度やり直してください。  |
|  StorageAccountLimitation  |  ストレージ アカウント '{0}' では、ディスクを作成するために必要なページ BLOB をサポートしていません。  |
|  StorageAccountLimitation  |  ストレージ アカウント '{0}' が割り当てられたクォータを超えています。  |
|  StorageAccountLocationMismatch  |  ストレージ アカウント {0} を解決できません。 ストレージ アカウントがコンピューティング リソースと同じ場所にストレージ リソース プロバイダーによって作成されたことを確認してください。  |
|  StorageAccountNotFound  |  ストレージ アカウント {0} が見つかりません。 ストレージ アカウントが削除されておらず、VM と同じ Azure の場所に属していることを確認してください。  |
|  StorageAccountNotRecognized  |  ストレージ リソース プロバイダーが管理するストレージ アカウントを使用してください。 {0} の使用はサポートされていません。  |
|  StorageAccountOperationInternalError  |  ストレージ アカウント {0} にアクセス中に内部エラーが発生しました。  |
|  StorageAccountSubscriptionMismatch  |  ストレージ アカウント {0} は、サブスクリプション {1} に属していません。  |
|  StorageAccountTooBusy  |  ストレージ アカウント '{0}' は現在ビジー状態です。 別のアカウントの使用を検討してください。  |
|  StorageAccountTypeNotSupported  |  ディスク {0} で、BLOB ストレージ アカウント {1} を使用しています。 汎用ストレージ アカウントを使ってもう一度お試しください。  |
|  StorageAccountTypeNotSupported  |  ストレージ アカウント {0} の種類は {1} です。 起動診断がサポートするストレージ アカウントの種類は {2} です。  <ul><li>このエラーは、ブート診断に Premium Storage アカウントを使用する場合に発生します。 詳しくは、[起動診断の使用方法](boot-diagnostics.md)に関するページをご覧ください。 </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  許可されていないサブスクリプションです。  |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} は既に存在します。 新しい空のデータ ディスク '{1}' を作成するには、別の BLOB URI を指定してください。  |
|  TargetDiskBlobAlreadyExists  |  ターゲット イメージ BLOB {0} は既に存在しており、VHD BLOB を上書きするフラグが設定されていないため、キャプチャ操作を続行できません。 BLOB を削除するか、VHD BLOB を上書きするフラグを設定してから、再試行してください。  |
|  TargetDiskBlobAlreadyExists  |  ターゲット イメージ BLOB {0} にアクティブなリースがあるため、キャプチャ操作を続行できません。   |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} は既に存在します。 ディスク '{1}' のターゲットとして別の BLOB URI を指定してください。  |
|  TooManyVMRedeploymentRequests  |  受信した、VM '{0}' またはこの VM と同じ可用性セットの VM に対する再デプロイ要求が多すぎます。 後で再試行してください。  |
|  VHDSizeInvalid  |  BLOB {2} のディスク '{1}' に対して指定したディスク サイズの値 {0} は無効です。 ディスク サイズは、{3} と {4} の間でなければなりません。  |
|  VMAgentStatusCommunicationError  |  VM '{0}' は VM エージェントまたは拡張機能の状態を報告していません。 VM で実行中の VM エージェントがあり、Azure ストレージへの送信接続が確立できることを確認してください。  |
|  VMArtifactRepositoryInternalError  |  VM アーティファクトの詳細を取得するためにアーティファクト リポジトリと通信中にエラーが発生しました。  |
|  VMArtifactRepositoryInternalError  |  アーティファクト リポジトリから VM アーティファクト データを取得中に内部エラーが発生しました。  |
|  VMExtensionHandlerNonTransientError  |  ハンドラー '{0}' は、ターミナル エラー コード '{2}' と次のエラー メッセージで VM 拡張機能 '{1}' のエラーを報告しました: '{3}'  |
|  VMExtensionManagementInternalError  |  VM 拡張機能 '{0}' を処理中に内部エラーが発生しました。  |
|  VMExtensionManagementInternalError  |  VM 拡張機能を準備しているときに、複数のエラーが発生しました。 詳しくは、VM 拡張機能のインスタンス ビューをご覧ください。  |
|  VMExtensionProvisioningError  |  拡張機能 '{0}' を処理しているときに、VM がエラーを報告しました。 エラー メッセージ: "{1}"。  |
|  VMExtensionProvisioningError  |  VM で複数の VM 拡張機能をプロビジョニングできませんでした。 詳しくは、VM 拡張機能のインスタンス ビューをご覧ください。  |
|  VMExtensionProvisioningTimeout  |  VM 拡張機能 '{0}' のプロビジョニングがタイムアウトしました。拡張機能のインストールに時間がかかりすぎたか、拡張機能の状態を取得できませんでした。  |
|  VMMarketplaceInvalidInput  |  Marketplace 以外のイメージから仮想マシンを作成する場合、プラン情報は必要ありません。要求からプラン情報を削除してください。 OS ディスク名は {0} です。  |
|  VMMarketplaceInvalidInput  |  購入情報が一致しません。 Marketplace イメージからデプロイできません。 OS ディスク名は {0} です。  |
|  VMMarketplaceInvalidInput  |  Marketplace イメージから仮想マシンを作成する場合、要求にプラン情報が必要です。 OS ディスク名は {0} です。  |
|  VMNotFound  |  VM '{0}' が見つかりません。  |
|  VMRedeploymentFailed  |  内部エラーが発生したため、VM '{0}' を再デプロイできませんでした。 後で再試行してください。  |
|  VMRedeploymentTimedOut  |  割り当てられた時間内に VM '{0}' の再デプロイが終了しませんでした。 しばらくしてから正常に終了する可能性があります。 終了しない場合は、要求を再試行してください。  |
|  VMStartTimedOut  |  割り当てられた時間内に VM '{0}' が起動しませんでした。 まだ VM が正常に起動する可能性があります。 後で電源の状態を確認してください。  |


## <a name="next-steps"></a>次の手順
さらに支援が必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。