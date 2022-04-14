vscode 代码中事件有关的类有这几个： `Event<T>`, `Emitter<T>`， `Listener<T>` 等。Emitter 字面上是发射器的意思，它负责发出一个事件，Listener 为监听器，它负责对事件的处理。下面我们以 `suggestModel.ts` 文件中对事件的使用来看看这几个类的关系。该文件中有三个事件发射器:

```ts
private readonly _onDidCancel = new Emitter<ICancelEvent>();
private readonly _onDidTrigger = new Emitter<ITriggerEvent>();
private readonly _onDidSuggest = new Emitter<ISuggestEvent>();
```

我们以`_onDidSuggest` 为例看看是怎么使用的。

## Emitter<T> 的定义

为了使类的定义看起来更加简洁，我省略了一些无关紧要的细节。

```ts
export class Emitter<T> {

	private readonly _options?: EmitterOptions;
	private _disposed: boolean = false;
	private _event?: Event<T>;
	private _deliveryQueue?: EventDeliveryQueue;
	protected _listeners?: LinkedList<Listener<T>>;

	constructor(options?: EmitterOptions) {
		this._options = options;
		this._deliveryQueue = this._options?.deliveryQueue;
	}

	dispose() {}

	/**
	 * For the public to allow to subscribe
	 * to events from this Emitter
	 */
	get event(): Event<T> {
		if (!this._event) {
			this._event = (callback: (e: T) => any, thisArgs?: any, disposables?: IDisposable[] | DisposableStore) => {
				const listener = new Listener(callback, thisArgs, stack);
				const removeListener = this._listeners.push(listener);
		}
		return this._event;
	}

	/**
	 * To be kept private to fire an event to
	 * subscribers
	 */
	fire(event: T): void {
		if (this._listeners) {
			// put all [listener,event]-pairs into delivery queue
			// then emit all event. an inner/nested event might be
			// the driver of this

			if (!this._deliveryQueue) {
				this._deliveryQueue = new PrivateEventDeliveryQueue();
			}

			for (let listener of this._listeners) {
				this._deliveryQueue.push(this, listener, event);
			}

			this._deliveryQueue.deliver();
		}
	}

	hasListeners(): boolean {
		if (!this._listeners) {
			return false;
		}
		return (!this._listeners.isEmpty());
	}
}
```

该类中较为重要的两个数据成员 `_deliveryQueue` 和 `_listeners`, 两个方法：`fire`， `event`。我们接下来结合源码来看看这些方法到底是怎么使用的。

## 在什么地方添加回调函数

既然是事件处理，那么必定需要添加回调函数。那么 Emitter 是在什么地方添加事件的回调函数呢？

在 `SuggestModel` 类中定了 `_onDidSuggest` 之后，立即定义了一个 `onDidSuggest` 变量，将 `_onDidSuggest` 的 event 成员赋值给 onDidSuggest 成员。

```ts
readonly onDidSuggest: Event<ISuggestEvent> = this._onDidSuggest.event;
```

event 成员是 Emitter 类的一个 getter 方法，访问 event 成员即可得到 getter 方法的返回值，该方法的返回值是 Emmiter 的私有成员 `_event`。 Event 类型定义如下：

```ts
export interface Event<T> {
	(listener: (e: T) => any, thisArgs?: any, disposables?: IDisposable[] | DisposableStore): IDisposable;
}
```

看起来是一个对象类型。而 event 方法返回的却是一个函数类型，这样没有问题吗？ 其实是可以的，因为 JavaScript中 函数也是对象，是可调用的对象。只要对象类型中存在调用签名，那么他就是一个可调用的对象，可以直接在其后面跟一对儿小括号进行调用。

这样我们可以将`onDidSuggest`看作一个函数，它是可以直接调用的。该函数的定义即为在 `event` 方法中返回的函数

```ts
(callback: (e: T) => any, thisArgs?: any, disposables?: IDisposable[] | DisposableStore) => {
	if (!this._listeners) {
		this._listeners = new LinkedList();
	}

	const listener = new Listener(callback, thisArgs, stack);
	const removeListener = this._listeners.push(listener);

	const result = listener.subscription.set(() => {
		if (removeMonitor) {
			removeMonitor();
		}
		if (!this._disposed) {
			removeListener();
			if (this._options && this._options.onLastListenerRemove) {
				const hasListeners = (this._listeners && !this._listeners.isEmpty());
				if (!hasListeners) {
					this._options.onLastListenerRemove(this);
				}
			}
		}
	});

	if (disposables instanceof DisposableStore) {
		disposables.add(result);
	} else if (Array.isArray(disposables)) {
		disposables.push(result);
	}

	return result;
};
```

同样，我省略了很多无关紧要的内容。如果 `_listeners` 为空，则初始化为一个空的列表用于存储所有的回调函数。 然后使用 `callback` 参数新创建一个 Listener 类型的对象，存放在刚才新建的列表中。这样就完成了回调函数的添加。 只要调用了 `onDidSuggest` 函数，那么传入的第一个参数 `callback` 就会被添加到 Emitter 类的 `_listeners` 列表中。

## 事件的发生以及对回调函数的调用

回调函数已经添加，那么事件何时发生，回调函数是怎么调用的呢？

Emitter 类中有一个 `fire` 方法，根据字面意思我们可以猜测出该方法应该负责事件的发出以及对回调函数的调用。该方法的定义在上面已经列出，为了方便查看，此处再次列出：

```ts
/**
 * To be kept private to fire an event to
 * subscribers
 */
fire(event: T): void {
	if (this._listeners) {
		// put all [listener,event]-pairs into delivery queue
		// then emit all event. an inner/nested event might be
		// the driver of this
		if (!this._deliveryQueue) {
			this._deliveryQueue = new PrivateEventDeliveryQueue();
		}
		for (let listener of this._listeners) {
			this._deliveryQueue.push(this, listener, event);
		}
		// start/stop performance insight collection
		this._perfMon?.start(this._deliveryQueue.size);
		this._deliveryQueue.deliver();
		this._perfMon?.stop();
	}
}
```

该方法中首先创建一个 `PrivateEventDeliveryQueue`, 它的底层实现是一个 `LinkedList`，用于保存所有的 listener 与 event。每一对儿 listener 和 event 作为 PrivateEventDeliveryQueue 的一个元素存储起来。 然后调用了 `PrivateEventDeliveryQueue` 的 `deliver` 方法。 `deliver` 方法定义如下：

```ts
deliver(): void {
	while (this._queue.size > 0) {
		const element = this._queue.shift()!;
		try {
			element.listener.invoke(element.event);
		} catch (e) {
			onUnexpectedError(e);
		}
	}
}
```

`deliver` 方法遍历了 `PrivateEventDeliveryQueue` 的每一个元素，取出其中的 listener， 类型为 `Listener<T>`，调用其 `invoke` 方法，`Listener` 定义如下：

```ts
class Listener<T> {

	readonly subscription = new SafeDisposable();

	constructor(
		readonly callback: (e: T) => void,
		readonly callbackThis: any | undefined,
		readonly stack: Stacktrace | undefined
	) { }

	invoke(e: T) {
		this.callback.call(this.callbackThis, e);
	}
}
```

前面我们讲过 Listener 中的 callback 即为回调函数，invoke 方法直接通过函数的 `call` 方法调用了 `callback` 函数。至此，回调函数被调用。以上就是事件的处理过程。