
在使用Navigation时时，你是否遇到了这样一个问题，Navigation加载启动页为入口，在启动页replace到首页，使首页替换换启动页，结果发现不生效，启动页依然存在。


## 为什么根页面启动页不能直接替换成首页


我们验证后发现当页面是Navigation的子页面时，是可以使用replace替换，当要替换根页面时，却发现不生产，这是因为我们使用NavPathStack只能操作子页面，根页面不在操作范围内，NavPathStack也不能操作根页面。所以不管是用replace，还是先push，再pop启动页，都是不生效的。


## 在Navigation中如何将启动页替换成首页


直接替换行不通，这里也有其它几种方式，第一种是Navigation根页面设置成首页，启动页跳到首页使用router的方式来处理。这种方式是可行的，但总觉得不是那么的完美，我都用Navigation了，为什么还要用router呢，官方一再强调不推荐使用router，推荐使用navigation。所以我们还有其它方式吗？


直接用Navigation替换不行，那我们自己手动来替换总行吧，在Navigation根页面加一个标签，首先加载启动页，然后更改标签的值，再去显示首页。我们以V2状态管理为例



```
@Entry
@ComponentV2
struct Index {
  @Local isShowHome: boolean = false

  build() {
    Navigation() {
      if (this.isShowHome) {
        HomePage()
      } else {
        SplashPage({
          onChangePage: () => {
            this.isShowHome = true
          }
        })
      }
    }
  }
}

@ComponentV2
export struct SplashPage {
  @Event onChangePage: VoidCallback = () => undefined

  aboutToAppear(): void {
    setTimeout(() => {
        this.onChangePage()
      }, 1000)
  }

  build() {
    Stack().width('100%').height('100%')
  }
}

@ComponentV2
export struct HomePage {

  build() {
    Stack().width('100%').height('100%')
  }
}

```

我们在启动页中使用@Event定义一个onChangePage事件，延时1秒后通知Navigation将根页面切换成首页。


这种方式虽然也可以，但总觉得还要手动通过if来切换页面，不是那么的优雅。为什么不能统一用NavPathStack来切换页面呢？不知道Navigation这么设计的原因是什么，不过大家想把启动页和首页也放在Navigation中统一管理，可以使用HMRouter这个三方库来处理，HMRouter是对Navigation做了封装，完成兼容Navigation。这几种方式都不太完美，要是需要单独特殊处理，要么需要引入三方库，大家结合自己的实际情况来选择吧。


 本博客参考[MeoMiao 萌喵加速](https://biqumo.org)。转载请注明出处！
