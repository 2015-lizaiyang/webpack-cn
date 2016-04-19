| extension | semantic | loader examples |
|-----------|----------|-----------------|
| `.js` | returns module exports | `path-replace-loader` |
| `.ts` | returns module exports | `ts-loader` |
| `.coffee` | returns module exports | `coffee-loader`<br>`coffee-redux-loader` |
| `.jsx` | returns module exports (react component) | `jsx-loader`<br>`react-hot-loader!jsx-loader` |
| `.json`<br>`.json5` | returns json value | `json-loader`<br>`json5-loader` |
| `.txt` | return string value | `raw-loader` |
| `.css` | returns nothing, side effect of adding style to DOM | `style-loader!css-loader`<br>`style-loader!css-loader!autoprefixer-loader` |
| `.less` | returns nothing, side effect of adding style to DOM | `style-loader!css-loader!less-loader` |
| `.scss` | returns nothing, side effect of adding style to DOM | `style-loader!css-loader!scss-loader` |
| `.styl` | returns nothing, side effect of adding style to DOM | `style-loader!css-loader!stylus-loader` |
| `.png`<br>`.jpg`<br>`.jpeg`<br>`.gif`<br>`.svg` | returns url to image | `file-loader`<br>`url-loader` |
| `.woff`<br>`.ttf` | returns url to font | `file-loader`<br>`url-loader` |
| `.wav`<br>`.mp3` | returns url to audio | `file-loader`<br>`url-loader` |
| `.mpeg`<br>`.mp4`<br>`.webm`<br>`.ogv` | returns url to video | `file-loader` |
| `.html` | returns HTML as string | `html-loader` |
| `.md`<br>`.markdown` | returns HTML as string | `html-loader!markdown-loader` |
| `.jade` | returns template function | `jade-loader` |
| `.hbs`<br>`.handlebars` | returns template function | `handlebars-loader` |