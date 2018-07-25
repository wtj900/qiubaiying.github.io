---
layout:     post
title:      ReactiveCocoa
subtitle:   RAC从入门到精通
date:       2018-07-25
author:     JT
header-img: img/post-bg-rac.png
catalog: true
tags:
    - RAC
---






appledoc是最年轻的一个，并且只为Objective-C服务（很专一），能生成和Apple一个风格的文档，功能齐全，使用方便，还可以直接编译成docset安装进xcode。。看样子除了语言支持太少，其他的表现都不错，关键是最贴合Xcode。 虽然没有统计数据，但我相信ObjC这个用的人应该是最多的。 

[Github项目链接](https://github.com/tomaz/appledoc)


# 安装

## 正常安装

```
git clone git://github.com/tomaz/appledoc.git
cd ./appledoc
sudo sh install-appledoc.sh
```

## Homebrew

`brew install appledoc`

## 自己编译

去Github把工程clone下来，用Xcode打开，然后随便搞。

## 更新

`git pull`

之后重新编译一遍appledoc.xcodeproj

> 安装完成后，验证一下OK了没: `appledoc --version`

# 使用

进入code所在目录，跑一下下面的命令，默认会编译出docset并安装进Xcode。

`appledoc --project-name MyProject --project-company ibireme ./`

如果想要详细的参数，可以查看帮助

`appledoc --help`

如果想要集成进Xcode工程:

1. 选中你的工程,点击Add Target按钮,选择 Other -> Aggregate模板新建.
2. 点击Add Build Phase按钮,添加一个Run Script.
3. 把下面的模板代码复制进去,把前几行参数改成你自己的.
4. 在Xcode左上角选择这个新建的Target,然后点击build.
5. 文档就会编译好并且自动安装进Xcode了(重启Xcode生效).

```
#appledoc Xcode script  
# Start constants  
company="ACME";  
companyID="com.ACME";
companyURL="http://ACME.com";
target="iphoneos";
#target="macosx";
outputPath="~/help";
# End constants
 
/usr/local/bin/appledoc \
--project-name "${PROJECT_NAME}" \
--project-company "${company}" \
--company-id "${companyID}" \
--docset-atom-filename "${company}.atom" \
--docset-feed-url "${companyURL}/${company}/%DOCSETATOMFILENAME" \
--docset-package-url "${companyURL}/${company}/%DOCSETPACKAGEFILENAME" \
--docset-fallback-url "${companyURL}/${company}" \
--output "${outputPath}" \
--publish-docset \
--docset-platform-family "${target}" \
--logformat xcode \
--keep-intermediate-files \
--no-repeat-first-par \
--no-warn-invalid-crossref \
--exit-threshold 2 \
"${PROJECT_DIR}"
```

# 语法

appledoc官方原来是有一篇语法的，但是现在貌似维护中了。。所以这里尽量多介绍一下。

首先，文档中的注释只有符合规范，才能被appledoc认可。

凡是以 “///”、”/**”或”/*!”开头的注释块，都算所appledoc注释。下面是示例:

```
/// 这是单行注释。
 
/** 这也是单行注释 */
 
/*! 同样是单行注释 */
 
/** 这也是单行注释，
 *  第二行会接上第一行。
 */
```

注释块中，每一行开头的空格和”*”字符多数情况都会被appledoc忽略。
连续的两行(即没有间隔空行)的注释，将被合并成一个段落，并忽略换行，就像html。

在注释块内，appledoc支持如下语法：Markdown、HTML、HeaderDoc Tags。

Markdown的语法在这里有介绍(中文翻译)，用Github的童鞋应该很熟悉。OSX上可以用Mou实时查看效果，Chrome也有一个插件来实时查看效果。这个东西可以说一看就会，学习成本很低。Markdown有很多方言，而且appledoc支持的也不算完整。所以用的时候可以试着在appledoc编译一下看看效果。

HTML这个就不用说了，支持Markdown肯定也支持HTML。。如果想要把控住更多细节，那就直接码Html吧。

HeaderDoc Tags这个东西是苹果的HeaderDoc工具的语法。详情可以见官网文档。例如@param、@return、@warning这样的东西，appledoc会进行解释。当然appledoc对这个东西的支持也不算完整 :?: 所以用的时候也要尝试一下。

下面是一些常用的语法示意:

```
/** 第一行是类的简介
 
 在简介的下面,就是类的详细介绍了。
 没有间隔换行会被消除，就像Html那样。
 
 下面是常用的markdown语法
 
 - - -
 
 无序列表: (每行以 '*'、'-'、'+' 开头):
 
 * this is the first line
 * this is the second line
 * this is the third line
 
 有序列表: (每行以 1.2.3、a.b.c 开头):
 
 a. this is the first line
 b. this is the secode line
 
 多级列表:
 
 * this is the first line
    a. this is line a
    b. this is line b
 * this is the second line
    1. this in line 1
    2. this is line 2
 
 标题:
 
 # This is an H1
 ## This is an H2
 ### This is an H3
 #### This is an h4
 ##### This is an h5
 ###### This is an H6
 
 链接:
 
 普通URL直接写上，appledoc会自动翻译成链接: https://blog.ibireme.com
 
 [这个](http://example.net/) 链接会隐藏实际URL.
 
 表格:
 
 | header1 | header2 | header3 |
 |---------|:-------:|--------:|
 | normal  |  center |  right  |
 | cell    | cell    | cell    |
 
 引用:
 
 这里会引用到方法 `someMethod:`，这里会引用到类 `YYColor`
 
 这里会引用到一个代码块
 
     void CMYK2RGB(float c, float m, float y, float k, 
                    float *r, float *g, float *b) {
         *r = (1 - c) * (1 - k);
         *g = (1 - m) * (1 - k);
         *b = (1 - y) * (1 - k);
     }
 
 @since iOS5.0
 
 */
@interface AppledocExample : NSObject
 
///这里是属性的说明
@property (nonatomic, strong) NSString *name;
 
/** 
 @brief 这里是方法的简介。该Tag不能放到类注释里。
 @exception UIColorException 这里是方法抛出异常的说明
 @see YYColor
 @see someMethod:
 @warning 这里是警告，会显示成蓝色的框框
 @bug 这里是bug，会显示成黄色的框框
 @param red   这里是参数说明1
 @param green 这里是参数说明2
 @param blue   这里是参数说明3
 @return  这里是返回值说明
 */
- (UIColor *)initWithRed:(int)red green:(int)green blue:(int)blue;
 
- (void)someMethod:(NSString *)str;
@end
```

[参考](https://blog.ibireme.com/2013/08/26/appledoc-guide/)


