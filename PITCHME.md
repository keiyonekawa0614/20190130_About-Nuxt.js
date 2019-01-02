### Nuxt.jsでプロダクトを開発して得た知見


---


### アジェンダ

- 1.自己紹介  
- 2.Nuxt.jsを導入したことによるメリット、デメリット  
- 3.工夫したこと  
- 4.まとめ  

---


### 1.自己紹介

- Twitter: @keiuwk0614  
- 所属: 株式会社ITプロパートナーズ
- 金融系SIer出身、サーバサイドエンジニア(Java、PHP)
- Nuxt(Vue)歴半年ぐらい
- フロントエンドをNuxt.js、サーバサイドをLaravelで自社プロダクト「Graspy」を開発中

---

### Graspyサービス紹介

- 中途向けの転職サービスの他、オンライン上でRuby on RailsやAI等のカリキュラムが無料で受講でき、著名人が開催する勉強会に参加したりすることができる **「学び」** と **「出会い」** を作り出すキャリア形成プラットフォーム

---

### 2.Nuxt.jsを導入したことによるメリット、デメリット

+++

### 技術的メリット

+++

- Vuex: store配下にjsファイルをつくると、NuxtがVuexストアにしてくれる |
- vue-router: pagesディレクトリに*.vueファイルを配置することで、自動でルーティングの定義をしてくれる |
- axios: axiosモジュールが用意されており、nuxt.config.jsにbaseURL等のオプションを容易に設定することができる | 

+++ 

** Vue使うならNuxt使った方が良い！！ ** 

+++

### 技術以外のメリット

+++

- 技術ブランディングの強化 | 
 - 昨年のVueFesJapan2018にゴールドスポンサーとして協賛し、社内でモダンな技術を取り入れていることの技術ブランディング
- エンジニア採用 |
 - Nuxt(Vue)で開発したいエンジニアから応募がくることが多くなった
- フロントとサーバサイド分けることで開発が分業化でき、開発効率が上がる |
 
+++ 

### Nuxt.jsを導入したことによるデメリット


---

### 3.工夫したこと

+++

1つ目

+++

<div style="text-align: left;">
課題  
→ Nuxt導入したのに、ページ遷移がめちゃくちゃ遅い   
原因  
→ 1ページ内で複数のAPIを呼びすぎており、HTTPリクエストに時間がかかっている  
解決策  
→ フロント側で複数のHTTPリクエストを纏めて、1リクエストとしてAPIを呼び出すようにした  
</div>

+++

実際にどのようにしたか？

+++

<div style="text-align: left;">
TODO  
axiosのラッパークラスを作成  
↓  
HTTPリクエストURLをqueueに溜める  
↓  
queueに溜めたリクエストURLを1リクエストとしてサーバ側に送信  
↓  
Laravel側で分散  
</div>

+++ 

TODO  
どのくらい効果があったか  
何秒から何秒に縮まり、速度改善した  

+++

2つ目

+++

Client側とServer側でCookieのライブラリを分けた  

<div style="text-align: left;">
Client  
　→universal-cookieを使用  

Server  
　→cookie-universal-nuxtを使用  
</div>

+++ 

何故分けたのか？

+++

<div style="text-align: left;">
結論から言うと、  
<br>
・ client側はuniversal-cookieの方が使い勝手が良い  
・ server側はcookie-universal-nuxtの方が使い勝手が良い  
<br>
から  
</div>
 
+++

+++

<div style="text-align: left;">
universal-cookieの特徴
 - new Cookies();として、client用のcookieをコードのどこでも取得できる
 <br>
cookie-universal-nuxtの特徴
 - app.$cookiesという感じでvueのコンポーネントを必ず経由する形での取得になる

</div>

cookieライブラリのラッパークラスを作成

``` 
import Cookies from 'universal-cookie';

const ClientCookies = () => new Cookies();
const SSRCookies = app => app.$cookies;
export {
    ClientCookies,
    SSRCookies,
};
```
使用する場合、明示的にserviceからimportするときに両者を分けるようにした

```
import { ClientCookies, SSRCookies } from '~/services/cookies';
```



---

### まとめ

- 
