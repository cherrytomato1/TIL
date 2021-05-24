# Vuex state computed

vuex의 state가 변경되었을 때, 특정 메서드 실행 및 값 할당을 위해서는 computed 및 watch를 통해 구현해야한다.

computed로 함수 객체를 생성하여 값을 리턴시킨다. computed는 리턴값을 가져야하며 콜백을 실행시킬 수 없으므로 watch로 해당 함수 객체의 값 변화를 감지한다.

우리는 `this.crateNativation()` 메서드로 파라미터가 변경되었을 때 네비게이션을 만들어야한다. 따라서 아래와 같은 코드를 작성했다.

```
    computed: {
        ...mapGetters(["articles", "pageParams"]),
        changedParams: function () {
            return { page: this.$store.getters.pageParams.page };
        },
    },
    watch: {
        changedParams(val) {
            console.log("watch init");
            this.page = val.page;
            this.createNavigation();
            console.log("watch end");
        },
    },
```

`...mapGetters` 로 각 getters를 매핑하고, 그중 `pageParams.page` 가 변경되었을 때의 `computed` 를 따로 선언하여 `page` 값을 담은 json 객체를 반환한다.

`watch` 에서 json 객체를 파라미터로 받아 `data` 의 `page` 를 변경시키고 그에 맞는 값으로 네비게이션을 생성한다.
