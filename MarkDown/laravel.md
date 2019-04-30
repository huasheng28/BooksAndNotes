# laravel
##安装laragon
##添加项目代码
使用git，在www文件夹下添加项目代码`git clone url`
##安装composer
```
composer config -g repo.packagist composer https://packagist.phpcomposer.com

composer install

composer self-update

composer update
```
##生成.env文件
```cp .env.example .env```
##修改.env文件
```
APP_KEY=base64:fxBYePZejqzPj30DYjqwHQJmT7ipNHLnzdAMSpEFJJ4=

DB_DATABASE=mysql
DB_USERNAME=root
DB_PASSWORD=
```
##执行数据库迁移
```php artisan migrate```
##填充初始数据
```php artisan db:seed```
##安装npm
```
npm install -g cnpm --registry=https://registry.npm.taobao.org

cnpm install
```
##直接 Mix 编译前端内容
```
#开发环境
npm run dev
#生产环节
npm run production
```
##监控修改并自动编译
```npm run watch```
##注意
不要开其他代理VPN