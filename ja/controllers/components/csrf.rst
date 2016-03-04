クロスサイトリクエストフォージェリ
###########################

..
    Cross Site Request Forgery

..
    By enabling the CSRF Component you get protection against attacks. `CSRF
    <http://en.wikipedia.org/wiki/Cross-site_request_forgery>`_ or Cross Site
    Request Forgery is a common vulnerability in web applications. It allows an
    attacker to capture and replay a previous request, and sometimes submit data
    requests using image tags or resources on other domains.

CSRF Componentを有効にすることにより、攻撃に対するプロテクションを得ることが出来ます。
`CSRF<https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA>`_ もしくは「クロスサイトリクエストフォージェリ」は一般的な脆弱性です。
この脆弱性によって攻撃者は以前のリクエストをキャプチャして再現したり、ときには別ドメインのイメージタグやリソースを使ってデータリクエストをサブミットすることができます。

..
    The CsrfComponent works by setting a cookie to the user's browser. When forms
    are created with the :php:class:`Cake\\View\\Helper\\FormHelper`, a hidden field
    is added containing the CSRF token. During the ``Controller.startup`` event, if
    the request is a POST, PUT, DELETE, PATCH request the component will compare the
    request data & cookie value. If either is missing or the two values mismatch the
    component will throw a
    :php:class:`Cake\\Network\\Exception\\InvalidCsrfTokenException`.


CsrfComponentは利用者のブラウザでCookieを有効にすることにより効果を発します。
:php:class:`Cake\\View\\Helper\\FormHelper` でフォームが作られ、CSRFトークンが含まる隠し要素が追加されます。
``Controller.startup`` イベント中に、POST,PUT,DELETE,PATCHリクエストがあったときにリクエストデータとCookieの値で比較します。
もし見つからなかったり2つの値が不一致した場合、CsrfComponentは :php:class:`Cake\\Network\\Exception\\InvalidCsrfTokenException` を投げます。

..
    note
    You should always verify the HTTP method being used before executing
    side-effects. You should :ref:`check the HTTP method <check-the-request>` or
    use :php:meth:`Cake\\Network\\Request::allowMethod()` to ensure the correct
    HTTP method is used.

.. note::
    あなたはHTTPが副作用を実行中となる前に毎回確かめるべきである。
    あなたは :ref:`check the HTTP method <check-the-request>` か :php:meth:`Cake\\Network\\Request::allowMethod()` を使いHTTPメソッドが使われることを確保するべきである。

..
    versionadded 3.1
    The exception type changed from
    :php:class:`Cake\\Network\\Exception\\ForbiddenException` to
    :php:class:`Cake\\Network\\Exception\\InvalidCsrfTokenException`.


.. versionadded:: 3.1

    Exceptionの種類が
    :php:class:`Cake\\Network\\Exception\\ForbiddenException` から
    :php:class:`Cake\\Network\\Exception\\InvalidCsrfTokenException` に変更されました。


CsrfComponentの使い方
=======================

..
    Using the CsrfComponent

..
    Simply by adding the ``CsrfComponent`` to your components array,
    you can benefit from the CSRF protection it provides


``CsrfComponent`` を追加することによりCSRF対策を約束することができます。 ::

    public function initialize()
    {
        parent::initialize();
        $this->loadComponent('Csrf');
    }

..
    Settings can be passed into the component through your component's settings.
    The available configuration options are:


そのComponentを通過する設定をあなたのComponentで設定できます。
オプションを設定することにより得られる事：

..
    ``cookieName`` The name of the cookie to send. Defaults to ``csrfToken``.
    ``expiry`` How long the CSRF token should last. Defaults to browser session.
    Accepts ``strtotime`` values as of 3.1
    ``secure`` Whether or not the cookie will be set with the Secure flag. That is,
    the cookie will only be set on a HTTPS connection and any attempt over normal HTTP
    will fail. Defaults to ``false``.
    ``field`` The form field to check. Defaults to ``_csrfToken``. Changing this
    will also require configuring FormHelper.

- ``cookieName`` 送信されるCookieの名称です。デフォルトは``csrfToken``です。
- ``expiry`` CSRFトークンが有効な期間です。デフォルトはブラウザセッションです。3.1以降で ``strtotime`` に対応しています。
- ``secure`` trueならセキュアフラグつきでCookieをセットされます。つまり、HTTPS接続のときだけCookieがセットされます、また通常のHTTPで接続しようとした場合は、どんな接続でも失敗します。デフォルトは ``false`` です。
- ``field`` フォームのチェックです。デフォルトは ``_csrfToken`` です。これを変更するとフォームヘルパーでの設定が必要となるでしょう。

..
    When enabled, you can access the current CSRF token on the request object
    $token = $this->request->param('_csrfToken');

有効になったとき、リクエストオブジェクト上にある現在のCSRFトークンにあなたはアクセスできます。::

    $token = $this->request->param('_csrfToken');

フォームヘルパーとの統合
==========================

..
    Integration with FormHelper

..
    The CsrfComponent integrates seamlessly with ``FormHelper``. Each time you
    create a form with FormHelper, it will insert a hidden field containing the CSRF
    token.




The CsrfComponent integrates seamlessly with ``FormHelper``.
Each time you create a form with FormHelper, it will insert a hidden field containing the CSRF token.



.. note::

    When using the CsrfComponent you should always start your forms with the
    FormHelper. If you do not, you will need to manually create hidden inputs in
    each of your forms.

CSRF Protection and AJAX Requests
==================================

In addition to request data parameters, CSRF tokens can be submitted through
a special ``X-CSRF-Token`` header. Using a header often makes it easier to
integrate a CSRF token with JavaScript heavy applications, or XML/JSON based API
endpoints.

Disabling the CSRF Component for Specific Actions
=================================================

While not recommended, you may want to disable the CsrfComponent on certain
requests. You can do this using the controller's event dispatcher, during the
``beforeFilter()`` method::

    public function beforeFilter(Event $event)
    {
        $this->eventManager()->off($this->Csrf);
    }

.. meta::
    :title lang=ja: Csrf
    :keywords lang=ja: configurable parameters,security component,configuration parameters,invalid request,csrf,submission
