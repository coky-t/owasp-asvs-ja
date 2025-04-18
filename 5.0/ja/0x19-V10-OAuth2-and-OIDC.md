# V10 OAuth と OIDC

## 管理目標

OAuth2 (この章では OAuth と呼びます) は委譲された認可のための業界標準フレームワークです。たとえば、OAuth を使用すると、ユーザがクライアントアプリケーションに認可を与えている場合、クライアントアプリケーションはユーザに代わって API (サーバリソース) へのアクセスを取得できます。

それ自体では、OAuth はユーザ認証用に設計されていません。OpenID Connect (OIDC) フレームワークは、OAuth の上にユーザアイデンティティレイヤを追加することで OAuth を拡張します。OIDC は、標準化されたユーザ情報、シングルサインオン (SSO)、セッション管理などの機能をサポートします。OIDC は OAuth の拡張であるため、この章の OAuth 要件は OIDC にも適用します。

OAuth では以下のロールが定義されています。

* OAuth クライアントは、サーバリソースへのアクセスを (発行されたアクセストークンを使用して API を呼び出すなどで) 取得しようとするアプリケーションです。OAuth クライアントは、多くの場合、サーバサイドアプリケーションです。
    * 機密クライアントは、認可サーバで認証するために使用するクレデンシャルの機密性を維持できるクライアントです。
    * パブリッククライアントは、認可サーバで自身を認証するためのクレデンシャルの機密性を維持できません。したがって、client_id と client_secret を使用して自身を認証する代わりに、client_id のみを使用して自身を識別します。
* OAuth リソースサーバ (RS) は OAuth クライアントにリソースを公開するサーバ API です。
* OAuth 認可サーバ (AS) は OAuth クライアントにアクセストークンを発行するサーバアプリケーションです。これらのアクセストークンにより、OAuth クライアントはエンドユーザに代わって、あるいは OAuth クライアント自身の代わりとして RS リソースにアクセスできます。AS は独立したアプリケーションであることが多いのですが、(適切な場合) 適切な RS に統合されることもあります。
* リソースオーナー (RO) は、OAuth クライアントがリソースサーバでホストされているリソースへの制限付きアクセスを取得することを自分の代わりに認可するエンドユーザです。リソースオーナーは認可サーバとやり取りすることでこの委譲された認可に同意します。

OIDC では以下のロールが定義されています。

* 依拠当事者 (RP) は OpenID プロバイダを通じてエンドユーザ認証を要求するクライアントアプリケーションです。OAuth クライアントのロールを担います。
* OpenID プロバイダ (OP) は、エンドユーザを認証し、OIDC クレームを RP に提供できる OAuth AS です。OP はアイデンティティプロバイダ (IdP) であることもありますが、連合シナリオでは、OP とアイデンティティプロバイダ (エンドユーザが認証する場所) は異なるサーバアプリケーションであることもあります。

OAuth と OIDC は当初はサードパーティアプリケーション向けに設計されていました。現在は、ファーストパーティアプリケーションでも使用されることがよくあります。しかし、認証やセッション管理など、ファーストパーティシナリオで使用する場合、プロトコルは複雑さを増し、新たなセキュリティ上の課題を引き起こす可能性があります。

OAuth と OIDC はさまざまなアプリケーションに使用できますが、ASVS とこの章の要件の焦点は Web アプリケーションと API です。

OAuth と OIDC は Web テクノロジの上のロジックとみなすことができるため、他の章にある一般的な要件を常に適用し、この章をコンテキストから外すことはできません。

この章では <https://oauth.net/2/> と <https://openid.net/developers/specs/> にある使用に沿った OAuth2 と OIDC の現在のベストプラクティスを説明します。RFC は成熟していると考えられていますが、頻繁に更新されています。したがって、この章の要件を適用する際には、最新バージョンに合わせることが重要です。詳細については参考情報セクションを参照してください。

この領域の複雑さを考えると、安全な OAuth または OIDC ソリューションには、よく知られている業界標準の認可サーバを使用し、推奨されるセキュリティ構成を適用することが極めて重要です。

用語は付録 A - 用語集で定義されており、OAuth RFC および OIDC 仕様に準拠しています。この章では、OIDC 用語は OIDC 固有の要件にのみ使用され、それ以外は OAuth 用語が使用されることに注意してください。

OAuth および OIDC のコンテキストでは、「トークン」という用語は以下のものを指します。

