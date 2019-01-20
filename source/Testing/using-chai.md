# 使用Chai

Chai是一个断言库，有一些美味的语法糖，可以与任何其他测试框架配对。 它允许我们以TDD（测试驱动开发）风格或BDD（行为驱动开发）风格编写测试。 我们已经知道什么是TDD（阅读介绍！），那么什么是BDD？ BDD是使用TDD和自然语言结构（类似英语的句子）来表达单元测试的行为和结果的组合。 Jasmine已经使用了TDD风格，因此我们将使用Chai作为其BDD接口，主要是通过使用`should`和`expect` 。

```
describe('Testing math', () => {
  it('multiplying should work', () => {
    let testMe = 16;

    // Using the expect interface
    chai.expect(testMe).to.be.a('number');
    chai.expect(testMe).to.equal(16);

    // Using the should interface
    chai.should();
    testMe.should.be.a('number');
    testMe.should.equal(16);
  });
});
```

`expect`和`should`接口都利用链接来构造类似英语的句子来描述测试。 一旦你决定了一个风格，你应该保持这种风格为所有其他测试。 每种风格都有自己独特的语法; 请参阅该 [特定API的文档](http://chaijs.com/guide/styles/) 。