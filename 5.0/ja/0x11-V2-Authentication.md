# V2 認証

## 管理目標

認証とは誰か (あるいは何か) を真正であるとして確立または確認する行為であり、個人またはデバイスに関する申し出は正しく、なりすましに耐性があり、パスワードの回復や傍受を防ぐことです。

ASVS が最初にリリースされたとき、ユーザ名 + パスワードは高度なセキュリティシステム以外で最も一般的な認証形式でした。多要素認証 (Multi-factor Authentication, MFA) はセキュリティサークルで一般的に受け入れられていますが、他ではほとんど必要とされていません。パスワード侵害の数が増加するにつれ、ユーザ名は何かしらの形で機密情報であり、パスワードは不明であるという考えでは、多くのセキュリティ管理策は維持できなくなりました。例えば、NIST 800-63 はユーザ名とナレッジベース認証 (Knowledge Based Authentication, KBA) を公開情報、電子メールを [not allowed](https://pages.nist.gov/800-63-FAQ/#q-b11)、SMS を ["restricted" authenticator type](https://pages.nist.gov/800-63-FAQ/#q-b01) 、パスワードを pre-breached としています。この本質は、知識ベースの認証符号、SMS および電子メールでのリカバリー、パスワード履歴、複雑さ、ローテーション管理を無用とします。これらの管理はまったく役に立たず、ユーザは数か月ごとに脆弱なパスワードを考えてきました。しかし、50億を超えるユーザ名とパスワード侵害のリリースにより、先に進む時がきました。

ASVS のすべての章の中で、認証とセッション管理の章が最も変更されています。効果的で、エビデンスベースのリーディングプラクティスの採用は多くの人にとって挑戦となるでしょうが、それはまったく問題ありません。今ここでポストパスワードの将来への移行を開始する必要があります。

## NIST 800-63 - 最新のエビデンスベースの認証標準

[NIST 800-63b](https://pages.nist.gov/800-63-3/sp800-63b.html) は最新のエビデンスベースの標準であり、適用可能性とは関係なく、利用可能な最適なアドバイスを表しています。この標準は世界中のすべての組織に役立ちますが、特に米国の代理店および米国の代理店を扱う組織に関連します。

NIST 800-63 の用語は、特にユーザ名 + パスワードの認証にしか慣れていない場合には、最初は少しわかりにくいかもしれません。最新の認証に進歩する必要があるため、将来一般的になるであろう用語を導入する必要がありますが、業界がこれらの新しい用語に落ち着くまで理解の難しさを理解しています。この章の最後に支援のための用語集があります。要件の表記ではなく、要件の意図を満たすために多くの要件を言い換えました。例えば、NIST が「記憶された秘密 (memorized secret)」を使用する場合、ASVS では「パスワード」という用語を使用します。

ASVS V2 認証、V3 セッション管理、および範囲は狭いですが、V4 アクセス制御は選択された NIST 800-63b 管理策の準拠サブセットに適応し、一般的な脅威と一般的に悪用される認証の脆弱性に焦点を当てています。NIST 800-63 に完全に準拠する必要がある場合には、NIST 800-63 を確認してください。

### 適切な NIST AAL レベルの選択

アプリケーションセキュリティ検証標準は ASVS L1 を NIST AAL1 要件に、L2 を AAL2 に、L3 を AAL3 にマップしようとしました。しかし、「必須」管理策としての ASVS レベル 1 のアプローチはアプリケーションや API を検証するための正しい AAL レベルであるとは限りません。例えば、アプリケーションがレベル 3 アプリケーションである場合や AAL3 とする規制要件がある場合には、章 V2 および V3 セッション管理でレベル 3 を選択すべきです。NIST 準拠の認証アサーションレベル (Authentication Assertion Level, AAL) の選択は NIST-63b ガイドラインに従って実行すべきです。[NIST 800-63b Section 6.2](https://pages.nist.gov/800-63-3/sp800-63-3.html#AAL_CYOA) の *Selecting AAL* に記載されています。

## V2.1 パスワードセキュリティ

NIST 800-63 により「記憶された秘密」と呼ばれるパスワードには、パスワード、PIN、ロック解除パターン、正しい子猫や他の画像要素の選択、およびパスフレーズがあります。それらは一般に「あなたが知っているもの (something you know)」とみなされ、多くの場合に単一要素認証子として使用されます。インターネットで公開されている数十億の有効なユーザ名とパスワード、デフォルトパスワードや脆弱なパスワード、レインボーテーブルや最も一般的なパスワードの順序付き辞書など、単一要素認証の継続使用には大きな課題があります。

アプリケーションはユーザに多要素認証への登録を強く推奨し、ユーザが FIDO や U2F トークンなどすでに所有しているトークンを再利用できるようにするか、多要素認証を提供するクレデンシャルサービスプロバイダーにリンクします。

クレデンシャルサービスプロバイダ (Credential Service Provider, CSP) はユーザにフェデレーション ID (federated identity) を提供します。ユーザは一般的な選択肢として Azure AD, Okta, Ping Identity, Google を使用するエンタープライズ ID や、Facebook, Twitter, Google, WeChat を使用するコンシューマ ID など、複数の CSP で複数の ID を持つことがよくあります。このリストはこれらの企業やサービスを支持するものではなく、多くのユーザが多くの ID をすでに持っているという現実を、開発者が検討することを推奨するものです。組織はCSP の ID プルーフィングの強度のリスクプロファイルに従って、既存のユーザ ID との統合を検討すべきです。例えば、政府機関がソーシャルメディア ID を機密システムのログインとして受け入れることはまずありません。偽の ID を作成したり、ID を破棄することが簡単であるためです。一方、モバイルゲーム会社はアクティブなプレイヤーベースを拡大するために、主要なソーシャルメディアプラットフォームと統合する必要があると考えるかもしれません。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.1.1** | [修正] ユーザが設定するパスワードの長さは少なくとも 8 文字である。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.2** | [修正] 64 文字以上のパスワードが許可されている、および 128 文字を超えるパスワードが拒否されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.3** | [修正] パスワードが切り捨てられていない。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.4** | スペースや絵文字などの言語ニュートラルな文字を含む、印字可能な Unicode 文字がパスワードで許可されている。 | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.5** | ユーザがパスワードを変更できる。 | ✓ | ✓ | ✓ | 620 | 5.1.1.2 |
| **2.1.6** | パスワード変更機能にはユーザの現在のパスワードと新しいパスワードが必要である。 | ✓ | ✓ | ✓ | 620 | 5.1.1.2 |
| **2.1.7** | [修正, 2.1.14 へ分割] アカウント登録またはパスワード変更時に送信されるパスワードは少なくとも上位 3000 件の利用可能な一連のパスワードと照合されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.8** | ユーザがより強力なパスワードを設定できるように、パスワード強度メーターが提供されている。 | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.9** | 許可される文字の種類を制限するパスワード構成規則がない。大文字、小文字、数字、または特殊文字を要求すべきではありません。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.10** | [修正, 2.1.13 へ分割, レベル L1 > L2] アプリケーションが定期的なクレデンシャルのローテーションを要求しない。 | | ✓ | ✓ | | 5.1.1.2 |
| **2.1.11** | 「貼り付け」機能、ブラウザパスワードヘルパー、および外部のパスワードマネージャが許可されている。 | ✓ | ✓ | ✓ | 521 | 5.1.1.2 |
| **2.1.12** | [削除] | | | | | |
| **2.1.13** | [追加, 2.1.10 から分割, レベル L1 > L2] アプリケーションがパスワード履歴を保持していない。 | | ✓ | ✓ | | 5.1.1.2 |
| **2.1.14** | [追加, 2.1.7 から分割, レベル L1 > L3] アカウント登録またはパスワード変更時に送信されるパスワードは一連の流出したユーザ名とパスワードのペアと照合されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | | ✓ | | 5.1.1.2 |


## V2.2 一般的な認証子

認証子の俊敏性は将来も使い続けるアプリケーションにとって不可欠です。アプリケーション検証器をリファクタリングして、ユーザの好みに応じて追加の認証子を許可し、非推奨の認証子や安全でない認証子を規則正しく廃止できるようにします。

NIST は SMS を [「制限された」認証子タイプ ("restricted" authenticator type)](https://pages.nist.gov/800-63-FAQ/#q-b01) と考えており、将来のある時点で NIST 800-63 および ASVS から削除される可能性があります。アプリケーションは SMS の使用を必要としないロードマップを計画すべきです。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.2.1** | 耐自動化コントロールが流出済みクレデンシャルテスト攻撃、ブルートフォース攻撃、およびアカウントロックアウト攻撃の軽減に効果的である。このようなコントロールには最も一般的な流出パスワードのブロック、ソフトロックアウト、レート制限、CAPTCHA、試行間の遅延増加、IP アドレスの制限、または場所、デバイスへの最初のログイン、アカウントロック解除の最近の試行などのリスクベースの制限がある。一つのアカウントで一時間あたり 100 回以上の試行失敗が可能ではない。 | ✓ | ✓ | ✓ | 307 | 5.2.2 / 5.1.1.2 / 5.1.4.2 / 5.1.5.2 |
| **2.2.2** | [修正] 弱い認証子 (VoIP や電子メールを使用した帯域外認証など) が認証方式として使用されていない。制限された認証子 (電話または SMS を使用して OTP を配信するために PSTN を使用するもの) はより強力な代替方式が提供される場合か、サービスがそのセキュリティリスクに関する情報をユーザに提供する場合にのみ提供される。 | ✓ | ✓ | ✓ | 304 | 5.2.10 |
| **2.2.3** | クレデンシャルのリセット、電子メールやアドレスの変更、不明な場所や危険な場所からのログインなどの認証詳細の更新後に、セキュアな通知がユーザに送信される。SMS や電子メールではなく、プッシュ通知の使用が推奨されるが、プッシュ通知がない場合、通知に機密情報が開示されていない限り SMS や電子メールは受け入れられる。 | ✓ | ✓ | ✓ | 620 | |
| **2.2.4** | [修正, 2.2.9 へ分割] 多要素暗号化ハードウェア、または単一要素暗号化ハードウェアと OTP デバイスの組み合わせなど、ハードウェアベースの認証子と検証子にフィッシング攻撃に対する偽装耐性を提供する認証子が使用されている。 | | | ✓ | 308 | 5.2.5 |
| **2.2.5** | クレデンシャルサービスプロバイダ (Credential Service Providers, CSP) と認証を検証するアプリケーションが分離されている箇所で、相互認証された TLS が二つのエンドポイント間に配置されている。 | | | ✓ | 319 | 5.2.6 |
| **2.2.6** | ワンタイムパスワード (One-time Password, OTP) デバイス、暗号化認証子、またはルックアップコードの使用を義務付けることにより、リプレイ耐性がある。 | | | ✓ | 308 | 5.2.8 |
| **2.2.7** | [削除, 2.3.2 と重複] | | | | | |
| **2.2.8** | [追加] すべての失敗した認証チャレンジは同じ平均応答時間で応答している。 | | ✓ | ✓ | | |
| **2.2.9** | [追加, 2.2.4 から分割] 多要素認証が必要とされている。つまり、アプリケーションが多要素認証子、または単一要素認証子の組み合わせを使用している。 | | ✓ | ✓ | 308 | 4.2.1 |

## V2.3 認証子ライフサイクル

認証子にはパスワード、ソフトトークン、ハードウェアトークン、生体認証デバイスがあります。認証子のライフサイクルはアプリケーションのセキュリティにとって重要です。任意の人が同一性の証拠がないアカウントを自己登録できる場合、ID アサーションに対する信頼はほとんどありません。Reddit のようなソーシャルメディアサイトでは、それはまったく問題ありません。銀行システムでは、クレデンシャルとデバイスの登録と発行に重点を置くことが、アプリケーションのセキュリティにとって重要です。

注意: パスワードの最大有効期間をもつべきではありません。パスワードローテーションの原因となります。パスワードは定期的に入れ替えるのではなく、侵害されているかどうかを確認する必要があります。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.3.1** | [修正, 2.5.1 からマージ] システムで生成された初期パスワードやアクティベーションコードはセキュアでランダムに生成されており、6文字以上であり、文字と数字を含むことができ、短期間で有効期限が切れ、使い捨てである。これらの初期の秘密は長期パスワードとなることを許可されてはいけない。 | ✓ | ✓ | ✓ | 330 | 5.1.1.2 / A.3 |
| **2.3.2** | [修正] U2F や FIDO トークンなど、ユーザが提供する認証デバイスの登録と使用がサポートされている。 | | ✓ | ✓ | 308 | 6.1.3 |
| **2.3.3** | 期限付き認証子を更新するために十分な更新時間で更新指示が送信されている。 | | ✓ | ✓ | 287 | 6.1.4 |

## V2.4 クレデンシャルストレージ

アーキテクトおよび開発者はコードをビルドまたはリファクタリングする際にこのセクションを順守すべきです。このセクションはソースコードレビューを使用するか、セキュア単体テストまたはセキュア統合テストを通してのみ、完全に検証できます。ペネトレーションテストではこれらの問題を特定できません。

承認された一方向鍵生成関数のリストは NIST 800-63 B section 5.1.1.2 および [OWASP Password Storage Cheatsheet (2021)](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) で詳しく説明されています。

このセクションはペネトレーションテストができないため、管理策は L1 としてマークされていません。但し、このセクションはクレデンシャルが盗まれた場合にそのセキュリティにとって非常に重要であるため、アーキテクチャ、コーディングガイドライン、コードレビューチェックリストに対して ASVS をフォークする場合は、これらの管理策をあなたのプライベートバージョンでは L1 に戻してください。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.4.1** | [修正] パスワードを保存する際に、argon2id, scrypt, bcrypt, PBKDF2 のいずれかのパスワードハッシュ関数が使用されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 916 | 5.1.1.2 |
| **2.4.2** | [削除] | | | | | |
| **2.4.3** | [修正] PBKDF2 が使用されている場合、イテレーションカウントは検証サーバのパフォーマンスが許す限り大きくすべきである。PBKDF2-HMAC-SHA1 では 720,000 回以上、PBKDF2-HMAC-SHA-256 では 310,000 回以上、PBKDF2-HMAC-SHA-512 では 120,000 回以上とする。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 916 | 5.1.1.2 |
| **2.4.4** | [修正] bcrypt が使用されている場合、ワークファクターは検証サーバのパフォーマンスが許す限り大きくすべきであり、 10 以上とする。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 916 | 5.1.1.2 |
| **2.4.5** | [削除] | | | | | |
| **2.4.6** | [追加] argon2id が使用されている場合、構成は検証サーバのパフォーマンスが許す限り強力にすべきである。最小構成は 15 MiB のメモリ、イテレーションカウント 2 回、並列度は 1 とする。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 916 | 5.1.1.2 |
| **2.4.7** | [追加] scrypt が使用されている場合、構成は検証サーバのパフォーマンスが許す限り強力にすべきである。最小 CPU/メモリコストパラメータは (2^16)、最小ブロックサイズは 8 (1024 バイト)、並列化パラメータは 1 とする。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 916 | 5.1.1.2 |

米国標準が言及されている場合、必要に応じて米国標準の代わりに、またはそれに加えて、地域またはローカルの標準を使用できます。

## V2.5 クレデンシャルの回復

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.5.1** | [削除, 2.3.1 へマージ] | | | | | |
| **2.5.2** | パスワードのヒントまたは知識ベースの認証 (いわゆる「秘密の質問」) が存在しない。 | ✓ | ✓ | ✓ | 640 | 5.1.1.2 |
| **2.5.3** | パスワードクレデンシャルリカバリーは決して現在のパスワードを明らかにしない。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 640 | 5.1.1.2 |
| **2.5.4** | 共有アカウントまたはデフォルトアカウントが存在しない (例 "root", "admin", "sa") 。 | ✓ | ✓ | ✓ | 16 | 5.1.1.2 / A.3 |
| **2.5.5** | 認証要素が変更または置換された場合、ユーザにこのイベントが通知される。 | ✓ | ✓ | ✓ | 304 | 6.1.2.3 |
| **2.5.6** | パスワードを忘れた場合、他のリカバリーパスはタイムベースのワンタイムパスワード (time-based OTP, TOTP) またはその他のソフトトークン、モバイルパス、または別のオフラインリカバリーメカニズムなどのセキュアなリカバリーメカニズムを使用する。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 640 | 5.1.1.2 |
| **2.5.7** | [レベル L2 > L1] OTP または多要素認証要素が失われた場合、登録時と同じレベルで同一性証明の証拠が実行される。 | ✓ | ✓ | ✓ | 308 | 6.1.2.3 |

## V2.6 Look-up Secret Verifier

ルックアップシークレットはトランザクション認証番号 (TAN) 、ソーシャルメディアリカバリーコードなどの、事前に生成されたシークレットコードのリスト、またはランダム値のセットを含むグリッドです。これらはユーザにセキュアに配布されます。これらのルックアップコードは一度使用され、すべて使用されると、ルックアップシークレットリストは破棄されます。このタイプの認証子は「あなたが持っているもの (something you have)」とみなされます。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.6.1** | ルックアップシークレットは一度のみ使用できる。 | | ✓ | ✓ | 308 | 5.1.2.2 |
| **2.6.2** | [修正] ルックアップシークレットに十分なランダム性 (112ビットのエントロピー) がある、または112ビット未満のエントロピーの場合、承認されたパスワードストレージハッシュアルゴリズムでハッシュされている。 | | ✓ | ✓ | 330 | 5.1.2.2 |
| **2.6.3** | ルックアップシークレットは予測可能な値などのオフライン攻撃に対して耐性がある。 | | ✓ | ✓ | 310 | 5.1.2.2 |

## V2.7 Out of Band Verifier

過去において、一般的な帯域外検証子はパスワードリセットリンクを含む電子メールまたは SMS でした。攻撃者はこの脆弱なメカニズムを使用して、ある人物の電子メールアカウントを乗っ取り、発見されたリセットリンクを再利用するなど、まだ制御していないアカウントをリセットします。帯域外検証を処理するためのより良い方法があります。

セキュアな帯域外認証子はセキュアなセカンダリチャネルを介して検証子と通信できる物理デバイスです。例えばモバイルデバイスへのプッシュ通信がそうです。このタイプの認証子は「あなたが持っているもの (something you have)」とみなされます。ユーザが認証したいとき、検証アプリケーションは認証子への接続を介して直接もしくは間接的にサードパーティサービスを通じて帯域外認証子にメッセージを送信します。メッセージには認証コード (通常はランダムな六桁の番号またはモーダル承認ダイアログ) が含まれます。検証アプリケーションはプライマリチャネルを通じて認証コードを受信することを待ち、受信した値のハッシュを元の認証コードのハッシュと比較します。それらが一致する場合、帯域外検証子はユーザが認証されたと想定できます。

ASVS はプッシュ通知などの新たな帯域外認証子を開発する開発者はごく少数であると想定しているため、認証 API 、アプリケーション、シングルサインオン実装などの検証子に次の ASVS 管理策が適用されます。新しい帯域外認証子を開発する場合には、NIST 800-63B &sect; 5.1.3.1 を参照してください。

電子メールや VOIP などの安全でない帯域外認証子は許可されていません。PSTN および SMS 認証は現在 NIST により「制限」され、廃止対象であり、プッシュ通知などを推奨しています。電話または SMS の帯域外認証を使用する必要がある場合には、&sect; 5.1.3.3 を参照してください。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.7.1** | SMS や PSTN などのクリアテキスト帯域外 (NIST "制限(restricted)") 認証子がデフォルトで提供されておらず、プッシュ通知などの強力な代替が最初に提供されている。 | ✓ | ✓ | ✓ | 287 | 5.1.3.2 |
| **2.7.2** | 帯域外検証子は10分後に帯域外認証リクエスト、コード、またはトークンが期限切れになる。 | ✓ | ✓ | ✓ | 287 | 5.1.3.2 |
| **2.7.3** | 帯域外検証子の認証リクエスト、コード、またはトークンは元の認証リクエストに対してのみ一度だけ使用できる。 | ✓ | ✓ | ✓ | 287 | 5.1.3.2 |
| **2.7.4** | 帯域外認証子と検証子はセキュアで独立したチャネルを介して通信する。 | ✓ | ✓ | ✓ | 523 | 5.1.3.2 |
| **2.7.5** | 帯域外検証子は認証コードのハッシュバージョンのみを保持している。 | | ✓ | ✓ | 256 | 5.1.3.2 |
| **2.7.6** | 初期認証コードは少なくとも20ビットのエントロピー (通常、6デジタル乱数で十分です) を持ち、セキュアな乱数生成器により生成されている。 | | ✓ | ✓ | 310 | 5.1.3.2 |

## V2.8 ワンタイム検証子

単一要素ワンタイムパスワード (One-time Password, OTP) は継続的に変化する疑似ランダムワンタイムチャレンジを表示する物理トークンまたはソフトトークンです。これらのデバイスはフィッシング (なりすまし) を困難にしますが、不可能ではありません。このタイプの認証子は「あなたが持っているもの (something you have)」とみなされます。多要素トークンは単一要素 OTP と似ていますが、有効な PIN コード、生体認証ロック解除、USB 挿入または NFC ペアリングまたは追加の値 (トランザクション署名計算機など) を入力して最終 OTP を作成する必要があります。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.8.1** | 時間ベースの OTP は期限切れまでの有効期間が定義されている。 | ✓ | ✓ | ✓ | 613 | 5.1.4.2 / 5.1.5.2 |
| **2.8.2** | 送信された OTP を検証するために使用される対称鍵は、ハードウェアセキュリティモジュールまたはセキュアなオペレーティングシステムベースのキーストレージなどを使用して、高度に保護されている。 | | ✓ | ✓ | 320 | 5.1.4.2 / 5.1.5.2|
| **2.8.3** | 承認された暗号化アルゴリズムが OTP の生成、シード、および検証に使用されている。 | | ✓ | ✓ | 326 | 5.1.4.2 / 5.1.5.2 |
| **2.8.4** | 時間ベースの OTP は有効期間内に一度しか使用できない。 | | ✓ | ✓ | 287 | 5.1.4.2 / 5.1.5.2 |
| **2.8.5** | 時間ベースの多要素 OTP が有効期間内に再使用される場合、デバイスの所有者に送信されるセキュアな通知でログに記録され、リジェクトされる。 | | ✓ | ✓ | 287 | 5.1.5.2 |
| **2.8.6** | 物理的な単一要素 OTP 生成器は盗難またはその他の紛失の場合に無効にできる。場所に関係なく、ログインしたセッション全体で無効化がすぐに反映される。 | | ✓ | ✓ | 613 | 5.2.1 |
| **2.8.7** | [レベル L2 > L3] 生体認証子はあなたが持っているものかあなたが知っているもののいずれかと組み合わせて、二次的要素としてのみ使用するように制限されている。 | | | ✓ | 308 | 5.2.3 |
| **2.8.8** | [追加] 時間ベースの多要素 OTP トークンの生成がクライアントのマシンではなくサーバーのシステム時間に基づいている。 | | | ✓ | 367 | 5.1.4.2 / 5.1.5.2 |

## V2.9 暗号化検証子

暗号化セキュリティキーはスマートカードまたは FIDO キーであり、ユーザは認証を完了するために暗号化デバイスをコンピュータに接続するかペアリングする必要があります。検証子はチャレンジナンスを暗号化デバイスまたはソフトウェアに送信し、デバイスまたはソフトウェアはセキュアに保存された暗号化鍵に基づいてレスポンスを計算します。

単一要素暗号化デバイスとソフトウェア、および多要素暗号化デバイスとソフトウェアの要件は同じです。これは暗号化認証子の検証が認証要素の所有を証明するためです。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.9.1** | 検証に使用される暗号化鍵は、トラステッドプラットフォームモジュール (Trusted Platform Module, TPM) やハードウェアセキュリティモジュール (Hardware Security Module, HSM) またはこのセキュアなストレージを使用できる OS サービスを使用するなど、セキュアに保存され、開示から保護されている。 | | ✓ | ✓ | 320 | 5.1.7.2 |
| **2.9.2** | [レベル L2 > L3] チャレンジナンスは少なくとも64ビットの長さがあり、統計的に一意であるか、暗号化デバイスの寿命において一意である。 | | | ✓ | 330 | 5.1.7.2 |
| **2.9.3** | 承認された暗号化アルゴリズムが生成、シード、および検証に使用されている。 | | ✓ | ✓ | 327 | 5.1.7.2 |

## V2.10 サービス認証

このセクションはペネトレーションテスト可能ではないため、L1 要件はありません。

シークレットはフレームワーク、オペレーティングシステム、ハードウェアが提供するサービスを使用してセキュアに保存できます。たとえば Java Key Store, Keychain Access, AWS Secrets Manager はシークレットをセキュアに保存するのに役立つサービスです。最適なセキュリティを実現するには Hardware Security Module や Trusted Platform Module などのハードウェアソリューションを使用すべきです。レベル 2 に準拠するには、フレームワークやオペレーティングシステムが提供するセキュアなサービスにシークレットを保存する必要があります。レベル 3 では、シークレットはハードウェア支援サービスに保存する必要があります。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **2.10.1** | サービス内シークレットはパスワード、API キーや特権アクセスを持つ共有アカウントなど不変のクレデンシャルに依存していない。 | | ✓ | ✓ | 287 | 5.1.1.1 |
| **2.10.2** | サービス認証にパスワードが必要である場合、使用されるサービスアカウントがデフォルトクレデンシャルではない。 (例えば、あるサービスではインストール時に root/root または admin/admin がデフォルトである) | | ✓ | ✓ | 255 | 5.1.1.1 |
| **2.10.3** | ローカルシステムアクセスを含むオフラインリカバリー攻撃を防ぐために、パスワードは十分な保護で保存されている。 | | ✓ | ✓ | 522 | 5.1.1.1 |
| **2.10.4** | パスワード、データベースおよびサードパーティシステムとの統合、シードおよび内部シークレット、および API キー がセキュアに管理され、ソースコードに含まれていない、またはソースコードリポジトリに保存されていない。このようなストレージはオフライン攻撃に耐える必要がある。パスワードストレージにはセキュアなソフトウェアキーストア (L1) 、ハードウェア TPM 、または HSM (L3) の使用を推奨する。 | | ✓ | ✓ | 798 | |

## 追加の米国政府機関要件

米国政府機関には NIST 800-63 に関する必須要件があります。アプリケーションセキュリティ検証標準は常に、アプリのほぼ100%に適用される管理策の約80%であり、高度な管理策や適用が制限される管理策の残りの20%ではありません。そのため、ASVS は特に IAL1/2 および AAL1/2 分類では NIST 800-63 の厳密なサブセットですが、特に IAL3/AAL3 分類に関しては十分に包括的ではありません。

米国政府機関には NIST 800-63 全体をレビューし実装することを強くお勧めします。

## 用語集

| 用語 | 意味 |
| -- | -- |
| CSP | クレデンシャルサービスプロバイダ (Credential Service Provider) は ID プロバイダ (Identity Provider) とも呼ばれます。 |
| 認証子 (Authenticator) | パスワード、トークン、MFA、フェデレーションアサーションなどを認証するコード。 |
| 検証子 (Verifier) | 「認証プロトコルを使用して一つまたは二つの認証子の主張者の所有と制御を検証することにより、主張者の身元を検証するエンティティ。これを行うために、検証子は認証子をサブスクライバの識別子にリンクしそのステータスを確認するクレデンシャルを妥当性確認する必要もある可能性があります。」 |
| OTP | ワンタイムパスワード (One-time password) |
| SFA | 単一要素認証子 (Single-factor authenticators) 。あなたが知っているもの (記憶された秘密、パスワード、パスフレーズ、PIN) 、あなたであるもの (生体情報、指紋、顔スキャン) 、またはあなたが持っているもの (OTP トークン、スマートカードなどの暗号化デバイス) など。 |
| MFA | 多要素認証 (Multi-factor authentication)。二つ以上の単一要素を含む。 |

## 参考情報

詳しくは以下の情報を参照してください。

* [NIST 800-63 - Digital Identity Guidelines](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63-3.pdf)
* [NIST 800-63 A - Enrollment and Identity Proofing](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63a.pdf)
* [NIST 800-63 B - Authentication and Lifecycle Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63b.pdf)
* [NIST 800-63 C - Federation and Assertions](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63c.pdf)
* [NIST 800-63 FAQ](https://pages.nist.gov/800-63-FAQ/)
* [OWASP Testing Guide 4.0: Testing for Authentication](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/04-Authentication_Testing/README.html)
* [OWASP Cheat Sheet - Password storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
* [OWASP Cheat Sheet - Forgot password](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html)
* [OWASP Cheat Sheet - Choosing and using security questions](https://cheatsheetseries.owasp.org/cheatsheets/Choosing_and_Using_Security_Questions_Cheat_Sheet.html)