* アクセストークン。これは RS によってのみ使用されるものとし、イントロスペクションを使用して検証されるリファレンストークン、または何らかの鍵マテリアルを使用して検証される自己完結型トークンのいずれかになります。
* リフレッシュトークン。これはトークンを発行した認可サーバによってのみ使用されます。
* OIDC ID トークン。これは認可フローをトリガーしたクライアントによってのみ使用されます。

ログアウトトークンなどの他の種類のトークンはこの章の対象外です。

この章のいくつかの要件のリスクレベルは、クライアントが機密クライアントであるか、パブリッククライアントとみなされるかによって異なります。強力なクライアント認証を使用することで多くの攻撃ベクトルを軽減するため、L1 アプリケーションに機密クライアントを使用する場合、いくつかの要件が緩和される可能性があります。

## V10.1 一般的な OAuth と OIDC セキュリティ

このセクションは、保護されたリソースにアクセスするために OAuth または OIDC に依存するブラウザベースの JavaScript アプリケーションを構築する際に利用可能な主要なアーキテクチャパターンなど、OAuth や OIDC を使用するすべてのアプリケーションに適用する一般的なアーキテクチャ要件をカバーします。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.1.1** | トークンは厳密に必要とするコンポーネントにのみ送信される。たとえば、ブラウザベースの JavaScript アプリケーションで Backend for Frontend パターンを使用する場合、アクセストークンとリフレッシュトークンはバックエンドでのみアクセス可能である。 | 2 | v5.0.be-51.1.1 |
| **10.1.2** | クライアントは、認可サーバからの値 (認可コードや ID トークンなど) が、同じユーザエージェントセッションとトランザクションによって開始された認可フローから得られた値である場合にのみ、その値を受け入れている。これには、コード交換のための証明鍵 (PKCE) 'code_verifier'、'state'、OIDC 'nonce' など、クライアントが生成したシークレットが推測不可能であり、トランザクションに固有であり、トランザクションが開始されたクライアントとユーザエージェントセッションの両方に安全にバインドされていることが必要である。 | 2 | v5.0.be-51.1.2 |

## V10.2 OAuth クライアント

これらの要件は OAuth クライアントアプリケーションの責務を詳述しています。クライアントには、たとえば、Web サーババックエンド (多くの場合 Backend For Frontend (BFF) として機能します)、バックエンドサービス統合、フロントエンドシングルページアプリケーション (SPA、別名ブラウザベースのアプリケーション) があります。

一般的に、バックエンドクライアントは機密クライアントとみなされ、フロントエンドクライアントはパブリッククライアントとみなされます。しかし、OAuth 動的クライアント登録を実行する場合、エンドユーザデバイスで実行するネイティブアプリケーションは機密クライアントとみなされる可能性があります。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.2.1** | コードフローが使用される場合、OAuth クライアントはコード交換のための証明鍵 (PKCE) 機能を使用するか、認可リクエストで送信された state パラメータをチェックすることで、トークンリクエストをトリガーするクロスサイトリクエストフォージェリ (CSRF) 攻撃から保護している。 | 2 | v5.0.be-51.2.2 |
| **10.2.2** | OAuth クライアントが複数の認可サーバとやり取りできる場合、ミックスアップ攻撃に対して防御している。たとえば、認可サーバが 'iss' パラメータ値を返し、認可レスポンスとトークンレスポンスでそれを検証することを要求できる。 | 2 | v5.0.be-51.2.1 |
| **10.2.3** | OAuth クライアントは、認可サーバへのリクエストで必要なスコープ (またはその他の認可パラメータ) のみをリクエストしている。 | 3 | v5.0.be-51.2.3 |

## V10.3 OAuth リソースサーバ

ASVS とこの章のコンテキストでは、リソースサーバは API です。安全なアクセスを提供するには、リソースサーバは以下を行う必要があります。

* トークン形式と関連するプロトコル仕様 (JWT バリデーションや OAuth トークンイントロスペクションなど) に従って、アクセストークンを検証します。
* 有効な場合、アクセストークンの情報と付与されたパーミッションに基づいて認可決定を実施します。たとえば、リソースサーバは、クライアント (RO に代わって動作する) が要求されたリソースにアクセスするための認可があることを検証する必要があります。

