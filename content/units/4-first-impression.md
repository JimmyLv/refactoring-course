# 任务 1：初识重构

不知道大家在这段视频里面看到了什么？如果你没有特别留意，我觉得你可以对比一下，在这些方面我和你的做法是否一样，有什么不同：

- 实现功能的方式。是先编写测试，还是先实现功能？
- 重命名函数的过程。这跟你眼里的重构有什么不同？
- 运行测试的频率。你的开发过程会不会编写自动化的单元测试？你在什么时候会运行它们？

相信大家已经看到，重构并不是多么神奇的东西，不一定得是整个架构的重写，不一定是一下替换掉整个类体系，也不一定是极致的算法优化等。重构其实涉及非常细小的调整，比如说，怎么安全地给函数重命名、给变量重命名、简化一些条件分支等。我们说的重构，除了非常细小，一般可以在 1、2 分钟之内完成一次重构之外，最重要的特征，其实是**安全**。

还记得前面提醒大家思考的问题吗？**你如何保证重构过程不改变已有的软件行为呢**？答案是自动化测试。

## 任务：重命名函数

有同学可能觉得，重命名函数，有什么难的？不就是全局搜索，然后替换一下？高级一点的同学会说，我可以用 IDE/编辑器提供的快捷键一键命名。

这里要给你分享的是更为保险的方法。

请大家按照视频里面，我把`parseTo(anotherUnit)`函数重命名为`as(anotherUnit)`的过程，试着重命名你的代码里的某一个函数。注意，我需要你完全遵循视频里所用的手法。

为了你学习方便，我把这个手法总结成这个过程：

1. 拷贝一份函数，将新函数命名为你想要的新名字
2. 运行测试
3. 让原函数调用新的函数
4. 运行测试
5. 查找原函数的引用点
6. 逐一替换每个引用点，使它们引用新的函数。每次替换之后运行测试
7. 所有替换完成后，删除旧函数，运行测试
8. 所有测试通过，完成替换

看出来我们所讲的重构手法的不同之处了吗？

- 我们重构的过程是小步前进的，每一小步之间都会运行测试。如果测试失败，则马上撤销修改，回到可工作的状态
- 重构的过程是个精华，它其实遵循一个十六字定理：旧的不变，新的创建；一步切换，旧的再见

这样做的优点在于，每一步你的测试都能通过。这也意味着，你随时都能停下来。如果一个重命名过为复杂，你马上可以将已经完成的部分重构提交，日后再完成未完的提交。更重要的是，你要掌握这个“**小步前进、运行测试**”的节奏。很多人对自己“一步替换”的能力有过于强大的自信，他们觉得很简单的事情，……。

## 重构基本功

### 旧的不变

旧的不变，是指对于你要重构的编程元素，先不去做直接的修改，也不直接删除它，而是先创建一份新的拷贝。这样做的好处是，原来的引用点和测试都依然会通过。

```javascript
export class Length {
  value
  unit

  ...

  // 旧的函数不变（不删除，不直接修改），所以原来的引用点和测试都应该能通过
  parseTo(targetUnit) {
    if (this.unit === 'yard') {
      if (targetUnit === 'foot') {
        return new Length(this.value * 3, targetUnit)
      }

      return new Length(this.value * 36, targetUnit)
    }

    if (this.unit === 'inch') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 36, targetUnit)
      }

      return new Length(this.value / 12, targetUnit)
    }

    if (this.unit === 'foot') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 3, targetUnit)
      }

      return new Length(this.value * 12, targetUnit)
    }

    return this
  }

  ...
}
```

### 新的创建

这一步有两个步骤：第一步是先拷贝一份`as(targetUnit)`函数，拷贝完成后要运行测试。因为新的函数并没有被任何地方调用，所以测试应该都能够完全通过；第二步是将旧函数的内部调用委托给新函数，然后运行测试。这一步相当关键，它建立了旧函数到新函数之间的桥梁，同时又保留了旧函数的对外接口不改变。如果你内部的委托调用替换是正确的，那么这一步运行测试也应该能够全部通过。

```javascript
export class Length {
  value
  unit

  ...

  // 旧的函数不变（不删除，不直接修改），所以原来的引用点和测试都应该能通过
  parseTo(targetUnit) {
    return this.as(targetUnit)
  }

  // 新的函数创建，并将旧的函数直接委托给新的函数
  as(targetUnit) {
    if (this.unit === 'yard') {
      if (targetUnit === 'foot') {
        return new Length(this.value * 3, targetUnit)
      }

      return new Length(this.value * 36, targetUnit)
    }

    if (this.unit === 'inch') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 36, targetUnit)
      }

      return new Length(this.value / 12, targetUnit)
    }

    if (this.unit === 'foot') {
      if (targetUnit === 'yard') {
        return new Length(this.value / 3, targetUnit)
      }

      return new Length(this.value * 12, targetUnit)
    }

    return this
  }

  ...
}
```

### 一步替换

上一步搭建好新旧函数之间的桥梁以后，我们就可以从容地开始引用点的替换工作了。我们在老的`parseTo(targetUnit)`上使用快捷键`cmd + B`查找它的所有引用点如下：

![find-references](./images/find-references.png)

此时我们找到第一处引用点，将它替换为直接调用`as(targetUnit)`，运行测试。测试应该能完全通过。

```javascript
  it('should 1 foot equals 12 inches', () => {
    ~~const result = new Length(1, 'foot').parseTo('inch')~~
    const result = new Length(1, 'foot').as('inch')

    expect(result.getValue()).toEqual(12)
    expect(result.getUnit()).toEqual('inch')
  })
```

对于剩余的引用点，我们如法炮制，直至把所有的`parseTo(targetUnit)`都替换成为`as(targetUnit)`。在这个过程中，我们随时都可以停下来，休息一下，或者提交一下，然后先去做别的事情，而我们的代码会一直保持可以工作的状态。这才是重构的真正意涵：小步前进，代码随时可工作。

### 旧的再见

完成所有的引用点替换后，IDE 随即也会提示我们：`parseTo(targetUnit)`已经没有引用点了。此时，我们就可以把这个函数安全地删除，运行一下测试。测试应该依然能通过，我们也完成了一次简单的重构。

看完你是不是觉得，重构原来非常的简单？这样的重构，我们工作的每一天，每个小时，每 10 分钟里都可以发生许多次。而且神奇的是，我们的代码将会一直保持可工作的状态，而不会使其他人有任何的感知，也不会改变系统的现有行为。但是，我们确实通过这样的方法使代码更可读了一点。

## 练习

上面介绍的做法中，“旧的不变，新的创建”其实是所有重构手法的精髓。只要你能为新旧代码创建起连接的桥梁，你就为“一步替换”提供了随时可停的机制，从而保证系统随时处于可运行状态，也使你能从容而安全地小步前进。

所以，我希望你能好好练习上面这个函数重命名的案例。我希望你严格地遵循这个步骤，并且体会第 1-4 步中间的含义。在这个练习中，你需要注意：

- 体会“频繁运行测试”这个动作。每次做完一点修改，就应该运行测试，确保系统依然可工作
- 严格遵循重构步骤
- 刻意练习，直到你能在 1 分钟之内严格遵循这个步骤完成重构

为免你忘记，我把这个重构的步骤再提醒你一次：

```markdown
1. 拷贝一份函数，将新函数命名为你想要的新名字
2. 运行测试
3. 让原函数调用新的函数
4. 运行测试
5. 查找原函数的引用点
6. 逐一替换每个引用点，使它们引用新的函数。每次替换之后运行测试
7. 所有替换完成后，删除旧函数，运行测试
8. 所有测试通过，完成替换
```
