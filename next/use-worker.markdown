# Next.js comlink web worker 사용하기

## 개요

원래 브라우저상에서의 렌더링 엔진 및 기타 비즈니스 로직에 대한 처리 로직은 Single Thread로 처리된다. (event loop에 의해 실행됨)

즉, 브라우저 엔진에서 코드를 해석하는 과정에서 setInterval, async 비동기 처리등은 별도의 event callback queue에 저장되어, 일반 로직이 수행되고 event loop에 의해 queue에서 처리해야 하는 로직을 수행하게 된다.

이경우, setTimeout, setInterval의 경우 일반적인 로직 수행시 방해가 되는 경우가 있는데, 이를 개선하기 위한 한가지 방법으로 `web worker` 를 사용한다.

web worker는 브라우저 엔진 외에 별도의 thread를 두어, background worker에서 로직을 수행하여 처리하도록 하는 로직으로, window 상에서 처리할 수 있는 로직은 한정되어 있어 async 등의 로직을 interval을 통해 수행하기 좋다. 다만, web worker가 현재 (2021.05.06) 기준으로는 worker-plugin을 통한 처리가 불가능하다.

web worker를 사용하기 위해 [Comlink](https://www.npmjs.com/package/comlink)를 사용하는 예제이다.

## Example

### 1) npm 설치 (comlink, worker-plugin)

comlink 및 web worker를 사용하기 위해 각 플러그인을 설치한다.

```bash
npm i comlink worker-plugin
```

### 2) next.config.js 설정

next.config.js 를 이용하여 webpack 설정에 주입한다.

```javascript
// next.config.js
module.exports = {
  webpack: (config, { isServer }) => {
    // 2번째 인자에는 isServer 판단 가능
    if (!isServer) {
      config.plugins.push(
        new WorkerPlugin({
          globalObject: 'self',
        }),
      );
    }
  },
};

```

### 3) web worker로직 만들기

```typescript

```


```typescript
import * as Comlink from 'comlink';
import { WebWorkerApi } from './WebWorker';

function useWorker() {
  const workerRef = useRef<Worker>();
  const workerApiRef = useRef<Comlink.Remote<WebWorkerApi>>();

  const getWorkerProxy = Comlink.proxy((data) => {
    console.log(JSON.parse(data));
  });

  const sync = async () => {
    await workerApiRef.current?.subscribe(getWorkerProxy);
  };

  useEffect(() => {
    workerRef.current = new Worker('./WebWorker', {
      type: 'module',
    });
    workerRef.current = Comlink.wrap<WorkerApi>(workerRef.current);
    sync();

    // 주의!! component unmount시에는 worker를 꼭 해제해 주어야 함.
    () => {
      workerRef.current?.terminate();
    };
  }, []);
}
```
