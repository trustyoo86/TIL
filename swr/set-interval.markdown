# useSWR refreshInterval 중지하기

## 개요

useSWR에서 `refreshInterval` option을 사용시, 지속적으로 interval마다 swr의 fetcher를 호출한다.

status가 변경되어, interval을 중지해야 하는 경우, useState를 이용하여 interval을 중지시킬 수 있다.

## Example

- useSWR을 사용한 Hook code

```typescript

// useInterval.tsx
function useInterval() {
  const [interval, setInterval] = useState<number>(1 * 1000);
  
  const { data } = useSWR('SWR/INTERVAL', fetcher, {
    // interval 사용
    refreshInterval: interval,
  });

  useEffect(() => {
    if (!data) {
      // interval을 0으로 수정하여, reefreshInterval을 0으로 멈추게 만든다.
      setInterval(0);
    }
  }, [data]);
}

```
