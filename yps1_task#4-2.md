### wordpressのセキュリティ設定をする
1：WordPressで構築されていることを隠す  
WordPressが自動で出力するタグを隠し、ソースを見てもサイトがWordPressで構築されていることがわからないようにする  
functions.phpに不要なタグの出力を停止する以下のような設定を追記することで、その対応が可能
```
remove_action('wp_head', 'wp_generator');// WordPressのバージョン
remove_action('wp_head', 'wp_shortlink_wp_head');// 短縮URLのlink
remove_action('wp_head', 'wlwmanifest_link');// ブログエディターのマニフェストファイル
remove_action('wp_head', 'rsd_link');// 外部から編集するためのAPI
remove_action('wp_head', 'feed_links_extra', 3);// フィードへのリンク
remove_action('wp_head', 'print_emoji_detection_script', 7);// 絵文字に関するJavaScript
remove_action('wp_head', 'rel_canonical');// カノニカル
remove_action('wp_print_styles', 'print_emoji_styles');// 絵文字に関するCSS
remove_action('admin_print_scripts', 'print_emoji_detection_script');// 絵文字に関するJavaScript
remove_action('admin_print_styles', 'print_emoji_styles');// 絵文字に関するCSS
```
2：WordPress及びプラグインを最新版にする   
3：不要なプラグインを削除する  
4：ログイン画面のURLを変更する  
5：ログイン画面にBasic認証を設定する   
6：ログイン画面のアクセスを接続元のIPで制限する  
7：ログインの失敗回数を制限する  
8：ログインに画像認証を追加する  
9：WordPressの設定ファイルへのアクセスを制限する  
10：ユーザーのパスワードを複雑にする  
11：利用者ごとにユーザーを用意する  
12：phpMyAdminを削除する  

WP Cerberプラグイン
[参考](https://baigie.me/officialblog/2020/01/28/wordpress-security/)
[参考](https://h2o-space.com/blog/wp-cerber/)
