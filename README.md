# slack-nightmode
Night Mode theme for Slack

![Preview](https://raw.githubusercontent.com/MrBMT/slack-nightmode/master/Theme.png)

Add the following to the bottom of:

`/Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static/ssb-interop.js` (Mac)

`%LOCALAPPDATA%\slack\app-3.1.1\resources\app.asar.unpacked\src\static\ssb-interop.js` (Windows)

```
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

    // Then get its webviews
    let webviews = document.querySelectorAll(".TeamView webview");

    // Fetch our CSS in parallel ahead of time
    const cssPath = 'https://raw.githubusercontent.com/MrBMT/slack-nightmode/master/css/night_mode.css';
    let cssPromise = fetch(cssPath).then(response => response.text());

    let customCustomCSS = `
    :root {
      /* Modify these to change your theme colors: */
      --primary: #61AFEF;
      --text: #ABB2BF;
      --background: #282C34;
      --background-elevated: #3B4048;
    }
    div.c-message.c-message--light.c-message--hover {
      color: #fff !important;
      background-color: #222 !important;
    }

    span.c-message__body,
    a.c-message__sender_link,
    span.c-message_attachment__media_trigger.c-message_attachment__media_trigger--caption,
    div.p-message_pane__foreword__description span {
      color: #afafaf !important;
    }

    pre.special_formatting {
      background-color: #222 !important;
      color: #8f8f8f !important;
      border: solid;
      border-width: 1 px !important;
    }
    .c-message, .c-virtual_list__item {
      background-color: #222 !important;
    }
    `

    // Insert a style tag into the wrapper view
    cssPromise.then(css => {
      let s = document.createElement('style');
      s.type = 'text/css';
      s.innerHTML = css + customCustomCSS;
      document.head.appendChild(s);
    });

    // Wait for each webview to load
    webviews.forEach(webview => {
      webview.addEventListener('ipc-message', message => {
          if (message.channel == 'didFinishLoading')
            // Finally add the CSS into the webview
            cssPromise.then(css => {
                let script = `
                      let s = document.createElement('style');
                      s.type = 'text/css';
                      s.id = 'slack-custom-css';
                      s.innerHTML = \`${css + customCustomCSS}\`;
                      document.head.appendChild(s);
                      `
                webview.executeJavaScript(script);
            })
      });
    });
});
```

Credits: CSS initially based on ![sblack](https://github.com/frankdilo/sblack)
