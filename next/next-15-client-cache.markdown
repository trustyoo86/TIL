# Next.js 15 server cache

## server cache

- 서버 캐시는 이미 fetch를 오버라이딩 하여 완료 되어 있음.
- Server cache is already maked overriding native fetch method.

1. API routes에서 캐시 설정 변경
- API routes(/app/api) 에서 revalidate 옵션 조정
- Adjust revalidate option in api routes (/app/api)
```javascript
export const GET = async () => {
  return new Response(JSON.stringify({ message: "Hello" }), {
    headers: {
      "Cache-Control": "no-store", // 캐시 비활성화
    },
  });
};
```
* cache-control: no-store => 캐시 없이 매 요청마다 새로운 데이터를 가져옴. (get new data without cache)
* cache-control: max-age=10 => 10초동안 캐싱 후 새로운 데이터를 가져옴. (Caching until 10 seconds, after 10 seconds get new data)

2. 서버 컴포넌트에서 fetch 캐시 비활성화
- 서버 컴포넌트에서 데이터를 가져올때, fetch 함수의 cache 옵션 조정
- Adjust cache option in fetch method when get data from server component
```tsx
const getData = async () => {
  const res = await fetch("https://api.example.com/data", {
    cache: "no-store", // 캐시 비활성화
  });
  return res.json();
};

export default async function Page() {
  const data = await getData();
  return <div>{JSON.stringify(data)}</div>;
}
```
* cache: 'force-cache' => 기본 설정 (30초 캐싱) -> default option (caching until 30 seconds)

3. revalidate 옵션 사용
- 서버컴포넌트에서 ISR (Incremental Static Regeneration) 사용
- Use ISR in server component
```tsx
const getData = async () => {
  const res = await fetch("https://api.example.com/data", {
    next: { revalidate: 5 }, // 5초마다 새로운 데이터 요청
  });
  return res.json();
};
```
* revalidate: 5 -> 5초마다 데이터를 갱신함. Refresh data every 5 seconds
* revalidate: 0 => ISR없이 SSG (Static Site Generation) 방식으로 동작 -> Operate SSG without ISR

4. Dynamic route에서 캐시 비활성화 (dynamic: 'force-dynamic') - Disable cache in dynamic route
- 동적 페이지의 경우 dynamic 설정을 통해 캐싱을 관리 -> Manage cache using dynamic option when dynamic page

```tsx
export const dynamic = "force-dynamic"; // SSR로 동작 (캐시 사용 X)

export default async function Page() {
  const res = await fetch("https://api.example.com/data");
  const data = await res.json();
  return <div>{JSON.stringify(data)}</div>;
}
```
* dynamic: 'force-dynamic' -> SSR 방식으로 동작하며 캐시 없이 요청 -> Operate like SSR without cache
* dynamic: 'auto' -> default
* dynamic: 'force-static' -> Operate like SSG

5. 환경 변수로 캐시 설정 -> Set cache using env files.
* Add property to .env.local
```
NEXT_PUBLIC_FETCH_CACHE=no-store
```

* Apply server component

```tsx
const getData = async () => {
  const res = await fetch("https://api.example.com/data", {
    cache: process.env.NEXT_PUBLIC_FETCH_CACHE,
  });
  return res.json();
};
```

## Conclusion
* Disable cache absolutely:
- cache: 'no-store'
- dynamic: 'force-dynamic'
- cache-control: 'no-store'
* Reduce cache remain time
- next: { revalidate: 5 }
* Manage by env files
- NEXT_PUBLIC_FETCH_CACHE=no-store


































