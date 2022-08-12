---
title: Puppeteer实践
date: 2022-08-12 12:39:40
tags:
 - Puppeteer
 - NodeJS
---

# Puppeteer

#截图

```jsx
(async () => {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    //设置可视区域大小
    await page.setViewport({width: 1920, height: 800});
    await page.goto('https://youdata.163.com');
    //对整个页面截图
    await page.screenshot({
        path: './files/capture.png',  //图片保存路径
        type: 'png',
        fullPage: true //边滚动边截图
        // clip: {x: 0, y: 0, width: 1920, height: 800}
    });
    //对页面某个元素截图
    let [element] = await page.$x('/html/body/section[4]/div/div[2]');
    await element.screenshot({
        path: './files/element.png'
    });
    await page.close();
    await browser.close();
})();
```

#模拟用户登录

```jsx
(async () => {
    const browser = await puppeteer.launch({
        slowMo: 100,    //放慢速度
        headless: false,
        defaultViewport: {width: 1440, height: 780},
        ignoreHTTPSErrors: false, //忽略 https 报错
        args: ['--start-fullscreen'] //全屏打开页面
    });
    const page = await browser.newPage();
    await page.goto('https://demo.youdata.com');
    //输入账号密码
    const uniqueIdElement = await page.$('#uniqueId');
    await uniqueIdElement.type('admin@admin.com', {delay: 20});
    const passwordElement = await page.$('#password', {delay: 20});
    await passwordElement.type('123456');
    //点击确定按钮进行登录
    let okButtonElement = await page.$('#btn-ok');
    //等待页面跳转完成，一般点击某个按钮需要跳转时，都需要等待 page.waitForNavigation() 执行完毕才表示跳转成功
    await Promise.all([
        okButtonElement.click(),
        page.waitForNavigation()  
    ]);
    console.log('admin 登录成功');
    await page.close();
    await browser.close();
})();
```

那么 ElementHandle 都提供了哪些操作元素的函数呢？

elementHandle.click()：点击某个元素elementHandle.tap()：模拟手指触摸点击elementHandle.focus()：聚焦到某个元素elementHandle.hover()：鼠标 hover 到某个元素上elementHandle.type('hello')：在输入框输入文本

#请求拦截

```jsx
(async () => {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    const blockTypes = new Set(['image', 'media', 'font']);
    await page.setRequestInterception(true); //开启请求拦截
    page.on('request', request => {
        const type = request.resourceType();
        const shouldBlock = blockTypes.has(type);
        if(shouldBlock){
            //直接阻止请求
            return request.abort();
        }else{
            //对请求重写
            return request.continue({
                //可以对 url，method，postData，headers 进行覆盖
                headers: Object.assign({}, request.headers(), {
                    'puppeteer-test': 'true'
                })
            });
        }
    });
    await page.goto('https://demo.youdata.com');
    await page.close();
    await browser.close();
})();
//那 page 页面上都提供了哪些事件呢？

page.on('close') //页面关闭
page.on('console') //console API 被调用
page.on('error') //页面出错
page.on('load') //页面加载完
page.on('request') //收到请求
page.on('requestfailed') //请求失败
page.on('requestfinished') //请求成功
page.on('response') //收到响应
page.on('workercreated') //创建 webWorker
page.on('workerdestroyed') //销毁 webWorker
```

#获取 WebSocket 响应

Puppeteer 目前没有提供原生的用于处理 WebSocket 的 API 接口，但是我们可以通过更底层的 Chrome DevTool Protocol (CDP) 协议获得

```jsx
(async () => {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    //创建 CDP 会话
    let cdpSession = await page.target().createCDPSession();
    //开启网络调试,监听 Chrome DevTools Protocol 中 Network 相关事件
    await cdpSession.send('Network.enable');
    //监听 webSocketFrameReceived 事件，获取对应的数据
    cdpSession.on('Network.webSocketFrameReceived', frame => {
        let payloadData = frame.response.payloadData;
        if(payloadData.includes('push:query')){
            //解析payloadData，拿到服务端推送的数据
            let res = JSON.parse(payloadData.match(/\{.*\}/)[0]);
            if(res.code !== 200){
                console.log(`调用websocket接口出错:code=${res.code},message=${res.message}`);
            }else{
                console.log('获取到websocket接口数据：', res.result);
            }
        }
    });
    await page.goto('https://netease.youdata.163.com/dash/142161/reportExport?pid=700209493');
    await page.waitForFunction('window.renderdone', {polling: 20});
    await page.close();
    await browser.close();
})();
```

#植入 javascript 代码

Puppeteer 最强大的功能是，你可以在浏览器里执行任何你想要运行的 javascript 代码，下面是我在爬 188 邮箱的收件箱用户列表时，发现每次打开收件箱再关掉都会多处一个 iframe 来，随着打开收件箱的增多，iframe 增多到浏览器卡到无法运行，所以我在爬虫代码里加了删除无用 iframe 的脚本：

