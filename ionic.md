# Ionic 相关记录

## 如何进行 App 应用更新升级

### 1 安装 `app-update` 插件和模块

```shell
  ionic cordova plugin add cordova-plugin-app-update
  npm install --save @ionic-native/app-update
```

### 2 在需要检测升级的地方加入代码

```js
import { AppUpdate } from '@ionic-native/app-update';

constructor(private appUpdate: AppUpdate) {

   const updateUrl = 'https://your-remote-api.com/update.xml';
   this.appUpdate.checkAppUpdate(updateUrl).then(() => { console.log('Update available') });

}
```

### 3 添加一个 XML 文件用来配置读取的版本信息

如文件名为：`version.xml`

内容：

```xml
<update>
    <version>302048</version>
    <name>APK Name</name>
    <url>https://your-remote-api.com/YourApp.apk</url>
</update>
```

值得说明的是，version 值对应比较的是 `platforms/android/app/src/main/AndroidManifest.xml` 文件中的 `VersionCode` 值，只有当 version 的值大于 VersionCode 时，才会进行更新。

### 4 每次发版时修改 `config.xml` 和 上一步创建的 `version.xml` 两个文件

前三步是一次性的步骤，本步骤是每次发版时都要做的重复性动作。

每当需要发版时，修改 `config.xml` 文件中的 `<widget version="1.1.1"></widget>` 和 `version.xml` 中的`<version>100101<version>`。

修改 `config.xml` 的 version 在执行打包时，会将 `1.1.1` 解析为 `AndroidManifest.xml` 文件中的 `VersionCode`，且解析的规则为 `主版本号.次版本号.修订号 => 主版本 * 10000 + 次版本号 * 100 + 修订号 * 1`，即，这里的`1.1.1`被解析为了`100101`，这意味着 `version.xml` 文件中的版本号对应的最新的 `VersionCode`，而且要大于上一次发布的版本才能成功更新。

## Ionic APP 更新升级流程示意图

![APP 更新升级流程示意图](./images/megvii.png)

## 使用 ionic hub 构建发布流程

### 在 ionic hub 上创建同名的项目

打开 `https://dashboard.ionicframework.com/personal/apps`，创建项目，如: z-test-tab

### 链接本地项目和远程项目

```shell
ionic link
```

### 上传代码

```shell
git add .
git commit -m "ci message"
git push ionic master
```

## 自动密钥签名

### 在 platforms/android 下创建 release-signing.properties 文件

### 设置文件属性

```properties
keyAlias=ckj
keyPassword=123456
storePassword=123456
storeFile=../../my-release-key.keystore
```
