# V3 セッション管理

## 管理目標

Web ベースのアプリケーションやステートフル API の中核となるコンポーネントの一つは、それと対話するユーザやデバイスの状態を制御および保守するメカニズムです。セッション管理はステートレスプロトコルをステートフルに変更します。これはさまざまなユーザやデバイスを区別するために重要です。

検証対象のアプリケーションが以下の上位のセッション管理要件を満たすことを確認します。

* セッションは各個人に固有のものであり、推測や共有することはできません。
* セッションは不要になったときや非アクティブ期間内にタイムアウトしたときに無効になります。

前述のように、これらの要件は、一般的な脅威や一般的に悪用される認証脆弱性にフォーカスした、選択された NIST 800-63b 管理策の準拠サブセットとなるように適合されています。以前の検証要件は廃止、重複削除、またはほとんどの場合、必須の [NIST 800-63b](https://pages.nist.gov/800-63-3/sp800-63b.html) 要件の意図と厳密に一致するように調整されています。

## V3.1 基本的なセッション管理セキュリティ

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.1.1** | アプリケーションが URL パラメータにセッショントークンを漏洩しない。 | ✓ | ✓ | ✓ | 598 | |

## V3.2 セッションバインディング

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.2.1** | アプリケーションがユーザ認証時に新しいセッショントークンを生成する。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 384 | 7.1 |
| **3.2.2** | [修正] セッショントークンが少なくとも 128 ビットのエントロピーを有する。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 331 | 7.1 |
| **3.2.3** | [削除] | | | | | |
| **3.2.4** | セッショントークンが承認済みの暗号化アルゴリズムを使用して生成されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 331 | 7.1 |

TLS や他のセキュアなトランスポートチャネルはセッション管理に必須です。これは通信セキュリティの章でカバーされています。

## V3.3 セッションの終了

セッションタイムアウトは NIST 800-63 と整合しています。これはセキュリティ標準で旧来許可されているよりもはるかに長いセッションタイムアウトを許可します。組織は以下の表をレビューすべきです。また、アプリケーションのリスクに基づいてより長いタイムアウトが望ましい場合には、NIST 値をセッションアイドルタイムアウトの上限にすべきです。

このコンテキストでの L1 は IAL1/AAL1, L2 は IAL2/AAL3, L3 は IAL3/AAL3 です。IAL2/AAL2 および IAL3/AAL3 では、アイドルタイムアウトはより短くなり、ログアウトやセッションを再開するための再認証にはアイドルタイムの下限とします。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.3.1** | 戻るボタンやダウンストリーム Relying Party が Relying Party を含めて認証済みセッションを再開しないように、ログアウトおよび有効期限切れがセッショントークンを無効にする。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 613 | 7.1 |
| **3.3.2** | Authenticator がユーザにログインしたままでいることを許可する場合には、アクティブに使用されているときとアイドル期間後の両方で定期的に再認証が行われる。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | 30 日 | 12 時間または 30分の非アクティブ, 2FA はオプション | 12 時間または 15 分の非アクティブ, 2FA を要求 | 613 | 7.2 |
| **3.3.3** | [レベル L2 > L1] パスワードが正常に変更 (パスワードリセットやリカバリーによる変更を含む) された後、アプリケーションが他のすべてのアクティブセッションを終了するためのオプションを提供していること、およびこれがアプリケーション、Federated ログイン (存在する場合) 、Relying Party すべてに有効である。 | ✓ | ✓ | ✓ | 613 | |
| **3.3.4** | [削除, 3.3.3 と重複] | | | | | |
| **3.3.5** | [追加] 認証を必要とするすべてのページでログアウト機能に簡単かつ目に見える形でアクセスできる。 | ✓ | ✓ | ✓ | | |

## V3.4 Cookie ベースのセッション管理

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.4.1** | Cookie ベースのセッショントークンには 'Secure' 属性が設定されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 614 | 7.1.1 |
| **3.4.2** | Cookie ベースのセッショントークンには 'HttpOnly' 属性が設定されている。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 1004 | 7.1.1 |
| **3.4.3** | Cookie ベースのセッショントークンには、クロスサイトリクエストフォージェリ攻撃への露出を制限するために、'SameSite' 属性を利用している。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 16 | 7.1.1 |
| **3.4.4** | [修正] Cookie ベースのセッショントークンには "__Host-" プレフィックスを使用しており、Cookie は最初に Cookie を設定したホストにのみ送信されている。 | ✓ | ✓ | ✓ | 16 | 7.1.1 |
| **3.4.5** | [修正] セッション Cookie を開示する可能性があるセッション Cookie を設定または使用する他のアプリケーションと一緒のドメイン名の下でアプリケーションが公開されている場合には、可能な最も正確なパスを使用して Cookie ベースのセッショントークンに path 属性を設定している。 ([C6](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 16 | 7.1.1 |
| **3.4.6** | [追加] Cookie ベースのセッショントークンは Set-Cookie ヘッダと Cookie ヘッダでのみ転送されている。 | ✓ | ✓ | ✓ | 200 | |

## V3.5 トークンベースのセッション管理

トークンベースのセッション管理には JWT, OAuth, SAML, API キーが含まれます。これらのうち、API キーは脆弱であることが分かっているため、新しいコードでは使用すべきではありません。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.5.1** | [削除, 3.3.4 と重複] | | | | | |
| **3.5.2** | レガシーな実装を除いて、アプリケーションが静的な API シークレットや鍵ではなくセッショントークンを使用している。 | | ✓ | ✓ | 798 | |
| **3.5.3** | [修正, レベル L2 > L1] ステートレスセッショントークンは改竄から保護するためにデジタル署名を使用している。 | ✓ | ✓ | ✓ | 345 | |
| **3.5.4** | [追加] バックエンドサービスで JWT の有効期限がチェックされている。 | ✓ | ✓ | ✓ | 613 | |
| **3.5.5** | [追加] バックエンドサービスで完全性アルゴリズム妥当性確認が行われている。また、有効なアルゴリズムタイプのみがバックエンドサービスに適用されている。 | ✓ | ✓ | ✓ | 347 | |
| **3.5.6** | [追加] バックエンドサービスで発行者、サブジェクト、オーディエンスなどの JWT ペイロードクレームの適切な妥当性確認が行われている。 | ✓ | ✓ | ✓ | 287 | |

## V3.6 Federated 再認証

このセクションは Relying Party (RP) またはクレデンシャルサービスプロバイダ (Credential Service Provider, CSP) のコードを書いている人に関するものです。これらの機能を実装するコードに依存する場合には、これらの問題が正しく処理されていることを確認します。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.6.1** | [修正] Relying Party がクレデンシャルサービスプロバイダ (Credential Service Provider, CSP) に最大認証時間を指定していること、および CSP がその期間内にセッションを使用していない場合には CSP がユーザを再認証する。 | | | ✓ | 613 | 7.2.1 |
| **3.6.2** | RP がユーザを再認証する必要があるかどうかを RP が判断できるようにするために、クレデンシャルサービスプロバイダ (Credential Service Provider, CSP) が Relying Party に最後の認証イベントを通知する。 | | | ✓ | 613| 7.2.1 |

## V3.7 セッション管理の悪用に対する防御

少数のセッション管理攻撃がありますが、そのいくつかはセッションのユーザエクスペリエンス (UX) に関連しています。以前は、ISO 27002 要件に基づいて、ASVS は複数の同時セッションをブロックすることを要求していました。最近のユーザは多くのデバイスを使用しているだけでなく、アプリはブラウザセッションを使用しない API であるため、同時セッションのブロックはもはや適切ではありません。これらの実装のほとんどでは、最終認証者が勝利します。それはおおよそ攻撃者です。このセクションでは、コードを使用したセッション管理攻撃の阻止、遅延、検出に関する主要なガイダンスを提供します。

### ハーフオープン攻撃の説明

2018年初頭、攻撃者が「ハーフオープン攻撃」と呼ぶものを使用して、いくつかの金融機関が侵害されました。この用語は業界に受け入れられています。攻撃者はさまざまな独自のコードベースを使用して複数の機関を攻撃しました。実際、同じ機関内でもコードベースが異なるようです。ハーフオープン攻撃は、多くの既存の認証、セッション管理、およびアクセス制御システムで一般的にみられる設計パターンの欠陥を悪用しています。

攻撃者は、クレデンシャルをロック、リセット、リカバリーしようと試みることにより、ハーフオープン攻撃を開始します。一般的なセッション管理デザインパターンは、未認証、半認証 (パスワードリセット、ユーザ名忘れ) 、完全認証コードの間でユーザプロファイルセッションオブジェクトやモデルを再利用します。このデザインパターンは、パスワードハッシュやロールなど、被害者のプロファイルを含む有効なセッションオブジェクトやまたはトークンを入力します。コントローラまたはルータのアクセス制御チェックでユーザが完全にログインしていることを正しく検証していない場合、攻撃者はそのユーザとして行動できるかもしれません。攻撃には、ユーザのパスワードを既知の値に変更する、有効なパスワードリセットを実行するために電子メールアドレスを更新する、多要素認証を無効にする、新しい MFA デバイスを登録する、API キーを公開または変更する、などがあります。

| # | 説明 | L1 | L2 | L3 | CWE | [NIST &sect;](https://pages.nist.gov/800-63-3/sp800-63b.html) |
| :---: | :--- | :---: | :---:| :---: | :---: | :---: |
| **3.7.1** | [修正] 機密性の高いトランザクションやアカウント変更を許可する前に、アプリケーションが再認証や二次要素検証を必要としている。 | ✓ | ✓ | ✓ | 306 | |

## 参考情報

詳しくは以下の情報を参照してください。

* [OWASP Testing Guide 4.0: Session Management Testing](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/06-Session_Management_Testing/README.html)
* [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
* [Set-Cookie __Host- prefix details](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#Directives)