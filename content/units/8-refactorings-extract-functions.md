# 任务 4：提炼函数

现在，你已经学会了为函数改名、抽取字符串到常量，虽然这离更有用的重构还有一段距离，但是你已经迈出了十分坚实的一步。对于初识重构这个作用来讲，其实第一课的内容应该差不多了，但是这一节我还想再跟你分享一招。传说中，学会了这招重构，就可以超越 80% 以上的工程师。这个手法在工作中可以说是非常常见的一个手法了，它往往还能促成更深远的结构调整，使你更容易地看清代码结构，从而更轻松地做出进一步的优化。

那么这个手法叫什么名字呢？我相信本节的标题早已出卖了我。这一次，我依然先不讲这个手法的实施步骤，看看你能不能按照前面的重构手法依样画出来。

请看下面的代码：

```javascript
import { Units } from "./units";

export class Length {
  value;
  unit;

  constructor(value, unit) {
    this.value = value;
    this.unit = unit;
  }

  getValue() {
    return this.value;
  }

  getUnit() {
    return this.unit;
  }

  as(targetUnit) {
    if (this.unit === Units.YARD) {
      if (targetUnit === Units.FOOT) {
        return new Length(this.value * 3, targetUnit);
      }

      return new Length(this.value * 36, targetUnit);
    }

    if (this.unit === Units.INCH) {
      if (targetUnit === Units.YARD) {
        return new Length(this.value / 36, targetUnit);
      }

      return new Length(this.value / 12, targetUnit);
    }

    if (this.unit === Units.FOOT) {
      if (targetUnit === Units.YARD) {
        return new Length(this.value / 3, targetUnit);
      }

      return new Length(this.value * 12, targetUnit);
    }

    return this;
  }
}
```

光看这份代码，不容易看出我们应该提炼一个什么函数，因此，我们还是要从“重复代码”这个坏味道入手。上一步我们已经将长度单位抽取到`Units`常量中，代码结构又整齐了一些，各种干扰视线的字符串常量已经不见了，但是上面这份代码，依然有重复的地方。请你花 3 分钟的时间阅读一下，看看这里面还有些什么重复？

【此处可配一张图分割空间和时间】

看清楚了吗？什么地方还有重复呢？

在上面各个`if`分支里头，每个分支都写了一行`return new Length(..., targetUnit)`语句，用以返回一个新的以`targetUnit`为单位的`Length`对象。其实，这每个分支都是要创建一个`Length`对象，都是要以`targetUnit`为新的单位，真正的区别在于转换单位后的长度值`value`不同。我们大可不必在每个分支中重复创建过程，而只需要决定新的`value`值，最后由一个语句统一创建过程即可。说到这里，你是不是已经知道我们应该怎么重构了？

## 你的任务

1. 思考一下，如何应用“十六字心法”设定重构步骤，消除上述重复的`return new Length(..., unit)`创建过程
2. 自己动手，严格遵循你思考过的重构步骤，完成这次重构
3. 观看下面的视频，找到自己做得好的地方，和不够严谨的地方
4. 重新练习 3 遍以上，直到你能在 5 分钟之内，严格遵循重构手法完成这项任务
5. 请你自己总结一下，“提炼函数”手法的具体步骤。写下来，可以发到群里跟大家交流

## 提示

如果你实在想不到，怎么样消除这个重复的`new Length()`语句，那么我可以给你一点点提示：

```javascript
import { Units } from "./units";

export class Length {
  ...

  as(targetUnit) {
    let value = this.value
    if (this.unit === Units.YARD) { ... }

    if (this.unit === Units.INCH) { ... }

    if (this.unit === Units.FOOT) {
      if (targetUnit === Units.YARD) {
        return new Length(this.value / 3, targetUnit);
      }

      value = this.value * 12
      ~~return new Length(this.value * 12, targetUnit);~~
    }

    return new Length(value, targetUnit);
  }
}
```

你可以像上面这样，把`new Length()`的创建挪到函数的最后，将变化的`value`值抽取成一个变量，再依次把每个分支改为仅仅对`value`进行一次赋值（因为这是每个分支真正在处理的不同点）。

对每个分支都完成处理后，你就会发现，这一坨`if`套`if`的分支，干的就是给`value`赋值这件事情。于是，把这一全套`if`分支提炼成一个函数也就顺理成章了，甚至于函数的命名本身都已经呼之欲出。好了，相信你已经按捺不住激动的心情了，赶紧去实践一下吧。注意，一定要记得遵循我们的十六字武功心法。

## 我的 Coding 演示

## 参考资料

- 《重构 2》第 6 章第 1 节：提炼函数（Extract Method）