したがって、ここでリストされている要件は OAuth や OIDC に固有のものであり、トークンバリデーション後、トークンの情報に基づいて認可を実行する前に実行する必要があります。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.3.1** | リソースサーバは、そのサービス (オーディエンス) での使用を意図したアクセストークンのみを受け付けている。オーディエンスは、構造化されたアクセストークン (JWT の 'aud' クレームなど) に含めることや、トークンイントロスペクションエンドポイントを使用してチェックできる。 | 2 | v5.0.be-51.3.2 |
| **10.3.2** | リソースサーバは、デリゲートされた認可を定義するアクセストークンからのクレームに基づいて認可決定を実施している。'sub', 'scope', 'authorization_details' などのクレームが存在する存在する場合、それらは決定の一部となる必要がある。 | 2 | v5.0.be-51.3.3 |
| **10.3.3** | アクセス制御の決定がアクセストークン (JWT または関連するトークンイントロスペクションレスポンス) から一意のユーザを識別する必要がある場合、リソースサーバは他のユーザーに再割り当てできないクレームからユーザを識別する。通常、これは 'iss' と 'sub' のクレームの組み合わせを使用することを意味する。 | 2 | v5.0.be-51.3.4 |
| **10.3.4** | リソースサーバが特定の認証強度、認証方法、認証日時を要求する場合、リソースサーバは提示されたアクセストークンがこれらの制約を満たすことを検証している。たとえば、存在する場合、それぞれ OIDC 'acr', 'amr', 'auth_time' クレームを使用する。 | 2 | v5.0.be-51.3.5 |
| **10.3.5** | リソースサーバは、OAuth 2 の相互 TLS または OAuth 2 Demonstration of Proof of Possession (DPoP) のいずれかの送信者制約のあるアクセストークンを要求することで、盗まれたアクセストークンの使用やアクセストークンのリプレイを (認可されていない第三者から) 防止する。 | 3 | v5.0.be-51.3.1 |

## V10.4 OAuth 認可サーバ

