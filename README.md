# VUE 与 原生交互（iOS为主）

# iOS环境：
> wkwebview
> 加载本地js文件要用fileURLWithPath，并且资源文件要采用folder references
    
![](media/16270283562860/16270289518664.jpg)

![](media/16270283562860/16270287315073.jpg)

    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"index" ofType:@"html" inDirectory:@"h5"];
    NSURL *pathURL = [NSURL fileURLWithPath:filePath];
    [_webView loadRequest:[NSURLRequest requestWithURL:pathURL]];
        
        
#VUE环境     
> 将vue  build成文件导入到iOS项目中
   
template 里面设置组件

    <button type="primary" @click="changebgColor">改变颜色</button>
		<button type="primary" v-on:click="clickBtn">点击我触发调用原生方法</button>

script 里面定义方法
mounted（）里面将vue方法挂载到window上暴露出来供原生调用，示例

    mounted() {
			window.changeTitle = this.changeTitle
			window.changebgColor = this.changebgColor
		},


##iOS 调用 js

  iOS侧
  
    NSString *jsString = [NSString stringWithFormat:@"javascript:changebgColor()"]
    [_webView evaluateJavaScript:jsString completionHandler:^(id _Nullable data, NSError * _Nullable error) {
    }];

vue 侧

    <view class="content" :class="{change:isChange}">

    mounted() {
			window.changebgColor = this.changebgColor
		},
	data() {
			return {
				isChange:false
			}
		},
    methods: {
        changebgColor(){
			 this.$data.isChange = !this.$data.isChange
        }
    }
    

##vue 调用 原生
> 两侧需先商议好固定的交互函数名 AppJsFunction 


iOS侧 

    [self.webView.configuration.userContentController addScriptMessageHandler:self name:@"AppJsFunction"];

    -(void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message
    {
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:@"提示信息" message:message.body preferredStyle:UIAlertControllerStyleAlert];

        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {

        }];
        [alertController addAction:okAction];
        [self presentViewController:alertController animated:YES completion:nil];
    }
    
vue 侧
    
    methods: {
		clickBtn: function() {
			switch(this.$data.platform) {
				case "android":	
				    AppJsFunction.showDialog(this.$data.title);
					break
				 case "ios":
                window.webkit.messageHandlers.AppJsFunction.postMessage(this.$data.title);
					 break
				}
			}
	}



参考：https://github.com/D10NGYANG/AndroidIOSAndVueJS

本项目代码：https://github.com/theLizhao/vue2native/
