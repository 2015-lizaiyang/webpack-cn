## Usage with Visual Studio

**For users of Visual Studio 2015, there's an extension available for Task Runner Explorer** [here](https://visualstudiogallery.msdn.microsoft.com/5497fd10-b1ba-474c-8991-1438ae47012a). For older versions of Visual Studio, it is a simple task to integrate Visual Studio with Webpack. All you need to do is use the external tool feature. After following the instructions below, you'll see a new command button on the ribbon within the IDE. Clicking the button will toggle Webpack's watch mode on and off. When turned on, a change to any bundle dependencies will trigger the Webpack build process. The output will appear within Visual Studio's Output window. 

## You can add an external tool to the Tools menu. 

1. Open the External Tools dialog box and click Add.
1. Title: webpack
1. Command: The path to the `webpack.cmd` file. Assuming Webpack was installed globally with npm, the path is:
   `C:\Users\{{username}}\AppData\Roaming\npm\webpack.cmd`
1. Arguments: `-w` (can also specify other [CLI options](http://webpack.github.io/docs/cli.html), such as `--display-modules`)
1. Check `Use Output window`

![Visual Studio External Tool](http://d3m4lzjblc2qwl.cloudfront.net/webpack-tool.png)

## Now add to your toolbar

1. On the menu bar, right click and select `Customize...`.
1. Click on the `Commands` tab and click on `ToolBar` radio button to select the newly created external tool.
![Visual Studio customize toolbar](http://d3m4lzjblc2qwl.cloudfront.net/customize-toolbar.png)
1. Select Standard and click on `Add Command ...` button.
1. On the left lit item, select `Tools` and than select the `External Command X` item where X is the index of your tool that appears in the `Tools` menu (starting index => 1). In my example `External Command 6`.
![Visual Studio customize toolbar](http://d3m4lzjblc2qwl.cloudfront.net/add-command.png)
1. Click `Ok` and then `Close`.