# 异步执行测试

由于服务以异步方式操作，因此异步地执行服务的整个单元测试可能是有用的。 这可以加快完成整个测试周期所需的总时间，因为特定的单元测试不会阻止其他测试的执行。 我们可以设置我们的单元测试来返回一个promise，它将根据测试的结果来返回成功或失败。

```
describe('verify search', () => {
  it('searches for the correct term',
    fakeAsync(inject([SearchWiki, MockBackend], (searchWiki, mockBackend) => {
        return new Promise((pass, fail) => {
          ...
        });
    })));
});
```

不是只使用`inject，`我们使用`fakeAsync`来包装它并完成依赖并在异步过程中执行测试。 使用`fakeAsync`需要我们返回一个Promise，我们通过调用`pass`或者`fail`来解决我们的测试的*competition* ，这取决于我们测试的结果。