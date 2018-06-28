# Outline

    CocoaPods介绍

    CocoaPods安装

    CocoaPods使用

    CocoaPods制作



## CocoaPods是什么

    每种语言发展到一个阶段，就会出现相应的依赖管理工具，例如

    – Java 语言的 Maven，

    – nodejs 的 npm。

    – iOS 依赖管理的工具CocoaPods

    项目的依赖项在一个称为Podfile的文本文件中指定。CocoaPods将解析库之间的依赖关系，获取生成的源代码，然后在Xcode工作区中将其链接在一起构建项目。

    \[官网链接\]\(https://guides.cocoapods.org\)  ：https://guides.cocoapods.org/



## CocoaPods安装

1. CocoaPods是基于ruby 的，需要ruby环境以及使用ruby的gem命令。

2. Mac系统默认会安装好ruby环境。可在终端ruby -v查看ruby版本

3. 安装命令 sudo gem install cocoapods

4. 安装过程中可能的问题：

*     ruby版本过低，需要升级ruby
*     ruby源默认是https://rubygems.org/（国外亚马逊云）
*     查看ruby源：gem sources –l

*     移除掉原有的 gem sources --remove https://rubygems.org/

       添加国内的源：

       gem source -a https://gems.ruby-china.org\(淘宝的源已经不更新维护了，现在使用ruby-china的源哦\)

       gem sources -a  https://ruby.taobao.org/\(以下命令添加淘宝的源：（不建议继续使用）\)

       验证是否替换成功：gem sources –l

       sudo gem update –system 更新gem



### ruby 版本的管理 —  \[rvm\]\( https://github.com/rvm/rvm\)



rvm 可以在电脑上安装多个版本的 ruby ,并可以自由切换 

好处:可以方便地处理 ruby 版本过低的问题 

-  安装:

```
$curl -L get.rvm.io | bash -s stable 
$ source ~/.rvm/scripts/rvm
```

-  rvm 教程

* 查看 rvm 版本:  ` rvm -v `
* 列出 ruby 可安装的版本信息:  ` rvm list known `
* 安装一个 ruby 版本:  ` rvm install 2.1.4`
* 查看已安装的 ruby :  ` rvm list `
* 卸载一个已安装 ruby 版本: ` rvm remove 2.1.4 ` 
* 查看当前ruby版本:  ` rvm current `
* 切换版本:  ` rvm use 2.1.4 —default `



## CocoaPods使用

### CocoaPods常用命令

1. pod –version：查看pods版本

2. pod setup：将所有第三方的Podspec索引文件更新到本地的~/.cocoapods/repos目录下

* 所有的第三方开源库的Podspec文件都托管在https://github.com/CocoaPods/Specs
*  pod setup 实际上做的事是将https://github.com/CocoaPods/SpecsCocoapods克隆~/.cocoapods/repos目录下。即使在安装时不执行此命令，在初次执行pod install命令时，系统也会自动执行pod setup.

很遗憾的是这个过程速度很慢,甚至被墙掉也很正常.



3. pod repo update:更新本地仓库

4. pod search xxx:查找某一个开源库。查找开源库之前，默认会执行pod repo update指令

5. pod list:列出所有可用的第三方库

6. pod install:

*     会根据Podfile.lock文件中列举的版本号来安装第三方框架
*     如果一开始Podfile.lock文件不存在, 就会按照Podfile文件列举的版本号来安装第三方框架
*     安装开源库之前, 默认会执行pod repo update指令
*     所解决的只是 有和无得问题,如果有就什么也不做,而不会检查更新,

7. pod update:

*     将所有第三方框架更新到最新版本, 并且创建一个新的Podfile.lock文件
*     安装开源库之前, 默认会执行pod repo update指令
*     pod update 只是更新依赖库的版本,并且更新podfile.lock中记录的版本

8. pod install与pod update的总结就是:首次添加用pod install,更新用pod update,单独更新某一个依赖pod update + 名字,每个依赖库可能对cocoapods的版本要求不一致,所以建议多用单独更新

9. pod install –no-repo-update:安装开源库之前, 不会执行pod repo update指令

10. pod update –no-repo-update:安装开源库之前, 不会执行pod repo update指令



> \*\* 注意：\*\*  
>
> ` pod install ` 版本号的依据是: podfile -&gt; podfile.lock -&gt; 最新版本  
>
> ` pod update `   版本号的依据是: podfile -&gt; 最新版本
>
> 如果 podfile 内每个依赖都指定了版本，` pod install `和 ` pod update` 效果是一致的。
>
> 实际开发中，不推荐使用 pod update   是为了防止忘记某依赖未指定版本而执行了版本更新的误操作。
>
> ` podfile.lock ` 保存的是当前项目依赖的具体版本
>
> CocoaPods 强烈建议将 Podfile.lock 文件加入版本管理，这样其他人同步了你的 podfile.lock 文件之后，执行 pod install 时会将按照里面指定给的版本加载，避免多人协作时发生冲突。





### Podfile

#### 简单用法

```
target 'MyApp' do
  use_frameworks!
  pod 'Alamofire'
end
```

1. pod 'Alamofire' //表示永远使用最新版本

2. pod 'Alamofire', '3.0' //使用固定版本,3.0

3. pod 'Alamofire', '~&gt; 3.0' //大于3.0小于4.0\(不含4.0\)的版本

4. 其它:

*    &gt;version 要求版本大于version,当有新版本时，会更新至最新版本
*   &gt;= version 要求版本大于或者等于version，当有新版本时，会更新至最新版本
*    &lt; version 要求版本小于version，当超过version版本后，不会再更新
*    &lt;= version 要求版本小于或者等于version，当超过version版本后，不会再更新
*    ~&gt; version 比如version=0.1.2时，范围在\[0.1.2, 0.2.0\)。注意0.2.0是开区间，也就是不包括0.2.0。如果version = 0.1,那么范围就是\[0.1 1.0\).如果version = 3.0,那么范围就是\[3.0 4.0\)

#### 带参数用法

* 使用本地路径：`pod 'AFNetworking', :path => '~/Documents/AFNetworking'`
* 使用主分支的版本,也就是默认写法 `pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git'`
* 不使用master，而是使用指定的分支: `pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :branch => 'dev'`
* 使用指定的提交版本：`pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :commit => '082f8319af'`
* 使用指定的tag（标签，发布库的版本时，通常版本号与tag号是一致的）`pod 'AFNetworking', :git =>'https://github.com/gowalla/AFNetworking.git', :tag => '0.7.0'`

#### spec文件

一般spec文件与Podfile文件在同一个目录,并且spec文件的名字与依赖库的名字一致.如果不指定spec文件,则表示默认用根目录的spec文件.可以这样指定spec文件

* spec文件在网络上`pod 'JSONKit', :podspec => 'https://example.com/JSONKit.podspec'`
* spec文件在本地`podspec :path => '/Documents/PrettyKit/PrettyKit.podspec'`
* 如果根目录podspec的名字与库名不一样，可以通过这样来指定`podspec :name => 'QuickDialog'`



#### 其它用法



指定spec仓库地址,默认是官方的地址https://github.com/CocoaPods/Specs.git,如果是私有仓库的需要加上私有仓库地址:

```
//默认地址
source 'https://github.com/CocoaPods/Specs.git'

//私有地址
source 'https://github.com/Artsy/Specs.git'

target 'MyApp' do
    use_frameworks!
    pod 'Alamofire'
end

//cocoapods 官方source是隐式的需要的，也就是可以省略.一旦你指定了其他source 你就需要也把官方的地址也加上.
```

如果一个工程下面有多个项目,每个项目的依赖库不同,那么可以这样分别指定:

```
//禁止警告
 inhibit_all_warnings!

target 'MyApp' do
  pod 'GoogleAnalytics', '~> 3.1'

  target 'MyAppTests' do
    inherit! :search_paths
    pod 'OCMock', '~> 2.0.1'
  end
end

post_install do |installer|
  installer.pods_project.targets.each do |target|
    puts target.name
  end
  //项目MyApp不能访问OCMock,能访问GoogleAnalytics,MyAppTests则相
```



* inhibit\_all\_warnings!   表示不显示所引用的库中的警告信息。我们可以指定全局不显示警告信息，也可以指定某一个库不显示警告信息：

```
pod 'SSZipArchive', :inhibit_warnings => true
```



* 默认情况下,Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个 .a 文件即可。 use\_frameworks!会编译成一个Pods.framework的文件,而不是.a文件,一般用于依赖库是swift的情况.在target-&gt;BuildPhases-&gt;Link Binary with Libraries可以查看libPod这个文件来查看究竟是 .a还是.framework的方式集成到项目中。



## CocoaPods制作



### 创建Spec - LZLocation

其实让自己的工程支持cocoapods很简单只需要几步

1. 代码提交到github平台

*     将自己的代码上传到github; \(https://github.com/zhegu/LZLocation\)

2. 创建.podspec

*     项目的目录下执行命令

```
    // 注 LZLocation 这个是你框架的名称 
    $ pod spec create LZLocation
```

*     也可以使用vim/vi创建,只要创建就可以了



3. 编辑.podspec

```
Pod::Spec.new do |s|
    
      s.name         = "LZLocation"
      s.version      = "1.3"
      s.summary      = " LZLocation help you deal with your locaiton."
      s.description  = <<-DESC
    MNShowcaseView can highlight particular view of your app and describe about it as a guided tutorial.  It can be used as guided tutorial in your app where you can tell new user's how they can use   different items in your app.
                       DESC
    
      s.homepage     = "https://github.com/zhegu/LZLocation"
      s.license      = { :type => "MIT", :file => "LICENSE" }
      s.author             = { "Li Zhe" => "lizhe@ctsi.com.cn" }
    
      s.platform     = :ios, "8.0"
      s.source       = { :git => "https://github.com/zhegu/LZLocation.git", :tag => "#{s.version}" }
      s.source_files  = "LZLocation/"
    
    end    
```

*     s.name：名称，pod search 搜索的关键词,注意这里一定要和.podspec的名称一样,否则报错
*     s.version：版本号
*     s.ios.deployment\_target:支持的pod最低版本
*     s.summary: 简介
*     s.homepage:项目主页地址
*     s.license:许可证
*     s.author:作者
*     s.social\_media\_url:社交网址,这里我写的微博默认是Twitter,如果你写Twitter的话,你的podspec发布成功后会@你
*     s.source:项目的地址
*     s.source\_files:需要包含的源文件
*     s.resources: 资源文件
*     s.requires\_arc: 是否支持ARC
*     s.dependency：依赖库，不能依赖未发布的库
*     s.dependency：依赖库，如有多个可以这样写
*     source\_files:写法及含义建议大家写第一种或者第二种

```
    "YJSettingTableView/*
    ""YJSettingTableView/YJSettingTableView/*.{h,m}"
    "YJSettingTableView/**/*.h"
    “*” 表示匹配所有文件
    “*.{h,m}” 表示匹配所有以.h和.m结尾的文件
    “**” 表示匹配所有子目录
```



  s.source 常见写法

1. ```
   s.source = { :git => "https://github.com/coderYJ/YJSettingTableView.git", :commit => "68defea" }
   s.source = { :git => "https://github.com/coderYJ/YJSettingTableView.git", :tag => 1.0.0 }
   s.source = { :git => "https://github.com/coderYJ/YJSettingTableView.git", :tag => s.version }
   commit => "68defea" 表示将这个Pod版本与Git仓库中某个commit绑定
   tag => 1.0.0 表示将这个Pod版本与Git仓库中某个版本的comit绑定
   tag => s.version 表示将这个Pod版本与Git仓库中相同版本的comit绑定
   ```

 

4. 将自己的项目打成tag

*     https://github.com/zhegu/LZLocation/releases/tag/1.4 
*     注意 项目中必须要有LICENSE许可证/授权

5. 验证

*     pod spec lint LZLocation.podspec

    ![](http://ctsi-mts.oss-cn-beijing.aliyuncs.com/client/lizhe/cocoapods_1.png)    

*     LICENSE不能写错
*     iPhone4S 无法验证之类的错误 —— 升级cocoapods



——————————————————————————————————————



以上已经完成了一个私有项目的cocoapods支持

在podFile 里面添加以下pod就可以支持了



```
pod 'LZLocation', :git => 'https://github.com/zhegu/LZLocation.git' 
也可以加上  :tag => '1.4'等
```

6. 注册CocoaPods

*     注册Trunk：trunk需要CocoaPods 0.33版本以上，用pod --version 
*     查看是否注册 pod trunk me

![](http://ctsi-mts.oss-cn-beijing.aliyuncs.com/client/lizhe/cocoapods_2.png)

*       注册

```
// 加上--verbose可以输出详细debug信息，方便出错时查看。
    pod trunk register lizhe@ctsi.com.cn "zhegu707" --verbose
```

*     注册完成之后会给你的邮箱发个邮件,进入邮箱邮件里面有个链接,需要点击确认一下

    

7. 发布

*     提交spec文件到 CocoaPods 中心仓库

```
   pod trunk push LZLocation.podspec 
```

    ![](http://ctsi-mts.oss-cn-beijing.aliyuncs.com/client/lizhe/cocoapods_3.png)

*     怎样删除自己的开源库在cocopods上的某个版本

```
    pod trunk delete 名字 版本
```

8. 多人共同维护的开源项目

    1. 如果需要多个人维护一个库,每个人都应该有权限push提交spec文件;第一个push的人可以被认为是管理员,可以再添加子管理员,这样子管理员就有权限push了

```
pod trunk add-owner 邮箱地址    
```

    2. 移除某个管理员

```
  pod trunk remove-owner 邮箱地址
```

    3. pod lib lint 和 pod spec lint 有什么区别

```
pod lib lint 不需要联网

pod spec lint 会联网检查sepc repo,并且关联tag
```



### podfile 书写规范

```
source 'https://github.com/CocoaPods/Specs.git' # 组件依赖文件所存放仓库，根据需求可引入多个
source 'https://github.com/artsy/Specs.git'


platform :ios, '8.0'            # 
inhibit_all_warnings!           # 忽视引用的代码中的警告

workspace 'CocoaPodsDemo'       # 指定生成的 workspace 名字

def common_pods                 # 如果有多个 target，可以将公共部分进行 def 定义再引入
    pod 'xxx'
end

target 'CocoaPodsDemo' do
    project 'DemoProject'       # 可用于指定实际的工程

    use_frameworks!             # 是否以 framework 形式引入。swift 必须有这个关键字 

    common_pods              # 公共引入的组件

    pod 'SSipArchive', :inhibit_warnings => true   # 屏蔽某个 pod 的 warning

    pod 'AFNetworking', '3.2'   # 使用 3.2 版本
    pod 'YYCache', '~> 0.3'     # pod update 时最高升级到 < 1.0，不包括 1.0
    
   # Build 环境配置
   pod 'PonyDebugger', :configurations => ['Debug', 'Beta']
   pod 'PonyDebugger', :configuration => 'Debug'

   # 使用具体的某个 subspec
   pod 'QueryKit/Attribute'
   pod 'QueryKit', :subspecs => ['Attribute', 'QuerySet']
   
   # 引用本地组件
   pod 'AFNetworking', :path => '~/Documents/AFNetworking'
   
   # 使用具体仓库
   pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git'
   # 使用具体仓库具体分支
   pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :branch => 'dev'
   # 使用具体仓库的某个 tag
   pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :tag => '0.7.0'
   # 使用具体仓库的某个 commit
   pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :commit => '082f8319af'
    
    # 使用指定路径的 spec 文件
   pod 'JSONKit', :podspec => 'https://example.com/JSONKit.podspec'
   
   
   target 'ShowsApp' do
     pod 'ShowsKit'

      # Has its own copy of ShowsKit + ShowTVAuth
       target 'ShowsTV' do
       pod 'ShowTVAuth'
       end

      # Has its own copy of Specta + Expecta
      # and has access to ShowsKit via the app
     # that the test target is bundled into
      target 'ShowsTests' do
       # inherit！ 有三种类型：':complete' 继承父级所有行为；':none' 什么行为都不继承；':search_paths' 继承父级的 search paths
        inherit! :search_paths
        pod 'Specta'
        pod 'Expecta'
      end
   end
end

# hook 配置, 在 preparing 阶段后，install 之前
pre_install do |installer|
    
end

# hook 配置，在 pod install 之后，可用于修改工程配置等
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['GCC_ENABLE_OBJC_GC'] = 'supported'
    end
  end
end
```



### Podspec 书写规范

```
Pod::Spec.new do |spec|

# 组件基本信息配置
#
   # 组件名
  spec.name         = 'Reachability'
  # 组件版本号，命名规则遵循 [semantic versioning](https://semver.org/)
  spec.version      = '3.1.0'    
  # 许可证
  spec.license      = { :type => 'BSD' }
  # 仓库主页
  spec.homepage     = 'https://github.com/tonymillion/Reachability'
  # 一个作者用 spec.author = 'Darth Vader'
  spec.authors      = { 'Tony Million' => 'tonymillion@gmail.com' }
  # 组件概述
  spec.summary      = 'ARC and GCD Compatible Reachability Class for iOS and OS X.'
  # 组件源码地址
  spec.source       = { :git => 'https://github.com/tonymillion/Reachability.git', :tag => 'v3.1.0' }

# 组件平台支持
#
  # 支持单平台使用
  spec.platform = :osx, '10.8'
  spec.platform = :ios

  # 支持多平台使用
  spec.ios.deployment_target = '6.0'
  spec.osx.deployment_target = '10.8'

# Build settings
#
  spec.dependency 'AFNetworking', '~> 1.0'    # 组件依赖的第三方库
  spec.requires_arc = false                   # 是否要求 ARC 环境
  spec.requires_arc = ['Classes/*ARC.m', 'Classes/ARC.mm']
  spec.frameworks = 'QuartzCore', 'CoreData'  # 组件引用的 framework spec.weak_frameworks = 'Twitter', 'SafariServices' # 组件弱引用的 framework
  spec.libraries = 'xml2', 'z'                # 组件引用的 library
  ... 更多请看官方文档
  
# File patterns
#
  spec.source_files = 'Classes/**/*.{h,m}'          # 接入方使用组件时，引入的源文件，正则匹配
  spec.public_header_files = 'Headers/Public/*.h'   # 引入的共有头文件
  spec.private_header_files = 'Headers/Private/*.h' # 引入的私有头文件
  spec.vendored_frameworks = 'MyFramework.framework'# 引入的 framework
  spec.vendored_libraries = 'libProj4.a'            # 引入的 library
  # 以 bundle 形式引入的资源
  spec.resource_bundles = {
    'MapBox' => ['MapView/Map/Resources/*.png'],
    'OtherResources' => ['MapView/Map/OtherResources/*.png']
  }
  # 直接引入资源
  spec.resources = ['Images/*.png', 'Sounds/*']
  ... 更多请看官方文档

# Subspecs
#
  # 将组件分为多个子组件，接入方可以根据需求只接入几个子组件，减少包体积
  subspec 'Twitter' do |sp|
      sp.source_files = 'Classes/Twitter'
  end
  # 测试组件
  spec.test_spec do |test_spec|
    test_spec.source_files = 'NSAttributedString+CCLFormatTests.m'
    test_spec.dependency 'Expecta'
  end
  # 默认子组件。也就是当接入方不作区分时，直接使用组件名引入时，所引入子组件
  spec.default_subspec = 'Core'

# 多平台支持
```



