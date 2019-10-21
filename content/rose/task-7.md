# 任务 7：添加结构

打扫完周边的战场，`update()`方法虽然仍然复杂，但相比一开始，它已经整洁了一些，终于是时候向它发起战斗的号角。这个时候，我们仍然建议你从大的结构入手，而非马上深入它的细节。这个`update()`方法做了很多事情，充斥着很多细节，许多细节我们现在也是一知半解，但把所有层级的嵌套收起来，我们大概会看到下面的结构，就是三个并列的条件判断：

```javascript
export class Item {
  ...

  update() {
    if (!this.isAgedBrie() && !this.isBackstagePass()) {
      if (this.quality > 0) {
        if (!this.isSulfuras()) {
          this.quality = this.quality - 1;
        }
      }
    } else {
      if (this.quality < 50) {
        this.quality = this.quality + 1;
        if (this.isBackstagePass()) {
          if (this.sellIn < 11) {
            if (this.quality < 50) {
              this.quality = this.quality + 1;
            }
          }
          if (this.sellIn < 6) {
            if (this.quality < 50) {
              this.quality = this.quality + 1;
            }
          }
        }
      }
    }
    if (!this.isSulfuras()) {
      this.sellIn = this.sellIn - 1;
    }
    if (this.sellIn < 0) {
      if (!this.isAgedBrie()) {
        if (!this.isBackstagePass()) {
          if (this.quality > 0) {
            if (!this.isSulfuras()) {
              this.quality = this.quality - 1;
            }
          }
        } else {
          this.quality = 0;
        }
      } else {
        if (this.quality < 50) {
          this.quality = this.quality + 1;
        }
      }
    }
  }

  ...
}
```

现在，你的任务是通读一下这三段代码，大概理解它们做的事情，然后把每一段提炼成一个方法，用它做的事情给它命名。然后回过头看看，代码的含义是不是更加清晰了？

## 你的任务

1. 提炼`update()`函数中的三段分支逻辑成为函数，用它们做的事情给方法命名
2. 要求小步前进，每一步修改之后马上运行所有测试，确保软件行为不变

## 思考

1. 你有没有其他整理程序结构的思路？
2. 尝试体会重构使代码结构自然“浮现”的过程