これらの要件は OpenID プロバイダを含む OAuth 認可サーバの責務を詳述しています。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.4.1** | 認可サーバは、正確な文字列比較を使用して、事前登録された URI のクライアント固有の許可リストに基づいて、リダイレクト URI を検証している。 | 1 | v5.0.be-51.4.6 |
| **10.4.2** | 認可サーバが認可レスポンスで認可コードを返す場合、そのコードはトークンリクエストに対して一度のみ使用できる。アクセストークンの発行にすでに使用されている認可コードでの二回目の有効なリクエストに対しては、認可サーバはトークンリクエストを拒否して、認可コードに関連する発行済みトークンをすべて取り消す必要がある。 | 1 | v5.0.be-51.4.1 |
| **10.4.3** | 認可コードは有効期間を短くしている。最大有効期間は、L1 および L2 アプリケーションで 10 分まで、L3 アプリケーションで 1 分までである。 | 1 | v5.0.be-51.4.2 |
| **10.4.4** | 特定のクライアントに対して、認可サーバはこのクライアントが使用する必要があるグラントの使用のみを許可している。'token' (暗黙的フロー) と 'password' (リソースオーナーのパスワードクレデンシャルフロー) のグラントはもはや使用してはならないことに注意する。 | 1 | v5.0.be-51.4.5 |
| **10.4.5** | 認可サーバは、できれば送信者制約リフレッシュトークン (つまり、Demonstrating Proof of Possession (DPoP) または Certificate-Bound Access Tokens (mTLS)) を使用して、パブリッククライアントに対するリフレッシュトークンリプレイ攻撃を軽減している。L1 および L2 アプリケーションの場合、リフレッシュトークンローテーションを使用できる。リフレッシュトークンローテーションを使用する場合、認可サーバが使用後にリフレッシュトークンを無効化する必要があり、すでに使用されて無効化されたリフレッシュトークンが提供された場合には、その認可のためのすべてのリフレッシュトークンを失効している。 | 1 | v5.0.be-51.4.4 |
| **10.4.6** | コードグラントを使用する場合、認可サーバはコード交換のための証明鍵 (PKCE) を要求することで認可コード傍受攻撃を軽減している。認可リクエストでは、認可サーバは有効な 'code_challenge' 値を要求する必要があり、'plain' の 'code_challenge_method' 値を受け入れてはいけない。トークンリクエストでは、'code_verifier' パラメータのバリデーションを要求する必要がある。 | 2 | v5.0.be-51.4.3 |
| **10.4.7** | 認可サーバが認可されていない動的クライアント登録をサポートしている場合、悪意のあるクライアントアプリケーションのリスクを緩和している。登録された URI などのクライアントメタデータを検証し、ユーザーの同意を確保し、信頼できないクライアントアプリケーションでの認可リクエストを処理する前にユーザに警告する必要がある。 | 2 | v5.0.be-51.4.16 |
| **10.4.8** | リフレッシュトークンは、スライディングリフレッシュトークン有効期限が適用されている場合も含め、絶対的な有効期限を持つ。 | 2 | v5.0.be-51.4.13 |
| **10.4.9** | 悪意のあるクライアントや盗まれたトークンのリスクを軽減するために、認可されたユーザが認可サーバのユーザインタフェースを使用して、リフレッシュトークンとリファレンスアクセストークンを失効させることができる。 | 2 | v5.0.be-51.4.14 |
| **10.4.10** | コンフィデンシャルクライアントは、トークンリクエスト、プッシュ認可リクエスト (PAR)、トークン失効リクエストなどのクライアントから認可済みサーバへのバックチャネルリクエストに対して認証されている。 | 2 | v5.0.be-51.4.7 |
| **10.4.11** | 認可サーバ設定では、必要なスコープのみを OAuth クライアントに割り当てている。 | 2 | v5.0.be-51.4.8 |
| **10.4.12** | 特定のクライアントに対して、認可サーバはこのクライアントが使用する必要がある 'response_mode' 値のみを許可している。たとえば、認可サーバがこの値を期待値と検証したり、プッシュ認可リクエスト (PAR) や JWT で保護された認可リクエスト (JAR) を使用している。 | 3 | v5.0.be-51.4.12 |
| **10.4.13** | 'code' グラントタイプは常にプッシュ認可リクエスト (PAR) と一緒に使用されている。 | 3 | v5.0.be-51.4.9 |
| **10.4.14** | 認可サーバは、mTLS 証明書バインディングまたは Demonstration of Proof of Possession (DPoP) のいずれかを使用して、送信者制約 (Proof-of-Possession) アクセストークンのみを発行する。 | 3 | v5.0.be-51.4.11 |
| **10.4.15** | サーバサイドクライアント (エンドユーザデバイス上で実行されない) では、認可サーバは 'authorization_details' パラメータ値がクライアントバックエンドからのものであり、ユーザがそれを改竄していないことを確保している。たとえば、プッシュ認可リクエスト (PAR) または JWT で保護された認可リクエスト (JAR) の使用を要求する。 | 3 | v5.0.be-51.4.15 |
| **10.4.16** | クライアントはコンフィデンシャルであり、認可サーバは強力なクライアント認証方法 (公開鍵暗号に基づき、リプレイ攻撃に耐性がある)、すなわち 'mTLS' または 'private-key-jwt' の使用を要求する。 | 3 | v5.0.be-51.4.10 |

## V10.5 OIDC クライアント

OIDC 依拠当事者は OAuth クライアントとして機能するため、「OAuth クライアント」セクションの要件も適用します。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.5.1** | クライアント (依拠当事者) は ID トークンのリプレイ攻撃を軽減している。たとえば、ID トークンの 'nonce' クレームが OpenID プロバイダへの認証リクエスト (OAuth2 では認可サーバに送信される認可リクエストと呼ばれる) で送信された 'nonce' 値と一致することを確認している。 | 2 | v5.0.be-51.5.1 |
| **10.5.2** | クライアントは ID トークンクレーム (通常は 'sub' クレーム) からユーザを一意に識別するが、(ID プロバイダのスコープでは) 他のユーザに再割り当てできない。 | 2 | v5.0.be-51.5.2 |
| **10.5.3** | クライアントは悪意のある認可サーバが認可サーバメタデータを通じて別の認可サーバになりすまそうとする試みを拒否する。認可サーバメタデータの発行者 URL が、クライアントが期待する事前設定された発行者 URL と正確に一致しない場合、クライアントは認可サーバメタデータを拒否する必要がある。 | 2 | v5.0.be-51.5.3 |
| **10.5.4** | クライアントは、トークンの 'aud' クレームがクライアントの 'client_id' 値と等しいことをチェックすることで、ID トークンがそのクライアント (オーディエンス) のために使用されることを意図していることを検証している。 | 2 | v5.0.be-51.5.4 |
| **10.5.5** | 特定の認証強度、認証方法、認証日時を要求する場合、依拠当事者 (RP) は提示された ID トークンがこれらの制約 (それぞれ 'acr', 'amr', 'auth_time' クレームを使用する) を満たすことを検証している。 | 2 | v5.0.be-51.5.5 |

