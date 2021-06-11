# Angular / RxJS observables
Observables provide support for passing messages between parts of your application. It is used for event handling, asynchronous programming and so on.

Main difference between `Observable` and `Promise` is that `Promise` emits only single values during the period of time, while `Observable` could emits multiple values.
| `Observable`  | `Promise`  |
|---|---|
| Emit multiple values over a period of time.  | Emit a single value at a time.  |
| Are lazy: they’re not executed until we subscribe to them using the subscribe() method.  | Are not lazy: execute immediately after creation.  |
| Have subscriptions that are cancellable using the unsubscribe() method, which stops the listener from receiving further values.  |  Are not cancellable. |
| Deliver errors to the subscribers.  |  Push errors to the child promises. |

## Unsubscribing
Every observable must be unsubscribed to prevent memory leaks. The best place to do it is `ngOnDestroy` hook:

### Using `unsubscribe`
```
private _sub: Subscription;
ngOnInit() {
   this._sub = this.service.doSomethingAsync()
        .subscribe((result) => {
             this.result = result;
        });
}

/* ... */

ngOnDestroy() {
   this._sub.unsubscribe();  
}
```

### Using `take-` pipes
#### `takeUntil`
`takeUntil` is used when it is needed to get the results from specific Observable across all the lifetime of the component.
```
private _destroy$: Subject<void> = new Subject<void>();
ngOnInit() {
   this.service.doSomethingAsync()
        .pipe(takeUntil(this._destroy$))
        .subscribe((result) => {
             this.result = result;
        });
}

/* ... */

ngOnDestroy() {
   this._destroy$.next();
   this._destory$.complete();
}
```
#### `take`
`take` is used when it is needed to get the results specific amount of times, e.g. only first
```
ngOnInit() {
   this.service.doSomethingAsync()
        .pipe(take(1))
        .subscribe((result) => {
             this.result = result;
        });
}
```

#### `takeWhile`
`takeWhile` is used when it is needed to process the results until something happened

```
ngOnInit() {
   this.service.doSomethingAsync()
        .pipe(takeWhile((result) => result <= 4))
        .subscribe((result) => {
             this.result = result;
        });
}
```

### Exceptions
It is not needed to unsubscribe from `Observable` in the following cases:
1. Http request using `@angular/common/http`
```
   private data: RestData;
   constructor(private httpService: HttpService) { }
   
   ngOnInit() {
      this.httpService.get(environment.url).subscribe((data: RestData) => {
         this.data = data;
      });
   }
```
2. Using built-in state manager `dispatch` methods
2.1. NGRX
```
  constructor(private store: Store<{ count: number }>) {
    this.count$ = store.select('count');
  }
 
  increment() {
    this.store.dispatch(increment());
  }
```
2.2. NGXS
```
  constructor(private store: Store) {
    this.count$ = store.select(state => state.data.count);
  }
 
  increment() {
    this.store.dispatch(new Increment());
  }
```
3. Using observable in template with AsyncPipe
counter.component.ts:
```
  constructor(private store: Store) {
    this.count$ = store.select(state => state.data.count);
  }
```
counter.component.html:
```
<div>{{ count$ | async }}</div>
```

