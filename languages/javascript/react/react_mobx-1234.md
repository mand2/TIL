### mobx  비동기작업 방법 1234

**1** `action`  
action 안에 연계되는 action을 생성할 때엔 사용안하는 게 낫다. 아래의 #2 를 하는 걸 추천. `action` 안에 `.then`에서 상태변화가 일어나면 다른 action으로 감싸야 함.

* 단점:  인라인 action 들의 단점은 <u>TypeScript 에서 이러한 형식을 허용하지 않음</u>.

```react
@action
fetchProject() {
    this.githubProjects = []
    this.state = 'pending'

    fetchGithubProjectsSomehow().then(
        // 또 다른 action을 생성해줍니다.
        action('fetchSuccess', projects => {
            const filteredProjects = somePreprocessing(projects)
            this.githubProjects = filteredProjects
            this.state = 'done'
        }),
        action('fetchError', error => {
            this.state = 'error'
        })
    )
}
```



**2** `runInAction`  
상태변화를 일으키는 콜백 action만 따로 적용할 때 사용. action 들을 남용하지 않으면서 오히려 전체의 프로세스에서 상태변화를 할 수 있게 만들어 준다.

```react
@action
fetchProjects() {
    fetchProjects() {
		...
        fetchGithubProjectsSomehow().then(
            projects => {
                const filteredProjects = somePreprocessing(projects)
                runInActions(() => {
                    this.githubProjects = filteredProjects
                    this.state = 'done'
                })
            },
            error => {
                runInAction(() => {
                    this.state = 'error'
                })
            }
        )
    } ...
```



**3** `await` / `async`  
결과적으로 `@action` 는 첫번째 `await` 전 까지의 블록만 적용됩니다. 그리고나서 각각 `await` 이 실행되고, 각 `await` 의 상태변경 코드들은 또 다른 action들로 감싸져야 합니다. 

```react
@action
async fetchProjects() {
    this.githubProjects = []
    this.state = 'pending'
    try {
      const projects = await fetchGithubProjectsSomehow()
      const filteredProjecst = somePreprocessing(projects)
      // await 이후에 상태를 변화시키려면 또 다시 action이 필요합니다.
      runInAction(() => {
        this.state = 'done'
        this.githubProjects = filteredProjects
      })
```

`@action` 어노테이션 + fetchProjects() 앞에 **`async`** 붙이고, `fetchGithubProjectsSomehow()` 앞에 **`await`**. 이말은 fetchGithubProjectsSomehow() 를 통해 상태변화가 되면 렌더링 되게끔!



**4** `flow`  

`flow` 는 더 좋은 사용 방법이며,  `async` 나 `await`과 같은 원리이다.  비동기 작업이 필요한 부분에 수동으로 감싸지 않아도 됨. **대신 `function * `을 사용 + await 대신 `yield` 사용.**

**flow는 취소 가능**!! cancle() 실행하면 해당 flow만 즉시 중단되며 `FLOW_CANCLED` 반환. 다른 flow들은 중지X.

```react
fetchProjcets = flow(function * () {
    this.githubProjects = []
    this.state = 'pending'
    try {
        // await 대신에 yield 를 사용합니다.
        const projects = yield fetchGithubProjectsSomehow() 
        const filteredProjects = somePreprocessing(projects)

        // 비동기 작업이 필요한 부분을 수동으로 감싸지 않아도 됩니다.
        this.state = 'done'
        this.githubProjects = filteredProjects
    } catch (error) {
        this.state = 'error'
    }
})
```

