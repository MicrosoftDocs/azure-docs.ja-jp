# 概要
## [Azure AD Connect とは](active-directory-aadconnect.md)
## [Azure AD Connect 同期とは](active-directory-aadconnectsync-whatis.md)
### [ユーザーと連絡先](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [アーキテクチャ](active-directory-aadconnectsync-understanding-architecture.md)
### [宣言型のプロビジョニング](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [宣言型のプロビジョニングの式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [既定の構成](active-directory-aadconnectsync-understanding-default-configuration.md)
## [Azure AD Connect とフェデレーションとは](active-directory-aadconnectfed-whatis.md)


# 作業開始
## [前提条件](active-directory-aadconnect-prerequisites.md)
## [Azure AD Connect のインストール](active-directory-aadconnect-select-installation.md)
### [簡単設定](active-directory-aadconnect-get-started-express.md)
### [カスタム設定](active-directory-aadconnect-get-started-custom.md)
### [DirSync からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [以前のバージョンからのアップグレード](active-directory-aadconnect-upgrade-previous-version.md)
### [既存の ADSync データベースを使用したインストール](active-directory-aadconnect-existing-database.md)

# 方法
## 計画と設計
### [設計概念](active-directory-aadconnect-design-concepts.md)
### [Azure AD Connect のトポロジ](active-directory-aadconnect-topologies.md)
### [Azure の Active Directory フェデレーション サービス](active-directory-aadconnect-azure-adfs.md)
### [インスタンスに関する特別な考慮事項](active-directory-aadconnect-instances.md)
### [Azure AD を既に使用している場合](active-directory-aadconnect-existing-tenant.md)
## [MAzure AD Connect の管理](active-directory-aadconnect-whats-next.md)
### [O365 と Azure AD の証明書を更新する](active-directory-aadconnect-o365-certs.md)
### [Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書の更新](active-directory-aadconnectfed-ssl-update.md)
### [デバイスの書き戻しを有効にする](active-directory-aadconnect-feature-device-writeback.md)
### [ユーザー サインオンのオプション](active-directory-aadconnect-user-signin.md)
#### [シームレス シングル サインオン](active-directory-aadconnect-sso.md)
##### [クイック スタート](active-directory-aadconnect-sso-quick-start.md)
##### [それはどのように機能しますか?](active-directory-aadconnect-sso-how-it-works.md)
##### [よく寄せられる質問](active-directory-aadconnect-sso-faq.md)
##### [トラブルシューティング](active-directory-aadconnect-troubleshoot-sso.md)
#### [パススルー認証](active-directory-aadconnect-pass-through-authentication.md)
##### [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md)
##### [現時点での制限事項](active-directory-aadconnect-pass-through-authentication-current-limitations.md)
##### [それはどのように機能しますか?](active-directory-aadconnect-pass-through-authentication-how-it-works.md)
##### [プレビュー エージェントのアップグレード](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)
##### [スマート ロックアウト](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)
##### [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md)
##### [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)
##### [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)
### [フェデレーションに使用する複数ドメインのサポート](active-directory-aadconnect-multiple-domains.md)
### [自動アップグレード](active-directory-aadconnect-feature-automatic-upgrade.md)
### [シングル サインオンに SAML 2.0 ID プロバイダー (IdP) を使用する](active-directory-aadconnect-federation-saml-idp.md)



## Azure AD Connect 同期を管理する
### [誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [パスワードの同期](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Azure AD サービス アカウント](active-directory-aadconnectsync-howto-azureadaccount.md)
### [インストール ウィザード](active-directory-aadconnectsync-installation-wizard.md)
### [既定の構成の変更](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [フィルター処理の構成](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Azure AD Sync のサービス アカウント パスワードの変更](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [AD DS アカウントのパスワードの変更](active-directory-aadconnectsync-change-addsacct-pass.md)
### [AD のごみ箱の有効化](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [操作](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [コネクタ](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [メタバース デザイナー](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [メタバース検索](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## フェデレーション サービスを管理する
### [管理およびカスタマイズする](active-directory-aadconnect-federation-management.md)
### [Azure AD の複数のインスタンスと AD FS の単一インスタンスのフェデレーションを行う](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## トラブルシューティング
### [接続](active-directory-aadconnect-troubleshoot-connectivity.md)
### [同期中のエラー](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [オブジェクトが同期されない](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [パスワードの同期](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [userCertificate による LargeObject エラー](active-directory-aadconnectsync-largeobjecterror-usercertificate.md)
### [LocalDB の 10 GB 制限からの回復方法](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [ID 同期と重複属性の回復性](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [ハイブリッド ID で必要なポートとプロトコル](active-directory-aadconnect-ports.md)
## [プレビュー段階の機能](active-directory-aadconnect-feature-preview.md)
## [バージョン履歴](active-directory-aadconnect-version-history.md)
## [アカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect 同期
### [Azure Active Directory に同期される属性](active-directory-aadconnectsync-attributes-synchronized.md)
### [コネクタ バージョンのリリース履歴](active-directory-aadconnectsync-connector-version-history.md)
### [関数参照](active-directory-aadconnectsync-functions-reference.md)
### [操作タスクおよび考慮事項](active-directory-aadconnectsync-operations.md)
### [Azure AD のフェデレーション互換性リスト](active-directory-aadconnect-federation-compatibility.md)
### [技術的概念](active-directory-aadconnectsync-technical-concepts.md)
### [サービスの機能](active-directory-aadconnectsyncservice-features.md)




# 関連項目
## [クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](../connect-health/active-directory-aadconnect-health.md)
## [ハイブリッド ID 設計ガイド](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=security-identity)
##[Azure AD Connect の FAQ](active-directory-aadconnect-faq.md)
##[DirSync の廃止](active-directory-aadconnect-dirsync-deprecated.md)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