```jsx
(async () => {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    await page.goto('https://webmail.vip.188.com');
    //注册一个 Node.js 函数，在浏览器里运行
    await page.exposeFunction('md5', text =>
        crypto.createHash('md5').update(text).digest('hex')
    );
    //通过 page.evaluate 在浏览器里执行删除无用的 iframe 代码
    await page.evaluate(async () =>  {
        let iframes = document.getElementsByTagName('iframe');
        for(let i = 3; i <  iframes.length - 1; i++){
            let iframe = iframes[i];
            if(iframe.name.includes("frameBody")){
                iframe.src = 'about:blank';
                try{
                    iframe.contentWindow.document.write('');
                    iframe.contentWindow.document.clear();
                }catch(e){}
                //把iframe从页面移除
                iframe.parentNode.removeChild(iframe);
            }
        }
        //在页面中调用 Node.js 环境中的函数
        const myHash = await window.md5('PUPPETEER');
        console.log(`md5 of ${myString} is ${myHash}`);
    });
    await page.close();
    await browser.close();
})();
```

page.evaluate(pageFunction[, ...args])：在浏览器环境中执行函数

page.evaluateHandle(pageFunction[, ...args])：在浏览器环境中执行函数，返回 JsHandle 对象

page.$$eval(selector, pageFunction[, ...args])：把 selector 对应的所有元素传入到函数并在浏览器环境执行

page.$eval(selector, pageFunction[, ...args])：把 selector 对应的第一个元素传入到函数在浏览器环境执行

page.evaluateOnNewDocument(pageFunction[, ...args])：创建一个新的 Document 时在浏览器环境中执行，会在页面所有脚本执行之前执行

page.exposeFunction(name, puppeteerFunction)：在 window 对象上注册一个函数，这个函数在 Node 环境中执行，有机会在浏览器环境中调用 Node.js 相关函数库

#抓取 iframe 中的元素

一个 Frame 包含了一个执行上下文（Execution Context），我们不能跨 Frame 执行函数，一个页面中可以有多个 Frame，主要是通过 iframe 标签嵌入的生成的。其中在页面上的大部分函数其实是 page.mainFrame().xx 的一个简写，Frame 是树状结构，我们可以通过 frame.childFrames() 遍历到所有的 Frame，如果想在其它 Frame 中执行函数必须获取到对应的 Frame 才能进行相应的处理

以下是在登录 188 邮箱时，其登录窗口其实是嵌入的一个 iframe，以下代码时我们在获取 iframe 并进行登录

```jsx
(async () => {
    const browser = await puppeteer.launch({headless: false, slowMo: 50});
    const page = await browser.newPage();
    await page.goto('https://www.188.com');
    //点击使用密码登录
    let passwordLogin = await page.waitForXPath('//*[@id="qcode"]/div/div[2]/a');
    await passwordLogin.click();
    for (const frame of page.mainFrame().childFrames()){
        //根据 url 找到登录页面对应的 iframe
        if (frame.url().includes('passport.188.com')){
            await frame.type('.dlemail', 'admin@admin.com');
            await frame.type('.dlpwd', '123456');
            await Promise.all([
                frame.click('#dologin'),
                page.waitForNavigation()
            ]);
            break;
        }
    }
    await page.close();
    await browser.close();
})();
```

#文件的上传和下载

```jsx
(async () => {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    //通过 CDP 会话设置下载路径
    const cdp = await page.target().createCDPSession();
    await cdp.send('Page.setDownloadBehavior', {
        behavior: 'allow', //允许所有下载请求
        downloadPath: 'path/to/download'  //设置下载路径
    });
    //点击按钮触发下载
    await (await page.waitForSelector('#someButton')).click();
    //等待文件出现，轮训判断文件是否出现
    await waitForFile('path/to/download/filename');

    //上传时对应的 inputElement 必须是<input>元素
    let inputElement = await page.waitForXPath('//input[@type="file"]');
    await inputElement.uploadFile('/path/to/file');
    browser.close();
})();
```

#模拟选择文件

```jsx
const [fileChooser] = await Promise.all([
        page.waitForFileChooser(),
        page.click('#mydropzone'), // some button that triggers file selection
    ]);
    await fileChooser.accept(['D:\\down\\tmp.zip']);
```

#跳转新 tab 页处理

在点击一个按钮跳转到新的 Tab 页时会新开一个页面，这个时候我们如何获取改页面对应的 Page 实例呢？可以通过监听 Browser 上的 targetcreated 事件来实现，表示有新的页面创建：

```jsx
let page = await browser.newPage();
await page.goto(url);
let btn = await page.waitForSelector('#btn');
//在点击按钮之前，事先定义一个 Promise，用于返回新 tab 的 Page 对象
const newPagePromise = new Promise(res => 
  browser.once('targetcreated', 
    target => res(target.page())
  )
);
await btn.click();
//点击按钮后，等待新tab对象
let newPage = await newPagePromise;
```

#模拟不同的设备

Puppeteer 提供了模拟不同设备的功能，其中 puppeteer.devices 对象上定义很多设备的配置信息，这些配置信息主要包含 viewport 和 userAgent，然后通过函数 page.emulate 实现不同设备的模拟

```jsx
const puppeteer = require('puppeteer');
const iPhone = puppeteer.devices['iPhone 6'];
puppeteer.launch().then(async browser => {
  const page = await browser.newPage();
  await page.emulate(iPhone);
  await page.goto('https://www.google.com');
  await browser.close();
});
```
