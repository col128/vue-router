# HTML5 히스토리 모드

`vue-router`의 기본 모드는 _hash mode_ 입니다. URL 해시를 사용하여 전체 URL을 시뮬레이트하므로 URL이 변경될 때 페이지가 다시 로드 되지 않습니다.

해시를 제거하기 위해 라우터의 **history 모드** 를 사용할 수 있습니다. `history.pushState` API를 활용하여 페이지를 다시 로드하지 않고도 URL 탐색을 할 수 있습니다 :

``` js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

히스토리 모드를 사용하면 URL이 "정상"으로 보입니다. `http://oursite.com/user/id`. 멋집니다!

그러나 문제는 다음과 같습니다. 우리의 앱이 적절한 서버 설정이 없는 단일 페이지 클라이언트 앱이기 때문에 사용자가 직접 `http://oursite.com/user/id` 에 액세스하면 404 오류가 발생합니다. 이 것은 잘못된 것입니다.

걱정하지 않아도됩니다. 문제를 해결하려면 서버에 간단하게 포괄적인 대체 경로를 추가하기 만하면됩니다. URL이 정적 애셋과 일치하지 않으면 앱이 있는 동일한 `index.html`페이지를 제공해야합니다.

## 서버 설정 예제

#### Apache

```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>
```

#### nginx

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

#### Node.js (Express)

Node.js/Express의 경우 [connect-history-api-fallback middleware](https://github.com/bripkens/connect-history-api-fallback)를 고려해보세요.

## 주의 사항

주의 사항이 있습니다. 여러분의 서버는 404 에러를 보고하지 않을 것입니다. 왜냐하면 모든 발견되지 않은 경로가 이제 `index.html` 파일을 제공하기 때문입니다. 이 문제를 해결하려면 Vue 앱에서 catch-all 라우트를 구현하여 404 페이지를 표시해야합니다.


``` js
const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
```

또는 Node.js 서버를 사용하는 경우 서버 측의 라우터를 사용하여 들어오는 URL을 일치시키고 라우트가 일치하지 않으면 404로 응답하여 폴백을 구현할 수 있습니다.
