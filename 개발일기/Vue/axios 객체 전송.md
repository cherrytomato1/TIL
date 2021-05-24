# axios 객체 전송

axios에서 객체를 보낼때 보낼때 그냥 보내면 안되고 객체로 {} 감싸줘야함

```
        let params = context.state.pageParams;
        http.get("/board",  params )

```

-   스프링에서 받지 못하는 경우 1

```
        http.get("/board",  {context.state.pageParams} )
```

-   객체 내용을 감쌀 때 . 사용하면 컴파일 에러

```
            let params = context.state.pageParams;
            http.get("/board", { params })
```

-   감싸줘서 던지기

## 이유

자바스크립트의 object를 그대로 던져주면 json 객체로 확인되지 않아 파라미터 값으로 스프링 컨테이너가 인식하지 않습니다.