## V10.6 OpenID プロバイダ

OpenID プロバイダは OAuth 認可サーバとして機能するため、「OAuth 認可サーバ」セクションの要件も適用します。

ID トークンフロー (コードフローではない) を使用する場合、アクセストークンは発行されず、OAuth AS の要件の多くは適用されないことに注意してください。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.6.1** | OpenID プロバイダはレスポンスモードに 'code', 'ciba', 'id_token', 'id_token code' の値のみを許可している。'code' は 'id_token code' (OIDC ハイブリッドフロー) よりも優先されること、'token' (任意の暗黙的フロー) は使用してはならないことに注意する。 | 2 | v5.0.be-51.6.1 |

## V10.7 同意管理

これらの要件は認可サーバによるユーザの同意の検証をカバーします。適切なユーザ同意の検証を行わないと、悪意にある行為者がなりすましやソーシャルエンジニアリングを通じてユーザに代わってパーミッションを取得する可能性があります。

| # | 説明 | レベル | #v5.0.be |
| :---: | :--- | :---: | :---: |
| **10.7.1** | 認可サーバは、ユーザが各認可リクエストに同意することを確保している。クライアントのアイデンティティを保証できない場合、認可サーバは常に明示的にユーザに同意を求める必要がある。 | 2 | v5.0.be-51.7.1 |
| **10.7.2** | 認可サーバはユーザの同意を求める場合、同意される内容について十分かつ明確な情報を提示している。該当する場合、これにはリクエストされた認可の性質 (通常はスコープ、リソースサーバ、リッチ認可リクエスト (RAR) 認可の詳細に基づく)、認可されたアプリケーションのアイデンティティ、これらの認可の有効期間を含む。 | 2 | v5.0.be-51.7.2 |
| **10.7.3** | ユーザは、認可サーバを通じてユーザが付与した同意を確認、変更、取消できる。 | 2 | v5.0.be-51.7.3 |

## 参考情報

OAuth について詳しくは以下の情報を参照してください。

* [oauth.net](https://oauth.net/)
* [OWASP Cheat Sheet: OAuth 2.0 Protocol Cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/OAuth2_Cheat_Sheet.html)

ASVS の OAuth 関連の要件については、以下の発行済みおよびドラフト状態の RFC が使用されます。

* [RFC6749 The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)
* [RFC6750 The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc6750)
* [RFC6819 OAuth 2.0 Threat Model and Security Considerations](https://datatracker.ietf.org/doc/html/rfc6819)
* [RFC7636 Proof Key for Code Exchange by OAuth Public Clients](https://datatracker.ietf.org/doc/html/rfc7636)
* [RFC7591 OAuth 2.0 Dynamic Client Registration Protocol](https://datatracker.ietf.org/doc/html/rfc7591)
* [RFC8628 OAuth 2.0 Device Authorization Grant](https://datatracker.ietf.org/doc/html/rfc8628)
* [RFC8707 Resource Indicators for OAuth 2.0](https://datatracker.ietf.org/doc/html/rfc8707)
* [RFC9068 JSON Web Token (JWT) Profile for OAuth 2.0 Access Tokens](https://datatracker.ietf.org/doc/html/rfc9068)
* [RFC9126 OAuth 2.0 Pushed Authorization Requests](https://datatracker.ietf.org/doc/html/rfc9126)
* [RFC9207 OAuth 2.0 Authorization Server Issuer Identification](https://datatracker.ietf.org/doc/html/rfc9207)
* [RFC9396 OAuth 2.0 Rich Authorization Requests](https://datatracker.ietf.org/doc/html/rfc9396)
* [RFC9449 OAuth 2.0 Demonstrating Proof of Possession (DPoP)](https://datatracker.ietf.org/doc/html/rfc9449)
* [RFC9700 Best Current Practice for OAuth 2.0 Security](https://datatracker.ietf.org/doc/html/rfc9700)
* [draft OAuth 2.0 for Browser-Based Applications](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps)<!-- recheck on release -->
* [draft The OAuth 2.1 Authorization Framework](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-12)<!-- recheck on release -->

OpenID Connect について詳しくは以下の情報を参照してください。

* [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)
* [FAPI 2.0 Security Profile](https://openid.net/specs/fapi-security-profile-2_0-final.html)
