#Maps are Iterable
Immutable.js中的Maps是可迭代的，这意味着你可以在Maps中映射，过滤，缩小等每个键值对。
```ts
let features = Immutable.Map<string, boolean>({
    'send-links': true,
    'send-files': true,
    'local-storage': true,
    'mirror-notifications': false,
    'api-access': false
});

let myFeatures = features.reduce((providedFeatures, provided, feature) => {
    if(provided)
        providedFeatures.push(feature);

  return providedFeatures;
}, []);

console.log(myFeatures); // [ 'send-links', 'send-files', 'local-storage' ]
```
```ts
const mapMap = Immutable.Map({ a: 0, b: 1, c: 2 });
mapMap.map(i => i * 30);

const mapFilter = Immutable.Map({ a: 0, b: 1, c: 2 });

mapFilter.filter(i => i % 2);

const mapReduce = Immutable.Map({ a: 10, b: 20, c: 30 });

mapReduce.reduce((acc, i) => acc + i, 0);
```