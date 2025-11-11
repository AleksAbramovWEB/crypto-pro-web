<a name="cryptoproweb"></a>
# cryptoProWeb
Единое, асинхронное API для взаимодействия с КриптоПРО ЭЦП Browser Plug-In

| [![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/62.2.25/src/chrome/chrome_64x64.png)](https://www.chromium.org/getting-involved/download-chromium#TOC-Downloading-old-builds-of-Chrome-Chromium)       | [![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/62.2.25/src/firefox/firefox_64x64.png)](https://ftp.mozilla.org/pub/firefox/releases/)                                     | [![IE](https://raw.githubusercontent.com/alrra/browser-logos/62.2.25/src/archive/internet-explorer_9-11/internet-explorer_9-11_64x64.png)](https://www.microsoft.com/ru-ru/download/details.aspx?id=43374)     | [![Opera](https://raw.githubusercontent.com/alrra/browser-logos/62.2.25/src/opera/opera_64x64.png)](http://get.opera.com/ftp/pub/opera/desktop/)                      |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| __v45+__ с расширением [CryptoPro Extension for CAdES Browser Plug-in](https://chrome.google.com/webstore/detail/cryptopro-extension-for-c/iifchhfnnmpdbibifmljnfjhpififfog?utm_source=chrome-app-launcher-info-dialog) | __v43+__. Начиная с версии 52, с [расширением](https://www.cryptopro.ru/sites/default/files/products/cades/extensions/cryptopro_extension_for_cades_browser_plug_in-1.1.1-an+fx-windows.xpi) | __v9+__ с установленным [КриптоПро ЭЦП Browser plug-in](https://www.cryptopro.ru/products/cades/plugin)                                                                                                        | __v40+__ с расширением [CryptoPro Extension for CAdES Browser Plug-in](https://addons.opera.com/en/extensions/details/cryptopro-extension-for-cades-browser-plug-in/) |


- [cryptoPro](#cryptopro)
    - [Зачем мне этот пакет?](#why)
    - [Установка](#install)
    - [API](#api)
        - [Методы объекта cryptoPro](#api-cryptopro)
        - [Методы объекта сертификата](#api-certificate)
    - [Поддерживаемые СКЗИ](#supported-cist)
    - [Примеры](#examples)
        - [Тэг script (UMD)](#example-script-tag)
        - [Angular (ES Modules + Typescript)](#example-angular)
        - [React (ES Modules + JavaScript)](#example-react)
    - [Миграция с версии 1 на 2](#upgrade-from-1-to-2)
- [Тем, кто хочет помочь](#developers)
    - [Запуск режима разработки](#dev-mode)
    - [Запуск тестов](#tests-execution)
    - [Проверка работы примеров с React и Angular](#examples-testing)
    - [Проверка пакета перед публикацией в NPM](#final-check)
- [Полезная информация](#helpful)
    - [Установка КриптоПРО CSP в Linux / OSX](#csp-install-linux-osx)
    - [Установка КриптоПРО ЭЦП browser plug-in в Linux](#plugin-install-linux)
        - [Настройка плагина для Firefox (до версии 52)](#plugin-install-old-firefox)
    - [Установка сертификатов в Linux](#certificates-install-linux)
- [Лицензия](#lisense)

<a name="why"></a>
## Зачем мне этот пакет?
КриптоПРО ЭЦП Browser Plug-In доступен в разных браузерах в двух версиях.
Асинхронной (в современных браузерах) и синхронной (в браузерах постарше).
С помощью этого пакета можно не писать реализацию под каждую версию плагина дважды.
И вместо [этого](http://cpdn.cryptopro.ru/content/cades/plugin-samples-sign.html)
и [этого](http://cpdn.cryptopro.ru/content/cades/plugin-samples-sign-cades-bes-async.html)
написать это (UMD):

![example-umd.gif](resources/example-umd-js.gif)

или это (ES Modules + Typescript):

![example-esm.gif](resources/example-esm-ts.gif)

<a name="install"></a>
## Установка
Для NPM:
```bash
npm install crypto-pro-web
```

Подключение пакета как UMD модуля через тэг script:
```html
<script src="crypto-pro/dist/crypto-pro.min.js"></script>
<script>
window.cryptoPro.getUserCertificates()
  .then(function (certificates) {
    //...
  })
  .catch(function (error) {
    //...
  });
</script>
```

Подключение пакета как ES модуля с Typescript или JavaScript:
```typescript
import { getUserCertificates, Certificate } from 'crypto-pro';

(async () => {
  let certificates: Certificate[];

  try {
    certificates = await getUserCertificates();
  } catch(error) {
    // ...
  }
})();
```

Список требуемых полифиллов (если необходимы, подключаются самостоятельно):
- Promise
- Array.prototype.find

<a name="api"></a>
## API

<a name="api-cryptopro"></a>
### Методы объекта cryptoPro
- [getUserCertificates](src/api/getUserCertificates.ts) - возвращает список [сертификатов](#api-certificate), доступных пользователю в системе
- [getCertificate](src/api/getCertificate.ts) - возвращает [сертификат](#api-certificate) по отпечатку
- [createAttachedSignature](src/api/createAttachedSignature.ts) - создает совмещенную (присоединенную) подпись сообщения
- [createDetachedSignature](src/api/createDetachedSignature.ts) - создает отсоединенную (открепленную) подпись сообщения
- [createXMLSignature](src/api/createXMLSignature.ts) - создает XML подпись для документа в формате XML
- [createHash](src/api/createHash.ts) - создает хеш сообщения по ГОСТ Р 34.11-2012 256 бит
- [createSignature](src/api/createSignature.ts) - создает подпись сообщения
    > Является устаревшим и будет убран из будущих версий.
    Используйте "createAttachedSignature" и "createDetachedSignature".
- [getSystemInfo](src/api/getSystemInfo.ts) - возвращает информацию о CSP и плагине
- [isValidSystemSetup](src/api/isValidSystemSetup.ts) - возвращает флаг корректности настроек ЭП на машине
- [execute](src/api/execute.ts) - компилирует и выполняет переданную функцию для доступной браузерной среды (синхронной/асинхронной)

<a name="api-certificate"></a>
### Методы объекта сертификата
[Сертификат](src/api/certificate/certificate.ts) предоставляет следущее API:
- [isValid](src/api/certificate/isValid.ts) - возвращает флаг действительности сертификата
- [getCadesProp](src/api/certificate/getCadesProp.ts) - возвращает указанное внутренее свойство у сертификата в формате Cades
- [exportBase64](src/api/certificate/exportBase64.ts) - возвращает сертификат в формате base64
- [getAlgorithm](src/api/certificate/getAlgorithm.ts) - возвращает информацию об алгоритме сертификата
- [getOwnerInfo](src/api/certificate/getInfo.ts) - возвращает расшифрованную информацию о владельце сертификата
- [getIssuerInfo](src/api/certificate/getInfo.ts) - возвращает расшифрованную информацию об издателе сертификата
- [getExtendedKeyUsage](src/api/certificate/getExtendedKeyUsage.ts) - возвращает ОИД'ы сертификата
- [getDecodedExtendedKeyUsage](src/api/certificate/getDecodedExtendedKeyUsage.ts) - возвращает расшифрованные ОИД'ы
- [hasExtendedKeyUsage](src/api/certificate/hasExtendedKeyUsage.ts) - проверяет наличие ОИД'а (ОИД'ов) у сертификата

<a name="supported-cist"></a>
## Поддерживаемые СКЗИ
[КриптоПРО CSP](https://www.cryptopro.ru/products/csp/downloads) (v4.0+) *рекомендуется использование только сертифицированных версий*. Инструкция по установке:
- [Linux / OSX](#csp-install-linux-osx)
- (в Windows следуйте указаниям программы-установщика)

[КриптоПРО ЭЦП browser plug-in](https://www.cryptopro.ru/products/cades/plugin) (v2.0.12438+).

[Инструкция](#plugin-install-linux) по установке плагина в Linux. В Windows и OSX следуйте указаниям программы-установщика.

[Инструкция](#certificates-install-linux) по установке сертификатов в систему для Linux / OSX.

<a name="examples"></a>
## Примеры
Для их запуска необходим NodeJS версии, указанной в [.nvmrc](.nvmrc).

<a name="example-script-tag"></a>
### Тэг script (UMD)
```bash
cd examples/script-tag
npm i
npm start
```

<a name="example-angular"></a>
### Angular (ES Modules + Typescript)
```bash
cd examples/angular
npm i
```

Запуск в режиме разработки:
```bash
npm start
```

Запуск в продакшн режиме:
```bash
npm run build
npm run serve
```

<a name="example-react"></a>
### React (ES Modules + JavaScript)
```bash
cd examples/react
npm i
```

Запуск в режиме разработки:
```bash
npm start
```
